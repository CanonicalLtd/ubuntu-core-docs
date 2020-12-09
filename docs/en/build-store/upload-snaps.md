By default, snapcraft will upload snaps to the main snap store. To change this behaviour and upload snaps to a specific brand store, you need to:

1.  Know the id of the store your are targeting, which is available in the [Store List](https://dashboard.snapcraft.io/dev/store/list/) page, which lists stores you can upload to or manage

-   Pass it to snapcraft through the `SNAPCRAFT_UBUNTU_STORE` environment variable

<h2 id="heading--example">Example</h2>

``` bash
$ export SNAPCRAFT_UBUNTU_STORE=<store id>
```

Then, you can register names for your snaps, push and release them:

``` bash
# snapcraft register <snap name>
$  snapcraft register my-app
# snapcraft push <snap file>
$ snapcraft push my-app
# snapcraft release <snap name> <revision> <track/channel>
$ snapcraft release my-app 12 latest/stable
```

If this is your first time with snapcraft, have a look at [this step-by-step tutorial](https://snapcraft.io/docs/build-snaps/publish) that will guide you through the publication process.
