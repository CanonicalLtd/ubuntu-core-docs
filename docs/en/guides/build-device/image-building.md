For [supported devices](https://developer.ubuntu.com/core/get-started), there are official Ubuntu Core images for both [Core 18](http://cdimage.ubuntu.com/ubuntu-core/18/stable/) and [Core 16](http://cdimage.ubuntu.com/ubuntu-core/16/stable/).

<h2 id="heading--build-a-custom-ubuntu-core-image">Build a custom Ubuntu Core image</h2>

This document will walk you through the steps needed to build either a Core 18 or Core 16 image for a device family. You will learn how to:

-   Create and upload a snapcraft key to the store
-   Create a model assertion for your target device
-   Compose and build a custom image using the `ubuntu-image` command

If you want to build an Ubuntu Core image for a device family that is not already supported, please follow the [Board enablement overview](../../guides/build-device/board-enablement.html) document.

<h2 id="heading--snapcraft-keys">Snapcraft keys</h2>

Before starting, you need to create a key to sign your future store uploads.

<h3 id="heading--1-create-a-key">1. Create a key</h3>

To generate a key that can then be linked to your Ubuntu Store account, run the following:

``` bash
snapcraft create-key
```

You can pass an optional name for the key:

``` bash
snapcraft create-key foo
```

The *create-key* command will ask you for a password to protect the key.

Generating the key will take some time because it's creating a 4096 bit long key that needs some entropy to complete. Moving the mouse or typing can help to speed up the process, as will installing the `rng-tools` package beforehand.

Now, you can list your keys with:

``` bash
snapcraft list-keys
```

Your keys are located within *~/.snap/gnupg*, and it's a good idea to keep a backup of this directory.

<h3 id="heading--2-register-the-key-with-the-store">2. Register the key with the store</h3>

With the key created, you now need to register it with the store, effectively linking it to your account. During this step, you will be asked to select an existing key (if you have more than one) and log in with your store account credentials:

    snapcraft register-key

The key is now registered with the store and you can start the actual image building.

<h2 id="heading--image-building">Image building</h2>

An Ubuntu Core image is composed of at least three snaps installed together: OS, gadget and kernel.

-   The OS snap contains *Ubuntu Core* itself and will be downloaded during image creation.
-   The kernel and gadget snaps are layers to personalise or enable your target device. They can be locally built or pulled from the store if they already exist.

A *model assertion*, defined within a JSON-formatted file and consumed by the build process, is used to configure your image.

<h3 id="heading--1-create-a-model-assertion">1. Create a model assertion</h3>

To build an image, it is required you have a signed model assertion.

<h3 id="heading--example">Example</h3>

The following is an example model assertion that builds a Core 18 image for a Raspberry Pi 3 board. The JSON file is named `pi3-model.json`:

``` json
{
  "type": "model",
  "authority-id": "<your account id>",
  "brand-id": "<your account id>",
  "series": "16",
  "model": "my-pi3",
  "architecture": "armhf",
  "base": "core18",
  "gadget": "pi=18-pi3",
  "kernel": "pi-kernel=18-pi3",
  "timestamp": "<timestamp>"
}
```

-   `authority-id` and `brand-id` are your Ubuntu SSO account ID. See [your account page](https://dashboard.snapcraft.io/dev/account/), in the `Account-Id` field.
-   `series`: the version of the assertion format, and snap namespaces, being used. Later series are backwards compatible.
-   `model`: a free form lower-case name for your target device.
-   `architecture`: the architecture of the device you are building the image for.
-   `base`: the runtime environment to use.
-   `gadget` and `kernel` refer to snaps already existing in the store or in the current directory.
-   `=18-pi3`, appended to *gadget* and *kernel*, defines the channel to track. For the universal *pi* snaps, these denote channels for specific models.
-   `timestamp` is a valid timestamp you need to generate using the `date -Iseconds --utc` command. The timestamp must be later than when the key that signs the model was registered.
-     `required-snaps`: you can optionally add a list of required snaps that will be unremovable and will be downloaded from the store or locally if they exist in the current directory

To alternatively build an Ubuntu Core 16 image, replace the `base`, `gadget` and `kernel` lines with the following:

``` json
  "gadget": "pi3",
  "kernel": "pi2-kernel",
```

**Note**: There are additional requirements when the image targets a Brand Store. Use your Brand SSO Account's account-id in the `authority-id` and `brand-id` fields. Also add a `store` key whose value is your Brand `store-id`.

<h3 id="heading--2-sign-your-model-assertion">2. Sign your model assertion</h3>

Now you have to sign the model assertion with a key by piping your JSON model through the `snap sign -k <key name>` command. The output is the assertion document you will use to build your image.

**Note**: If the model is for a Brand Store, the key must be registered by the Brand SSO Account.

``` bash
cat pi3-model.json | snap sign -k default &> pi3.model
```

This command will ask you for the password used when you created the key.

You can check the signing process has worked by ensuring the signed key has been appended to *pi3.model*.

<h3 id="heading--3-build-the-image">3. Build the image</h3>

You can now create your image with the `ubuntu-image` tool. To install it, run:

``` bash
snap install --classic ubuntu-image
```

Then, create the image:

``` bash
sudo ubuntu-image -c beta -O pi3-test pi3.model
```

<h4 id="heading--arguments">Arguments</h4>

-   `-c`: the channel snaps are downloaded from
-   `-O`: a directory for generated files

You can include specific snaps pre-installed by default in the image by using the `--extra-snaps` argument. For example:

``` bash
sudo ubuntu-image -c beta --extra-snaps rocketchat-server --extra-snaps nextcloud -o pi3-test.img pi3.model
```

Note: The `--extra-snaps` argument takes either a snap name accessible from the store or a local path to a built snap.

<h3 id="heading--4-your-image-is-ready">4. Your image is ready</h3>

You can use a tool like `dd` to write the image to an SD card and boot your device (replace *sdXX* with the node for your storage device):

``` bash
sudo dd if=pi3-test/pi3.img of=/dev/sdXX bs=32M
sync
```

<h4 id="heading--first-boot-tips">First boot tips</h4>

-   On the first boot you have to sign in with a valid Store account to make use of the device.
-   `console-conf` will download the SSH key registered with your Store account and configure it so you can log into the device via `ssh <account name>@<device address>` without a password.
-   There is no default `ubuntu` user on these images, but you can run `sudo passwd <account name>` to set a password in case you need a local console login.

<h4 id="heading--known-problems-and-limitations"> Known problems and limitations:</h4>

-   You need a monitor or a serial cable plugged to the device to be able to go through the first use setup process handled by `console-conf`.
