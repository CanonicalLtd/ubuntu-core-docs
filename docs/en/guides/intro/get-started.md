Ubuntu Core is built from snaps, a secure, confined, dependency-free, cross-platform Linux packaging solution.

It's ideal for IoT and embedded systems because it manages itself. Whether it's running on a Raspberry Pi hidden for media streaming, or a Qualcomm DragonBoard tucked away in a garage to handle automation, Ubuntu Core handles application and security updates autonomously,

Snaps are used by Ubuntu Core to both compose the image run on a device, and to deliver consistent and reliable software application updates, often to low-powered, inaccessible, and remotely administered embedded and IoT systems.

While Ubuntu Core can operate much like a traditional Ubuntu installation, its installation, management and deployment is directed by its snap foundations, as outlined below:

|                                           |                                                   |
|-------------------------------------------|---------------------------------------------------|
| [1. Requirements](#heading--requirements) | [3. Installing Ubuntu Core](#heading--installing) |
| [2. Choose a release](#heading--releases) | [4. Using Ubuntu Core](#heading--using)           |

------------------------------------------------------------------------

```{=html}
<h2 id='heading--requirements'>1. Requirements</h2>
```
Ubuntu Core runs on a large range of hardware, and pre-built images are available for specific reference platforms. These images are a great way to quickly get started with Ubuntu Core on a Raspberry Pi, for example, or to explore Ubuntu Core's features and evaluate its potential. See [Supported platforms](../../platforms.md) for more details.

Alongside enough storage to hold the Ubuntu Core image, installation also requires a screen and keyboard to navigate a few installation prompts. These aren't needed post-installation.

Additionally, you will need an [Ubuntu SSO account](https://login.ubuntu.com/), with associated [SSH keys](https://login.ubuntu.com/ssh-keys). This setup is required because, after Ubuntu Core has been installed, Ubuntu SSO is used to marshal local or remote logins to your device. This behaviour can be changed after the first login. For help importing SSH keys, see the [Ubuntu community wiki](https://help.ubuntu.com/community/SSH/OpenSSH/Keys).

When Ubuntu Core is up and running, if you want to access your device remotely, you'll need an SSH client for your operating system. Linux and macOS both have clients built-in, whereas Windows users could use [Ubuntu on WSL](https://ubuntu.com/wsl).

```{=html}
<h2 id='heading--releases'>2. Choose a release</h2>
```
Ubuntu Core releases track a corresponding [Ubuntu LTS release](https://ubuntu.com/about/release-cycle), and there are currently three supported versions available:

| **Ubuntu Core version** | **Ubuntu base**                                                        | **Status**                      |
|-------------------------|------------------------------------------------------------------------|---------------------------------|
| Ubuntu Core 20 (UC20)   | [Ubuntu 20.04 LTS (Focal Fossa)](https://releases.ubuntu.com/20.04/)   | [beta1](../../releases/uc20.md) |
| Ubuntu Core 18 (UC18)   | [Ubuntu 18.04 LTS (Bionic Beaver)](https://releases.ubuntu.com/18.04/) | supported                       |
| Ubuntu Core 16 (UC16)   | [Ubuntu 16.04 LTS (Xenial Xerus)](https://releases.ubuntu.com/16.04/)  | supported                       |

Ubuntu Core is more than a repackaged version of Ubuntu. Ubuntu Core 20, for example, offers full-disk encryption, a new recovery mode and Raspberry Pi ARMv7 and ARMv8 support.

Ubuntu Core 18 has the broadest hardware and compatibility support, and we currently recommend this for general use. Ubuntu Core 16 is useful for some legacy systems.

```{=html}
<h2 id='heading--installing'>3. Install Ubuntu Core</h2>
```
The typical installation procedure is as follows:

1.  **Download your preferred Ubuntu Core image for your device**`</br>`{=html} See [Supported platforms](../../platforms.md) for links to images.`</br>`{=html} By default, images are compressed with *xz* and can be expanded with `xz -d <image-name.img.xz`.
2.  **Write the image to your device's storage medium** (eg. SD card for Raspberry Pi)`</br>`{=html} We recommend using [Raspberry Pi imager](https://www.raspberrypi.org/downloads/) for Windows, macOS and Linux.
3.  **Boot the device from the flashed storage and follow the on-screen prompts**`</br>`{=html} This step is version-specific, but the installation typically asks only two questions.

For example, when booting a fresh Ubuntu Core 18 image on a Raspberry Pi with a keyboard and monitor connected, after pressing enter twice to navigate the preamble, you're first asked to configure a network connection. Standard DHCP ethernet connections are configured automatically, but for integrated Wifi, you need to navigate to the `wlan0` device and press *Enter* to access SSID and wifi settings. When complete, select *Done* to complete the network configuration.

The final step for any Ubuntu Core installation is to enter the email address associated with your SSO account. This is what provides the secure and automatic authentication to your device, and subsequent reboots of your hardware will show host fingerprints alongside the SSH command required to access your device. Use this to connect to your device:

``` bash
$ ssh <your key> <SSO identity>@<device IP address>
```

<h3 id="heading--testing-ubuntu-core-with-qemu">Testing Ubuntu Core with QEmu</h3>

You can test Ubuntu Core without specific hardware from within a virtual machine.

To test Ubuntu Core with QEMU ([https://www.qemu.org/](https://www.qemu.org)), on Ubuntu, first install the `qemu-kvm` package and test it with *kvm-ok*:

``` bash
$ sudo apt install qemu-kvm
$ kvm-ok
```

To ensure compatibility with the UEFI security features in Ubuntu Core 20, also install the [OVMF](https://wiki.ubuntu.com/UEFI/OVMF) package:

``` bash
sudo apt install ovmf
```

The exact command to run will depend on your host hardware capabilities, such as available memory and CPU cores, but the following should work in the majority of cases:

``` bash
$ qemu-system-x86_64 -enable-kvm -smp 2 -m 1500 \
 -netdev user,id=mynet0,hostfwd=tcp::8022-:22,hostfwd=tcp::8090-:80 \
 -device virtio-net-pci,netdev=mynet0  -drive file=pc.img,format=raw \
 -drive file=/usr/share/OVMF/OVMF_CODE.fd,if=pflash,format=raw,unit=0,readonly=on
```

In the above command, the Ubuntu Core image is called *uc.img* and we forward the virtual SSH port 22 to port 8022 on the host, and 80 to 8090 for any web server you run. To access this configuration from your host machine, type the following:

``` bash
$ ssh <your key> <SSO identity>@<device IP address> -p 8022
```

```{=html}
<h2 id='heading--using'>4. Using Ubuntu Core</h2>
```
In use, the Ubuntu Core environment is much like a vanilla Ubuntu environment. The main difference is that its system configuration, package management, and update control is all governed by *snapd*, the snap daemon.

Snap features are comprehensively explained in the [Snap documentation](https://snapcraft.io/docs), but we'll cover some basic operations below:

You can list which snaps are installed with *snap list*:

``` bash
$ snap list
Name       Version        Rev   Tracking     Publisher   Notes
core20     20             768   latest/beta  canonical✓  base
pc         20-0.4         108   20/beta      canonical✓  gadget
pc-kernel  5.4.0-47.51.1  598   20/beta      canonical✓  kernel
snapd      2.46.1         9279  latest/beta  canonical✓  snapd
```

The above shows the standard set of snaps in a default Ubuntu Core 20 installation. For more details on what these do, see [Snaps in Ubuntu Core](../../coresnaps.md).

As Ubuntu Core is a minimal installation, one of the first things you might want to install is a text editor. You can search for snaps using *snap find*, or the [Snap Store](https://snapcraft.io/store). Nano is a good choice:

``` bash
$ snap install nano
```

By default, Ubuntu Core defaults to disabling access to most resources, including writing files to your home directory. Permissions in snap and Ubuntu Core are handled by *interfaces*. You can see which interfaces nano needs with the *snap connections* command:

``` bash
snap connections nano
Interface        Plug                  Slot  Notes
home             nano:home             -     -
removable-media  nano:removable-media  -     -
```

Connecting the [home](https://snapcraft.io/docs/home-interface) interface between Ubuntu Core and *nano* will allow you to save files to your home directory. This is accomplished with the *connect* command:

``` bash
$ snap connect nano:home :home
$ snap connections nano
Interface        Plug                  Slot   Notes
home             nano:home             :home  manual
removable-media  nano:removable-media  -      -
```

After running the above command, you will now be able use nano to create and edit text files in your home directory!

The `:home` syntax denotes the *home* slot on Ubuntu Core. For more information on how to work with interfaces, see [Interface management](https://snapcraft.io/docs/interface-management) in the snap documentation.

To remove a snap from your system, along with its internal user, system and configuration data, use the remove command:

``` bash
$ sudo snap remove nano
nano removed
```

For more information on how to work with snaps, including how to control daemons/servers, how to make data snapshots and how to install specific revisions, see the [Snap Documentation](https://snapcraft.io/docs/).
