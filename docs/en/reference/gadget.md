The gadget snap is responsible for defining and manipulating system properties specific to one or more devices.

It contains device-specific support code and data, and is produced and signed by the device [brand](https://snapcraft.io/docs/glossary#heading--brand-store) via a [model assertion](assertions/model.md).

The gadget snap may encode the mechanisms for device initialisation, key generation and identity certification, as well as particular processes for the lifecycle of the device, such as factory resets.

It is perfectly possible for different models to share a gadget snap.

<h2 id="heading--setup-files">Setup files</h2>

In addition to traditional snap metadata, the gadget snap also holds some setup files fundamental to the initialization and lifecycle of the device:

-   `meta/snap.yaml` - Traditional snap details, with `type: gadget` explicitly defined.
-   `meta/gadget.yaml` - Gadget-specific information. See below.
-   `grub.conf` - Required grub configuration when using this bootloader.
-   `u-boot.conf` - Required u-boot configuration when using this bootloader.
-   `cloud.conf` - Optional [cloud-init](https://cloudinit.readthedocs.io/en/latest/) configuration; cloud-init is disabled if missing.

Sample configuration files may be found in the reference gadget snaps:

-   [Raspberry Pi (2B, 3B, 3A+, 3B+, 4B, Compute Module 3, and Compute Module 3+)](https://github.com/snapcore/pi-gadget)
-   [Beagleblack](https://github.com/ogra1/beaglebone-gadget)
-   [Dragonboard](https://github.com/snapcore/dragonboard-gadget)
-   [amd64](https://github.com/snapcore/pc-amd64-gadget)
-   [i386](https://github.com/snapcore/pc-i386-gadget)

<h2 id="heading--the-gadgetyaml-file">The gadget.yaml file</h2>

Four YAML keys are used to describe your target device:

-   `defaults` (YAML sub-section, optional): default configuration options for the defined snaps, applied on installation.

```{=html}
<!-- -->
```
``` yaml
defaults:
    <snap id>:
        <key>: <value>
```

-   `device-tree` (filename, optional): the Device Tree Blob path. If device-tree is specified, `dtbs/filename` must exist in kernel or gadget snap (depends on origin) and `snap_device_tree_origin` and `snap_device_tree` are made available for u-boot and grub.
-   `device-tree-origin` (string, optional): `kernel` or `gadget` (default) depending if the DTB is located in the kernel or in the gadget snap.
-   `volumes` (YAML sub-section, required): the volumes layout, where each disk image is represented as a YAML sub-section.

<h3 id="heading--the-volumes-sub-section">The `volumes` sub-section</h3>

Each volume is described by:

-   a name (required)
-   a partition structure (required)
-   a bootloader definition (`grub`, `u-boot`)
-   a partitioning schema eg. `mbr`

Volumes define the structure and content of the images to be written into one or more block devices of the gadget device. Each volume in the structure represents a different image for a "disk" in the device.

<h3 id="heading--specification">Specification</h3>

The `meta/gadget.yaml` file contains the basic metadata for gadget-specific functionality, including a detailed specification of which structure items compose an image. The latter is used both by snapd and by ubuntu-image when creating images for these devices.

A gadget snap's boot assets can also be automatically updated when the snap is refreshed. See [Updating gadget boot assets](https://snapcraft.io/docs/gadget-boot-assets) for further details.

The following specification defines what is supported in `gadget.yaml`: `</br>`{=html}
```{=html}
<details>
<summary>**gadget.yaml specification**</summary>

```yaml
# Define the format of this file. The default and latest format is zero.
# Clients reading this file must reject it the format is greater than
# the supported one. (optional)
format: <int>

# Default configuration options for defined snaps, applied on installation.
# The snap ID may be discovered via the snap info command.
# Since 2.33 snap ID can be the "system" nick to cover the system
# configuration. (optional)
defaults:
    <snap id>:
        <key>: <value>

# Interface connection instructions for plugs and slots of seeded
# snaps to connect at first boot. snap IDs can be the "system"
# nick as well. Omitting "slot" in an instruction is allowed
# and equivalent then to: slot: system:<plug>
# (since 2.34) (optional)
connections:
   -  plug: <plug snap id>:<plug>
      slot: <slot snap id>:<slot>
     
# If device-tree is specified, `dtbs/<filename>` must exist in kernel or
# gadget snap (depends on origin) and `snap_device_tree_origin` and
# and `snap_device_tree` are made available for u-boot and grub. (optional)
device-tree: <filename>

# Defines where the device tree is. Defaults to gadget. (optional)
device-tree-origin: kernel

# Volumes defining the structure and content for the images to be written
# into one ore more block devices of the gadget device. Each volume in
# in the structure represents a different image for a "disk" in the device.
# (optional)
volumes:

  # Name of volume and output image file. Must match [a-z-]+. (required)
  <volume name>:

    # 2-digit hex code for MBR disk ID or GUID for GPT disk id. (optional)
    id: <id>
                  
    # Bootloader in the volume. Required in one volume. (required/optional)
    bootloader: grub | u-boot

    # Which partitioning schema to use. Defaults to gpt. (optional)
    schema: mbr | gpt | mbr,gpt

    # Structure defines layout of the volume, including partitions,
    # Master Boot Records, or any other relevant content. (required)
    structure:
      - # Structure value is a list.

        # Structure item name. There's an implementation-specific constraint
        # on the maximum length. The maximum length of a partition name
        # for GPT is 36 characters in the UTF-16 character set. (optional)
        name: <name>

        # GPT unique partition id, disallowed on MBR volumes. (optional)
        id: <id>

        # Role defines a special role for this item in the image. (optional)
        # Must be either unset, or one of:
        #   mbr - Master Boot Record of the image.
        #   system-boot - Partition holding the boot assets.
        #   system-data - Partition holding the main operating system data.
        #   system-boot-image - Partition holding kernel images for the Little Kernel bootloader.
        #   system-boot-select - Partition holding state for snapd Little Kernel support.
        #
        # A structure with role:system-data must either have an implicit
        # file system label, or 'writable'.
        # A structure with role:system-boot-select must have 'snapbootsel' label.
        role: mbr | system-boot | system-data | system-boot-image | system-boot-select

        # Type of structure. May be specified as a two-hex-digit MBR partition
        # type, a GPT partition type GUID, or both on hybrid schemas.  The
        # special value `bare` says to not create a disk partition for this
        # structure. (required)
        type: <mbr type> | <gpt guid> | <mbr type>,<gpt guid> | bare

        # Size for structure item. Maximum of 446 for the mbr role. (required)
        size: <bytes> | <bytes/2^20>M | <bytes/2^30>G

        # The offset from the beginning of the image. Defaults to right after
        # prior structure item. (optional)
        offset: <bytes> | <bytes/2^20>M | <bytes/2^30>G

        # Offset of this structure element (in units of 512-byte sectors) is
        # written to the provided position within the volume in LBA48 pointer
        # format (32-bit little-endian). This position may be specified as a
        # byte-offset relative to the start of another named structure item.
        # (optional)
        offset-write: [<name>+]<bytes> |
                      [<name>+]<bytes/2^20>M |
                      [<name>+]<bytes/2^30>G

        # Filesystem type. Defaults to none. (optional)
        filesystem: none | vfat | ext4

        # Filesystem label. Defaults to name of structure item. (optional)
        filesystem-label: <label>

        # Content to be copied from gadget snap into the structure. This
        # field takes a list of one of the following formats. (required)
        content:

            # Copy source (relative to gadget base directory) into filesystem
            # at target (relative to root). Directories must end in a slash.
            - source: <filename> | <dir>/  # (required)
              target: <filename> | <dir>/  # (required)

            # Dump image (relative to gadget base directory) of the raw data
            # as-is into the structure at offset. If offset is omitted it
            # defaults to right after prior content item. If size is omitted,
            # defaults to size of contained data.
            - image: <filename>                                 # (required)
              offset: <bytes> | <bytes/2^20>M | <bytes/2^30>G   # (optional)
              offset-write: (see respective item above)         # (optional)
              size: <bytes> | <bytes/2^20>M | <bytes/2^30>G     # (optional)

        # Support automatic asset updates. (optional)
        update:
            # update only if new edition is higher than old edition.
            edition: uint32
            # This field takes a list of files to be preserved.
            # No support for preserving inside images. 
            # i.e. update will overwrite the whole image in this case.
            preserve: 
              - <filename>
```

</details>
```
`</br>`{=html}

```{=html}
<details>
<summary>**Example: Raspberry Pi 3 gadget.yaml**</summary>

```yaml
    device-tree: bcm2710-rpi-3-b.dtb
    volumes:
      pi3:
        schema: mbr
        bootloader: u-boot
        structure:
          - type: 0C
            filesystem: vfat
            filesystem-label: system-boot
            size: 128M
            content:
              - source: boot-assets/
                target: /
```

Used with the following directory structure:

    .
    ├── boot-assets
    │   ├── (...)
    │   └── bcm2710-rpi-3-b.dtb
    ├── meta
    │   ├── (...)
    │   ├── gadget.yaml
    │   └── snap.yaml
    ├── (...)
    └── README

</details>
```
`<a name="prepare-device">`{=html}`</a>`{=html}

<h2 id="heading--prepare-device-hook">prepare-device hook</h2>

The optional `prepare-device` hook will be called on the gadget at the start of the device initialisation process, after the gadget snap has been installed. The hook will also be called if this process is retried later from scratch in case of initialisation failures.

The device initialisation process is, for example, responsible for setting the serial identification of the device through an exchange with a device service. The `prepare-device` hook can for example redirect this exchange and dynamically set options relevant to it.

One must ensure that `registration.proposed-serial` is set to a value **unique** across all devices of the brand and model and that it does not contain a `/`. It is going to be used as the "serial number" (a string, not necessarily a number) part of the identification in case the device service supports setting it or **requires** it, as is the case with the *serial-vault*.

`</br>`{=html}
```{=html}
<details>
<summary>**Example: prepare-device hook**</summary>

```bash
#!/bin/sh

# optionally set the url of the service
snapctl set device-service.url="https://device-service"
# set optional extra HTTP headers for requests to the service
snapctl set device-service.headers='{"token": "TOKEN"}'

# set an optional proposed serial identifier, depending on the service
# this can end up being ignored;
# this might need to be obtained dynamically
snapctl set registration.proposed-serial="DEVICE-SERIAL"

# optionally pass details of the device as the body of registration request,
# the body is text, typically YAML;
# this might need to be obtained dynamically
snapctl set registration.body='mac: "00:00:00:00:ff:00"'

```
</details>
```
