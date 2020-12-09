```{=html}
<!-- 
Version: 2.0
-->
```
[Ubuntu Core](index.md) is designed for IoT and embedded environments, from a single device to a factory deployment of thousands. But it's also a great platform for experimentation and project building.

For more details on which platforms are supported, and for links to installation guides, see [Supported platforms](platforms.md).

<h2 id="heading--accessing-an-ubuntu-core-system">Accessing an Ubuntu Core system</h2>

Access to a self-deployed Ubuntu Core system is via the networking configuration and [SSH key](https://login.ubuntu.com/ssh-keys) linked to the [Ubuntu SSO account](https://login.ubuntu.com/) used to set up the system.

If you have a display connected to your device, it will display the account and IP address, alongside host key fingerprints, upon successful boot:

``` no-highlight
Ubuntu Core 20 on 10.0.2.15 (tty1)
The host key fingerprints are:

    RSA     SHA256:PaSE+kKJLKSDREKJeiKLKJeJ3JKLKJEEKJ
    ECDSA   SHA256:wIKSDFLKsdlkfsjDFSdfujsdf83kljSDdD
    ED25519 SHA256:7JSDFSDkdsS3KJKSDsdflkfSDFLJKDSs39

To login:

    ssh <sso-id>@10.0.2.15

Personalize your account at https://login.ubuntu.com.
```

By default, you can only access your deployed Ubuntu Core system from SSH, and not physically from the device itself. See [System user](/guides/manage-devices/) for details on how to create a system user with local login on systems that allow _classi_confinement (notably, not Ubuntu Core 20).

<h2 id="heading--user-environment">User environment</h2>

The Ubuntu Core user environment operates much like a traditional Ubuntu environment with the exception that you can only install and configure your system through *snap* packages.

To see which snap packages are installed, type `snap list`:

``` bash
$ snap list
Name       Version               Rev   Tracking     Publisher   Notes                                                                            
core20     20                    634   latest/edge  canonical✓  base                                                                             
pc         20-0.4                101   20/beta      canonical✓  gadget                                                                           
pc-kernel  5.4.0-31.35           502   20/beta      canonical✓  kernel                                                                           
snapd      2.45+git396.g5c3995e  7790  latest/edge  canonical✓  snapd
```

See [Snaps in Ubuntu Core](coresnaps.md) for more details on what these snaps do and how they're used.

To install a new snap package, type `snap install <snap-name>`:

``` bash
$ snap install nextcloud
nextcloud 18.0.4snap1 from Nextcloud✓ installed
```

Use the `remove` argument to remove a snap.

```{=html}
<div class="p-notification--positive"><p markdown="1" class="p-notification__response">
<span class="p-notification__status">Snap documentation:</span> to learn more about how snaps work, and how to use, see the 
<a href="https://snapcraft.io/docs/">Snap documentation.</a>
</p></div>
```
<h2 id="heading--configuring-a-snap">Configuring a snap</h2>

You can view and set specific configuration options for a snap with the *get* and *set* commands:

``` bash
$ snap get nextcloud
Key        Value
mode       production
nextcloud  {...}
php        {...}
ports      {...}
private    {...}

$ snap get nextcloud ports
Key          Value
ports.http   80
ports.https  443

$ sudo snap set nextcloud ports.http=81
```

```{=html}
<div class="p-notification--positive"><p markdown="1" class="p-notification__response">
<span class="p-notification__status">Setting snap options:</span> for more details on setting snap-specific options, see
<a href="https://snapcraft.io/docs/configuration-in-snaps">Managing snap configuration.</a>
</p></div>
```
<h2 id="heading--managing-a-snap-service-or-daemon">Managing a snap service or daemon</h2>

If a snap provides one or more services, they can be listed with the *services* command:

``` bash
snap services nextcloud
Service                    Startup  Current  Notes
nextcloud.apache           enabled  active   -
nextcloud.mdns-publisher   enabled  active   -
nextcloud.mysql            enabled  active   -
nextcloud.nextcloud-cron   enabled  active   -
nextcloud.nextcloud-fixer  enabled  active   -
nextcloud.php-fpm          enabled  active   -
nextcloud.redis-server     enabled  active   -
nextcloud.renew-certs      enabled  active   -
```

Use *start* and *stop* to control whether a service is running:

``` bash
$ snap stop nextcloud.apache
Stopped.
```

To prevent a service from starting on the next boot, use the `--disable` option:

``` bash
$ snap stop --disable nextcloud.apache
```

The inverse of the above is `start --enable` to start a service and enable it at boot time:

``` bash
$ snap start --enable nextcloud.apache
```

<h2 id="heading--accessing-logs">Accessing logs</h2>

If you need to see the log output for a snap’s services, use the logs command:

``` bash
$ snap logs nextcloud
2020-05-14T10:50:57Z systemd[1]: Stopping Service for snap application nextcloud.apache...
2020-05-14T10:50:57Z nextcloud.apache[49131]: No certificates are active: using HTTP only
```

Adding the -f option will keep log output open so you can follow new entries as they occur:

``` bash
$ sudo snap logs nextcloud -f
```

```{=html}
<div class="p-notification--positive"><p markdown="1" class="p-notification__response">
<span class="p-notification__status">Service management:</span> for more details on starting and stopping services, see
<a href="https://snapcraft.io/docs/service-management">Service management.</a>
</p></div>
```
