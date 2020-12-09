Without custom flags at installation, snaps run confined within a restrictive security sandbox.

The security policies and store policies work together to allow developers to quickly update their applications and to provide safety to end users.

This document describes the sandbox and how to configure and work with the security policies for snaps.

-   [Security policy walkthrough](#heading--walkthrough)
-   [AppArmor, Seccomp and device permissions](#heading--permissions)
-   [Interface security policies](#heading--interface)

For help resolving issues that may arise from a snap’s security policy, see [Debugging snaps](https://snapcraft.io/docs/debug-snaps).

```{=html}
<h2 id='heading--walkthrough'>Security policy walkthrough</h2>
```
Application developers should not need to know about, or understand, the low-level implementation details of how a security policy is enforced.

Each command declared with the `apps` [snap metadata](https://snapcraft.io/docs/snap-format#heading--snapyaml) is tracked by the system assigning a security label to the command.

This security label takes the form of `snap.<snap>.<app>` where `<snap>` is the name of the snap, and `<app>` is the application name.

For example, the following is an app declaration from `snap.yaml`:

``` yaml
name: foo
version: 1.0
apps:
  bar:
    command: bar
  baz:
    command: baz
    daemon: simple
    plugs: [network]
```

-   the security label for `bar` is `snap.foo.bar` . It uses only the default policy
-   the security label for `baz` is `snap.foo.baz` . It uses the `default` policy plus the `network` interface security policy as provided by the core snap

This security label is used throughout the system, including during the process confinement phase when running the application.

Under the hood, the application runner does the following:

1.  Sets up various environment variables:

    -   `HOME`: set to `SNAP_USER_DATA` for all commands
    -   `SNAP`: read-only install directory
    -   `SNAP_ARCH`: the architecture of device (eg, amd64, arm64, armhf, i386, etc)
    -   `SNAP_DATA`: writable area for a particular revision of the snap
    -   `SNAP_COMMON`: writable area common across all revisions of the snap
    -   `SNAP_LIBRARY_PATH`: additional directories which should be added to `LD_LIBRARY_PATH`
    -   `SNAP_NAME`: snap name
    -   `SNAP_INSTANCE_NAME`: snap instance name incl. instance key if one is set (snapd 2.36+)
    -   `SNAP_INSTANCE_KEY`: instance key if any (snapd 2.36+)
    -   `SNAP_REVISION`: store revision of the snap
    -   `SNAP_USER_DATA`: per-user writable area for a particular revision of the snap
    -   `SNAP_USER_COMMON`: per-user writable area common across all revisions of the snap
    -   `SNAP_VERSION`: snap version (from `snap.yaml`)

2.  When hardware is assigned to the snap, a device cgroup is set up with default devices (eg, /dev/null, /dev/urandom, etc) and any devices that are assigned to this snap. Hardware is assigned with *interface* connections.

3.  Sets up a private mount namespace shared across all the commands in the snap.

4.  Sets up a private `/tmp` directory using a per-snap private mount namespace and mounting a per-snap directory on /tmp.

5.  Sets up a new instance of *devpts* per command.

6.  Sets up the seccomp filter for the command.

7.  Executes the command under the command-specific AppArmor profile under a default nice value.

```{=html}
<h2 id='heading--permissions'>AppArmor, Seccomp and device permissions</h2>
```
When a snap is installed, it's metadata is examined and used to derive **AppArmor** profiles, **Seccomp** filters and device **cgroup** rules, alongside **traditional permissions**. This combination provides strong application confinement and isolation:

-   ```{=html}
    <h3 id='heading--apparmor'>AppArmor</h3>
    ```
    AppArmor profiles are generated for each command. These have the appropriate security label and command-specific AppArmor rules to mediate file access, application execution, Linux capabilities, mount, ptrace, IPC, signals, coarse-grained networking.

    As already mentioned, each command runs under an app-specific default policy that may be extended through declared interfaces which are expressed in the metadata as `plugs` and `slots`. AppArmor policy violations in strict mode snaps will be denied access, and typically have errno set to `EACCES`. The violation will be logged..

-   ```{=html}
    <h3 id='heading--seccomp'>Seccomp</h3>
    ```
    A seccomp filter is generated for each command in a snap to run under, enabling whitelist syscall filtering, which can then be extended through declared interfaces expressed in the metadata as `plugs` and `slots`.

    Processes with seccomp policy violations will be denied access to the system call with errno set to `EPERM` (snapd releases prior to 2.32 receive `SIGSYS`) and the violation is logged.

-   ```{=html}
    <h3 id='heading--cgroup'>Device cgroup</h3>
    ```
    udev rules are generated for each command to tag devices so they may be added/removed to the command's device cgroup. By default, however, no devices are tagged and the device cgroup is not used, with AppArmor used to mediate access.

    A device cgroup may be used in addition to AppArmor when a dependent interface is declared, as expressed through `plugs` and `slots` in the metadata.

    Processes accessing devices not in the snap-specific device cgroup will be denied access with errno set to `EPERM`. Access violations are typically not logged.

-   ```{=html}
    <h3 id='heading--permissions'>Traditional permissions</h3>
    ```
    Traditional file permissions (owner, group, file ACLs and others) are also enforced with snaps.

    Processes trying to access resources which the traditional file permissions do not allow are denied access with errno typically set to `EACCES` (see the man page for the operation for specifics). Access violations are typically not logged.

Consequently, all snaps run under a default security policy which can be extended through the use of [interfaces](https://snapcraft.io/docs/snapcraft-interfaces).

```{=html}
<h3 id='heading--interface'>Interface security policies</h3>
```
Interfaces are implemented as plugs and slots. A plug in one snap may connect to a slot in another and this provides access to the resources required.

The *snap connections* command can be used to see available interfaces alongside their slots and plugs.

``` bash
$ snap connections
Interface           Plug                                Slot               Notes
home                wormhole:home                       :home              -
log-observe         gnome-logs:log-observe              :log-observe       -
mount-observe       gnome-system-monitor:mount-observe  :mount-observe     -
[...]
```

In the above example output, the `gnome-logs` snap is connected to the `log-observe` interface, which means the security policy from `log-observe` has been added to `gnome-logs`.

Interfaces can be declared either per-snap or per-command: - if declared per-snap, all the commands within the snap have the interface security policy added to the command’s security policy when the interface is connected - if declared per-command, only the commands within the snap that declare use of the interface have the interface security policy added to them

An interface may either auto-connect upon install, or require the user to manually connect them. Interface connections and disconnections are performed via the `snap connect` and `snap disconnect` commands. See [interfaces](https://snapcraft.io/docs/interface-management) for details.
