If you haven't disabled it in the [store settings](settings.md), snaps reviews are run automatically. They consist of linting and security policy checks, using a set of reviewer tools, which can be installed and used locally if you need to do an in-depth manual review or override a snap declaration (see below for details).

All snaps pending reviews can be found in the [review queue](https://dashboard.snapcraft.io/dev/snaps/reviewer/) store page.

<h2 id="heading--adding-reviewers">Adding reviewers</h2>

As of now, to add reviewers, you need to get in touch with the Store team. The only prerequisite to add reviewers is to ensure they have [created a store account](https://dashboard.snapcraft.io) beforehand.

See the [Report an issue section](issues.md) for contact details.

<h2 id="heading--how-to-review">How to review</h2>

The role of a reviewer is to periodically:

-   Examine the [review queue](https://dashboard.snapcraft.io/dev/snaps/reviewer/) and select an app to be reviewed
-   Check to see if there are any warnings or errors in the review output
    -   If the error is for an interface restriction or [`confinement: classic`](https://snapcraft.io/docs/reference/confinement) and you deem it is acceptable to grant: update the snap declaration on the store, leave a comment as to why it was granted, then click 'Run the automated review again'. This should now pass automated review
        -   **IMPORTANT**: there are defined processes for providing snap declarations that must be followed (see below)
-   If overriding warnings or errors, leave a comment to the developer as to why and how to avoid this error/warning in the future and click 'Approve'
-   If rejecting, leave a comment to the developer as to how to fix the error/warning and click 'Reject'
-   If need more information, ask the question in the comment section and click 'Ask for information'

<h2 id="heading--snap-declarations">Snap declarations</h2>

Snapd, the daemon that powers snaps on a system, uses snap declarations to check for installation, interface connection and interface auto-connection. Snap declarations are text files generated and signed by the store that ensure communication of policies and ownership between stores and clients.

In essence, a base declaration defines all the available interfaces and when they are installable, connectable or auto-connectable (see below for details). The store uses the base declaration to prompt for manual review or not. A reviewer may override the base declaration with a snap declaration. The store provides a form that allows enabling/disabling (auto-)connection of the declared interfaces that will update the declaration accordingly. The snap declaration applies to all revisions for that snap.

Importantly, if a snap declaration is specified, it overrides the base declaration constraints (eg, installation, connection, auto-connection) and the base declaration is not consulted.

<h2 id="heading--review-criteria">Review criteria</h2>

There are three important checkpoints to consider when reviewing snaps: confinement, interfaces and aliases.

-   Confinement defines the read and write access a snap can have on a system. There are three confinement policies: `strict`, `devmode` and `classic`.
-   Interfaces provide snaps extra access to the system and the base declaration is written to reflect the scope of that access.
-   Aliases provide familiarity for snaps users by aliasing a snap command to another name. They are declared inside snaps by developers and it's up to reviewers to enable auto-aliasing of these commands upon install.

<h3 id="heading--interfaces">Interfaces</h3>

The base declaration general policy is as follow:

-   If the interface gives elevated privileges to a snap on the user machine, it requires a manual connection at the user discretion.
-   If the interface requires a manual connection, it is automatically approved in the store.
-   If the interface is useful to most snaps and doesn't give elevated privileges on the user machine, it is automatically approved in the store and automatically connected upon install on the user machine.

You can get more details on the status and purpose of existing interfaces by going through the [snap interfaces list](https://snapcraft.io/docs/reference/interfaces).

<h3 id="heading--your-own-criteria">Your own criteria</h3>

Brand stores are in control of their brand-only snaps and their brand's reputation, therefore brand store reviewers may use whatever criteria that make sense for the brand.
