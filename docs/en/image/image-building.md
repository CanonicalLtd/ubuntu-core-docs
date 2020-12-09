Any device running Ubuntu Core is instantiated from an image. This image contains little more than the kernel, an *init* process, and a few essential tools. On all but the earliest releases of Ubuntu Core, even the *snapd* daemon that manages snaps is itself installed via its own snap.

Reference images are available for supported platforms, including Raspberry Pi, Qualcomm Snapdragon and x86/KVM virtualisation, and these can be download and installed easily. See [Supported platforms](../platforms.md) for the current list and for download links to both the images and the model assertions they use.

<h2 id="heading--inside-a-model-assertion">Inside a model assertion</h2>

Creating an Ubuntu Core image starts with a *model assertion*, a digitally signed text file with structured headers defining every aspect of the image. The input for defining and signing such a document is provided as JSON text:

``` json
{
  "type": "model",
  "series": "16",
  "model": "ubuntu-core-18-pi-arm64",
  "architecture": "arm64",
  "gadget": "pi=18-pi",
  "kernel": "pi-kernel=18-pi",
  "authority-id": "canonical",
  "brand-id": "canonical",
  "timestamp": "2020-01-22T08:35:30+00:00"
}
```

The above snippet shows the reference [model assertion for the Raspberry Pi](http://cdimage.ubuntu.com/ubuntu-core/18/stable/current/ubuntu-core-18-arm64+raspi.model-assertion). It includes details such as the store to use (`brand-id` and `authority-id`), the model name (`ubuntu-core-18-pi-arm64`) and the hardware architecture (`arm64`). Any snaps installed on the device will respect this selected architecture.

As outlined in [Snaps in Ubuntu Core](../coresnaps.md), there are three principle snaps-types that combine to create the Ubuntu Core environment. These are **kernel**, **gadget** and **core**, and all three need to be referenced within a custom model assertion.

<h2 id="heading--building-with-ubuntu-image">Building with ubuntu-image</h2>

Images are built from a model assertion using [ubuntu-image](https://github.com/CanonicalLtd/ubuntu-image), a tool to generate a bootable image. It can be installed on a [snap-supporting Linux system](https://snapcraft.io/docs/installing-snapd) as follows:

``` bash
$ sudo snap install ubuntu-image --beta --classic
```

The *ubuntu-image* command needs only the filename of the model assertion to build an image.

The following will build an *amd64* image using the [ubuntu-core-18-amd64.model](https://raw.githubusercontent.com/snapcore/models/master/ubuntu-core-18-amd64.model) reference model assertion:

``` bash
$ ubuntu-image snap ubuntu-core-18-amd64.model
fetching snapd
fetching pc-kernel
fetching core18
fetching pc
```

The output includes the *img* file itself, alongside *seed.manifest* and *snaps.manifest* files. These manifest files simply list the specific revision numbers for the snapd, pc, pc-kernel and core snaps built within the image.

You can now test the resulting image. Using [QEMU](https://www.qemu.org/), for instance, the following command will boot the image inside a VM and forward (SSH) port 22 to 8022 on your machine:

``` bash
$ qemu-system-x86_64 -enable-kvm -smp 2 -m 1500 \
-netdev user,id=mynet0,hostfwd=tcp::8022-:22,hostfwd=tcp::8090-:80 \
-device virtio-net-pci,netdev=mynet0 -drive file=pc.img,format=raw
```

After running through the Ubuntu Core network setup and entering your account details, you will be able to SSH to your new Ubuntu Core deployment:

``` bash
$ ssh <username>@localhost -p 8022
```

You are now connected to the Ubuntu Core virtual machine, from where you can configure and install whatever apps you need.

To list which snaps are installed, for example, type 'snap list':

``` bash
$ snap list
Name       Version       Rev   Tracking  Publisher   Notes
core18     20200124      1668  stable    canonical✓  base
pc         18-2          36    18        canonical✓  gadget
pc-kernel  4.15.0-88.88  399   18        canonical✓  kernel
snapd      2.43.3        6434  stable    canonical✓  snapd
```

To view the model assertion used to build the image, type `snap model --assertion`:

``` bash
$ snap known model
type: model
authority-id: canonical
series: 16
brand-id: canonical
model: ubuntu-core-18-amd64
architecture: amd64
base: core18
display-name: Ubuntu Core 18 (amd64)
gadget: pc=18
kernel: pc-kernel=18
timestamp: 2018-08-13T09:00:00+00:00
sign-key-sha3-384: 9tydnLa6MTJ-jaQTFUXEwHl1yRx7ZS4K5cyFDhYDcPzhS7uyEkDxdUjg9g08BtNn
[...]
```

To output the serial assertion rather than the model assertion, type `snap model --serial`:

``` bash
$ snap model --serial
brand-id:  canonical
model:     ubuntu-core-18-amd64
serial:    acb9cfdc-c4a1-4317-809e-e9c306cfc7e5
```

See the [Snap documentation](https://snapcraft.io/docs) for more details on working with snaps. To build a custom image that includes your own selection of snaps, take a look at [Custom images](custom-images.md).
