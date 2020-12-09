Ubuntu Core supports a set of system-wide options that allow you to customise your snap and core environment.

As with [Configuration in snaps](/t/configuration-in-snaps/510), these options are changed with the `set` and `get` commands, but with a target of *system* instead of a specific snap:

``` bash
$ snap set system some.option="some value"
$ snap get system some.option
```

Configuration options can be unset by either passing their names to the unset command or by adding an exclamation mark (!) to the end of an option name: (from *snapd 2.41+*):

``` bash
$ snap unset system some.option
$ # or
$ snap set system some.option!
```

Typing `snap get system` outputs a top-level view of system-wide option categories which can be added as arguments to view their contents:

``` bash
$ snap get system
Key           Value
experimental  {...}
refresh       {...}
seed          {...}
$ snap get system experimental
Key                   Value
experimental.hotplug  true
experimental.layouts  true
```

------------------------------------------------------------------------

| Supported options                                                        |                                                       |
|--------------------------------------------------------------------------|-------------------------------------------------------|
| [pi-config](#heading--pi-config)                                         | [proxy.{http,https,ftp}](#heading--proxy)             |
| [refresh](#heading--refresh)                                             | [resilience.vitality-hint](#heading-resiliance)       |
| [service.console-conf.disable](#heading--console)                        | [service.ssh.disable](#heading--ssh)                  |
| [snapshots.automatic.retention](#heading--snapshots-automatic-retention) | [store-certs](#heading--store-certs)                  |
| [system.disable-backlight](#heading--backlight)                          | [system.power-key-action](#heading--power-key-action) |
| [system.system.timezone](#heading--timezone)                             |                                                       |

------------------------------------------------------------------------

```{=html}
<h2 id="heading--pi-config">pi-config</h2>
```
On a Raspberry Pi, the following options set corresponding values in the *config.txt* system configuration file:

-   pi-config.disable-overscan
-   pi-config.framebuffer-width
-   pi-config.framebuffer-height
-   pi-config.framebuffer-depth
-   pi-config.framebuffer-ignore_alpha
-   pi-config.overscan-left
-   pi-config.overscan-right
-   pi-config.overscan-top
-   pi-config.overscan-bottom
-   pi-config.overscan-scale
-   pi-config.display-rotate
-   pi-config.hdmi-group
-   pi-config.hdmi-mode
-   pi-config.hdmi-drive
-   pi-config.avoid-warnings
-   pi-config.gpu-mem-256
-   pi-config.gpu-mem-512
-   pi-config.gpu-mem

Further details on the above, see the [official Raspberry Pi documentation](https://www.raspberrypi.org/documentation/configuration/config-txt/).

```{=html}
<h2 id="heading--proxy">proxy.{http,https,ftp}</h2>
```
These options may be set to change the proxies to be used by the system when communicating with external sites that speak the respective protocols.

Available since snapd 2.28.

```{=html}
<h2 id="heading--refresh">refresh</h2>
```
There are four system-wide options that are used to manage how updates are handed:

-   **refresh.timer**: defines the refresh frequency and schedule
-   **refresh.hold**: delays the next refresh until the defined time and date
-   **refresh.metered**: pauses refresh updates when network connection is metered
-   **refresh.retain**: sets how many revisions of a snap are stored on the system

See [Controlling updates](/t/managing-updates/7022#heading--controlling-updates) for further details on how the above options are used.

```{=html}
<h2 id='heading--resiliance'>resilience.vitality-hint</h2>
```
This option adjusts the Linux kernel's out-of-memory ([OOM](https://www.kernel.org/doc/gorman/html/understand/understand016.html)) killer behaviour for specific snap services.

By default, all snap services have the same value for systemd's `OOMScoreAdjust`. By passing a list of snaps ordered by decreasing importance to the `resilience.vitality-hint` system option, the order is respected if snap processes are killed in low memory situations.

The list of snaps need to be as string containing comma separated snap instance names in decreasing order of importance, such as:

``` bash
snapA,snapB,snapC
```

In the above example, services inside `snapA` are the **least likely** to be killed in *out of memory* situations, followed by services in `snapB`, services in `snapC`, and then the services in all the other snaps not referenced by the `vitality-hint` option.

[note]
:information_source: Snaps added to `resilience.vitality-hint` are still *more likely* to be killed than the snap daemon, snapd, itself.
[/note]

Available since snapd 2.46.

```{=html}
<h2 id="heading--console">service.console-conf.disable</h2>
```
May be set to *true* to disable the console-conf system configuration wizard that is launched by default when booting an initialised Ubuntu Core image.

Available since snapd 2.46.

```{=html}
<h2 id="heading--ssh">service.ssh.disable</h2>
```
May be set to *true* to disable the SSH service at startup.

Available since snapd 2.22.

```{=html}
<h2 id='heading--snapshots-automatic-retention'>snapshots.automatic.retention</h2>
```
[Automatic snapshot](/t/snapshots/9468) retention time is configured with the `snapshots.automatic.retention` system option. The default value is 31 days, and the value needs to be greater than 24 hours:

``` bash
$ snap set system snapshots.automatic.retention=30h
```

To disable automatic snapshots, set the retention time to `no`:

``` bash
$ snap set system snapshots.automatic.retention=no
```

> ⓘ Disabling automatic snapshots will *not* affect preexisting, automatically generated snapshots, but only those generated by subsequent snap removals.

Automatic snapshots require snap version *2.39+*.

```{=html}
<h2 id='heading--store-certs'>store-certs</h2>
```
A custom SSL certificate can be added to snapd'd trusted certificates pool for the store communication with the `store-certs.<name>=<value>` system option.

To add a certificate, enter the following:

``` bash
$ snap set system store-certs.cert1="$(cat /path/to/mycert)"
```

A certificate can be removed with *unset*:

``` bash
$ snap unset system store-certs.cert1
```

Available since snapd 2.45

```{=html}
<h2 id='heading--backlight'>system.disable-backlight-service</h2>
```
May be set to *true* to disable the backlight service.

Available since snapd 2.46.

```{=html}
<h2 id='heading--power-key-action'>system.power-key-action</h2>
```
Defines the behaviour of the system when the power key is pressed.

May be set to one of:

-   ignore
-   poweroff
-   reboot
-   halt
-   kexec
-   suspend
-   hibernate
-   hybrid-sleep
-   lock

Available since snapd 2.23.

```{=html}
<h2 id='heading--timezone'>system.timezone</h2>
```
May be used to set a time zone value, as typically found in `/usr/share/zoneinfo`, such as `America/Chicago`.

Available since snapd 2.46.
