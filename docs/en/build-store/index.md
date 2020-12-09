Ubuntu Core includes access to the to Canonical's [Snap Store](https://snapcraft.io/store) by default. This allows any developer to easily release and support apps for multiple architectures, on multiple release channels, from daily builds to stable releases.

See [Releasing to the Snap Store](https://snapcraft.io/docs/releasing-to-the-snap-store) for more details on how to publish and distribute snaps to devices from the Snap Store.

<h2 id="heading--brand-stores">Brand stores</h2>

For larger projects and ISVs, it is often a requirement to publish snaps using a brand account to a brand-specific store.

A brand store allows vendors running Ubuntu Core and snap-based devices to control exactly what snaps are available, and when. It can inherit selected packages from other snap stores, and host a set of snaps specific to a brand and device models, and be either open to all developers or a specific list.

Here is an overview of how to create a brand account and define collaborators.

<h2 id="heading--registering-accounts">Registering accounts</h2>

To create an account, go to <https://snapcraft.io/account>. We then recommend the following process:

-   create an umbrella/brand account using the project name or name of the legal entity publishing the software
-   let each team member who releases and manages snaps register a personal account
-   grant each team member access to the snaps by adding their personal accounts as collaborators

<h2 id="heading--registering-snaps">Registering Snaps</h2>

Snaps can be registered using the `snapcraft` tool or via the web. Snaps should be registered using the brand/umbrella account.

<h3 id="heading--registering-snaps-with-snapcraft">Registering snaps with Snapcraft</h3>

-   install `snapcraft` using `snap install snapcraft --classic` on Linux or `brew install snapcraft` on macOS
-   execute `snapcraft login` and authenticate using the brand/umbrella account
-   once authenticated register the snap name(s) with `snapcraft register   yoursnapname`

For more details on this process, see [Registering your app name](https://snapcraft.io/docs/registering-your-app-name).

<h3 id="heading--registering-snaps-via-the-web">Registering snaps via the web</h3>

-   login to [https://snapcraft.io]() using the brand/umbrella account
-   register snap(s) here: [https://snapcraft.io/account/register-name]()

<h2 id="heading--collaborators">Collaborators</h2>

When you've registered snap(s) using a brand/umbrella account, you should add team members' personal accounts to the umbrella/brand account via the Dashboard for your snap. For example:

https://dashboard.snapcraft.io/snaps/yoursnapname/collaboration/

Collaborators can then push and release snaps using their personal accounts.
