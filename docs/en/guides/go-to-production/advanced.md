Here we provide an overview of the advanced production model:

-   You define your own device type and hence have one or more of:
    -   A kernel snap of you choice. See the [board enablement overview](../build-device/board-enablement.html) for details.
    -   A gadget snap that you have created. See the [board enablement overview](../build-device/board-enablement.html) for details.
    -   A factory image that contains snaps that are not publicly available
-   You curate your own store of snaps, a [Brand store](../../build-store)
-   You want *Update Control*, to decide when updates are published to devices you have created

They provide all the functionality and control required to manage your device ecosystem.

!["Advanced Production Model"](../../../media/production-model-rich.png)

<h2 id="heading--steps">Steps</h2>

The high level steps for this production model are:

1.  Contact your Canonical representative to:
    -   Create you a *Brand Account*
    -   Create your *[Brand store](../../build-store)* associated with this account
    -   Provide instructions on tools and process needed for manufacturing
2.  Create your gadget snap and publish it in your store
3.  Create the extra snaps needed for your device's base functionality and publish them in your store
4.  Create a *model assertion* and sign it using a key you have registered with the store
5.  Create your device image (including your extra snaps) using the *signed model assertion*
6.  Flash your image to your devices at your production facility / factory
7.  Perform an initial boot of the device in the factory to get a *signed serial assertion* from a *Serial Vault*
8.  Distribute your devices to the consumer

Let's take a closer look at some these steps.

<h2 id="heading--create-your-gadget-snap">Create your gadget snap</h2>

The reference section contains detailed information about the format of a gadget snap: [Gadget Snap Format](../../reference/gadget.html)

Of particular importance is the prepare-device hook which must contain the details how to contact your Serial Vault (see later section).

<h2 id="heading--create-the-extra-snaps">Create the extra snaps</h2>

Once your snap is working as expected, publish it to the brand store. These snaps will only be available to devices whose model assertion specifies your brand and store.

Detailed information about building snap packages can be found on [snapcraft.io](http://snapcraft.io).

<h2 id="heading--create-a-model-assertion">Create a model assertion</h2>

Steps for creating a signed model assertion are provided [here](../build-device/image-building).

Let's look at a sample of the JSON file used as input when creating a model assertion for a fictional device, the "Device 1", made by the brand "ACME Corp". The host architecture is armhf. The image will be populated with the gadget and kernel snaps that support this hardware, the core snap, and two extra snaps required for the device to function correctly, "acme-dashboard" and "acme-control-plane". Note that the `authority-id` and `brand-id` match the `account-id` defined for the ACME Corp account.

    {
      "type": "model",
      "authority-id": "acme",
      "brand-id": "acme",
      "store": "abcdef123456789"
      "series": "16",
      "model": "acme-device-1",
      "architecture": "armhf",
      "gadget": "device-1",
      "kernel": "device-1-kernel",
      "required-snaps": ["acme-dashboard", "acme-control-plane"],
      "timestamp": "2016-12-01T12:00:00+00:00"
    }

As noted in the image building documentation, you sign it with your registered key, which produces your signed model assertion file, which is used in the next step to build your image.

<h2 id="heading--build-the-image">Build the image</h2>

Steps for creating an image from the signed model assertion are found [here](../build-device/image-building).

<h2 id="heading--factory-flash-you-device">Factory flash you device</h2>

The process requires a serial assertion signed by your brand account. To support you in managing you keys and signing the serial assertion during the production process Canonical have developed an open source tool called the [Serial Vault](http://github.com/ubuntu-core/identity-vault).

The Serial Vault allows you to make sure the device leaves production ready to communicate with the brand store and gives you confidence that only devices produced by you have access to your snaps.

<h2 id="heading--distribute-your-devices">Distribute your devices</h2>

When your devices are in the field and operational they will communicate with your brand store to identify when updates are available or to install snaps that the user wants.
