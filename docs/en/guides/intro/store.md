Snaps and Ubuntu core devices in general are tied to accounts existing in the Ubuntu store or in another snaps enabled store.

<h2 id="heading--the-ubuntu-store">The Ubuntu Store</h2>

The main way of distributing snaps to is through [the Ubuntu Store](https://dashboard.snapcraft.io "Ubuntu store"), where you can customize how your snap is presented, review each new upload, and control publishing over several release channels. Here is the model it follows.

<h2 id="heading--developer-namespace">Developer namespace</h2>

You'll choose a unique developer namespace as part of the store account creation process. This namespace will represent you as a publisher in the store and you won't be able to change it afterwards.

<h2 id="heading--naming">Naming</h2>

You can publish a snap under any name you have rights to. Names can be registered by using the `snapcraft register` command, clicking **New Snap** on the developer portal, or by visiting the [Register name](https://dashboard.snapcraft.io/dev/snaps/register-name/ "register name") page. You can also grant other developers permission to publish versions of a snap you own, for example as part of an open source project.

<h2 id="heading--collaborating">Collaborating</h2>

You can share the management of any snap registered in the store under your own namespace. This will allow you to collaborate on specific snaps and extend administration rights (upload and publication) to other developers.

<h2 id="heading--uploading">Uploading</h2>

Uploading snaps to the store can be done directly from the command line or on the [store website](https://dashboard.snapcraft.io "Ubuntu store") itself. Once uploaded you choose the release channel(s) (`stable`, `candidate`, `beta`, `edge`) that the snap will be published to.

It's worth noting that when you upload a snap, the store assigns it a revision number of 1. The store then automatically increments this revision number each time you upload a new version.

<h2 id="heading--release-channels">Release channels</h2>

On the store, snaps can be published into different channels: `stable`, `candidate`, `beta`, and `edge`. This enables you to engage with users who are willing to test changes, and it helps users decide how close to the leading edge of development they want to be.

There are four channels available and snaps can be published into several at the same time:

-   `stable` is what most users will consume and as the name suggests, should be your most polished, stable and tested versions. Snaps in this channel appear in user searches.
-   `candidate` is used to vet uploads that should require no further code changes before moving to stable.
-   `beta` is used to provide preview releases of semi-stable changes. Snaps requiring the devmode flag to work are allowed in this channel.
-   `edge` is for your most recent changes, probably untested and with no guarantees attached. Snaps requiring the devmode flag to work are allowed in this channel.

By default, snaps are installed from the `stable` channel. Versions of snaps from other channels need to be explicitly selected by users:

    snap install hello --beta

And snaps can be refreshed from a different channel to the one it was originally installed from:

    snap install hello
    snap refresh hello --beta

This switches the snap to using the `beta` channel for future updates.

<h2 id="heading--publishing">Publishing</h2>

After you've chosen a channel, your snap is sent for review. Most snaps are reviewed by way of automated checks, but if your snap uses sensitive [interfaces](../../reference/interfaces/index.html), it may be flagged for a manual review and you will receive a notification email from the store whenever the status of the review evolves.

Once your snap has been reviewed and approved, you can publish it when you're ready, instantly making the snap available to users.
