This page will give you an overview of the lifecycle of a snap package on a brand store, with a focus on its differences with the main snap store. These differences are minimal and if you are already familiar with snaps and snapcraft, you can jump to learning how to [upload snaps to a specifc store](upload-snaps.md).

<h2 id="heading--lifecycle-of-a-snap-package">Lifecycle of a snap package</h2>

Snap packages are built using snapcraft, a command-line tool which uses a YAML file as a declaration to build a package. You can easily get started with snapcraft on [snapcraft.io](https://snapcraft.io/docs/build-snaps/).

In a nutshell, the lifecycle of a snap is the following:

1.  Once you have built a snap, it can be pushed and released to any snap store allowing you to do so: from the command-line, from the store web UI, or from your continuous integration pipeline (eg. Travis).

2.  Snaps have their names tied and registered to a specific store, allowing for an inheritance model between stores. For example, your brand store can distribute a "my-app" snap inherited from the main snap store. To distribute your customized version of "my-app", you will have to pick a different name for it (eg. "my-brand-app"), allowing users to easily differentiate between the two. This also means that no other store will be able to distribute "my-brand-app", unless you allow it to inherit from yours.

3.  Snaps can be released through severals tracks ("latest", "1.0", "2.0", etc) you can create to match your software versioning scheme, each track allowing for four channels: stable, candidate, beta and edge, giving your users a very clear picture of what they are installing and its stability level.

4.  The snapd client, installed on all user devices, checks the store the client is connected to for updates several times a day and upgrades to the latest available version of your snap, for the track/channel the user is following.
