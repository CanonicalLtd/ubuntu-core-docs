Making a smart doorbell from Ubuntu Core for your family and friends is undeniably awesome! At some point you may want to take your superb creation into production.

So: how does one take their brilliant Snap image into production with updatable snaps from a store? Not surprisingly, there is more than one way.

Here, we introduce two *production models*. One simple, one advanced.

Let's dive into the overviews.

<h2 id="heading--simple-production-model-overview">Simple production model overview</h2>

The first production model is simple. It's a good choice when you publish your extra snaps (those that make the device compelling and unique) for free in the Ubuntu Store and you base your image on unmodified snaps (kernel, gadget, and core). Your device in the field gets updates, including updates of your extra snaps that you included in the image.

One key limitation of the simple model is that you cannot have a a brand store. The devices all point to the base Ubuntu Store.

For a closer look, see [simple production model](simple.md).

<h2 id="heading--advanced-production-model-overview">Advanced production model overview</h2>

The second production model fits a range of additional cases. For example:

-   You want a brand store to host private snaps for your devices
-   You might use a customized kernel snap or a highly customized gadget snap (the advanced model requires a customized gadget snap but the required customizations are minimal).

This production model involves a bit more work, but it does offer a much wider range of options.

For a closer look, see [advanced production model](advanced.md).
