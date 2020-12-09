Ubuntu Core is built from *snaps*, a secure, confined, dependency-free, cross-platform Linux packaging format.

Snaps are entirely self-contained, even to the point of encapsulating their own file system, and this means they include everything needed to run. They're used by Ubuntu Core to both compose the image that's run on a device, and to deliver consistent and reliable software updates, often to low-powered, inaccessible, and remotely administered embedded and IoT systems.

Whether it's an update to a single device, a specific subset of devices, or a deployment of tens of thousands of devices, snaps enable Ubuntu Core to maintain and verify a system’s integrity:

-   **Autonomous updates to any device** - the update service needs to be a reliable and automatic process, catering for a predictable update cadence and an optional fine level of control over when and how updates are delivered

-   **Incomplete and problematic update recovery** - the inaccessible nature of many embedded device installations makes it imperative that updates are impregnable and capable of withstanding broken, blocked, partial and interrupted updates

-   **Critical update provision** - in specific circumstances when an ad-hoc or critical update needs to be made, the update system needs to give these priority and incorporate the update back into the regular update service

-   **Unpredictable hardware and network conditions** - in situations that can’t be easily modelled or predicted, any update system needs to have enough redundancy to handle roll-backs, network-free bootstrapping and autonomous re-provisioning

```{=html}
<div class="p-notification--positive"><p markdown="1" class="p-notification__response">
<span class="p-notification__status">Note:</span>
For further details on how snaps work and how they're built, see 
<a href=https://snapcraft.io/docs>Snap documentation</a>.
</p></div>
```
<h2 id="heading--types-of-snap">Types of snap</h2>

The snap packaging ecosystem consists of the following parts:

-   **snap** is both the command line interface and the application package format
-   **snapd** is the background service that manages and maintains your snaps
-   **snapcraft** is the command and the framework used to build your own snaps
-   **Snap Store** provides a place to upload your snaps, and for users to browse and install

Developers can publish snaps to the Snap Store or to their own private *Brand Store*. They take sole responsibility for update cadence and quality. While snaps are commonly known as an application packaging format, Ubuntu Core is built from several different types of snap:

1.  **kernel**: defines the Linux kernel for a device
    The kernel snap is selected with a *model assertion* which is produced and signed before the image is built. Once the image is built, the kernel snap may be updated but cannot be replaced by a completely different kernel snap.

2.  **gadget**: defines system properties
    The gadget snap is responsible for defining and manipulating the system properties which are specific to one or more devices that will usually look similar to one another from an implementation perspective.

3.  **core**: provides the execution environment
    The core snap is the execution environment inside which application snaps run. It also serves as the root file system for Ubuntu Core images. The current core snap is built from Ubuntu 18.04 packages.

4.  **app**: applications, daemons and tools
    Packages applications, pulled from multiple upstream sources using diverse build systems. The *snapd* daemon is itself installed as a snap (except with an old *core*, where it's included).

As with the core file system, snaps are presented to the system as read-only and are granted access to whatever resources they need through a set of explicit permissions, known as interfaces. Interfaces are implemented using well-tested Linux kernel confinement features.
