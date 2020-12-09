The reviewer tools are a suite of tests and utilities to help you reviewing packages. Checks that are run by default are equivalent to store default settings.

<h2 id="heading--install-the-tools">Install the tools</h2>

You first need to install the `bzr` package to fetch the source of the tools:

``` bash
sudo apt install bzr
```

Then, get the tools and set the Python path to the root of the tools directory:

``` bash
bzr branch lp:click-reviewers-tools
cd ./click-reviewers-tools
export PYTHONPATH=./
```

<h2 id="heading--run-checks-locally">Run checks locally</h2>

The `click-review` binary is the main tool to run checks on snaps. This command takes a path to a local snap as an argument. It does not run the snap but sanity checks its metadata and paths according to the general security policy.

``` bash
$ ./bin/click-review /tmp/foo-snap_1.0_amd64.snap
```

In case of success, `click-review` will print `pass`:

``` bash
$ ./bin/click-review /tmp/foo-snap_1.0_amd64.snap
/tmp/foo-snap_1.0_amd64.snap: pass
```

In case of failure, two categories of issues can be displayed: errors and warnings. Errors block a snap from being released (unless manually approved). For example:

``` bash
$ ./bin/click-review /tmp/foo-snap_1.0_amd64.snap
Errors
------
 - lint-snap-v2:confinement_classic
    (NEEDS REVIEW) confinement 'classic' not allowed
 - lint-snap-v2:confinement_classic_with_interfaces
    confinement 'classic' not allowed with plugs/slots
    https://launchpad.net/bugs/1655369
Warnings
--------
 - lint-snap-v2:meta_gui_desktop
    desktop interfaces (x11) specified without meta/gui/*.desktop. Please provide a desktop file via setup/gui/*.desktop if using snapcraft or meta/gui/*.desktop otherwise. It should reference one of the 'apps' from your snapcraft/snap.yaml.
```

You can then:

-   ask the uploader to remove or change the offending code
-   reject the snap
-   approve the snap anyway
-   edit its `snap-declaration` assertion to make it conform to your security policies or to adapt it to your users needs.

<h2 id="heading--edit-snap-declarations">Edit snap declarations</h2>

Snap declarations allow reviewers to enable specific features on snaps, such as allowing auto-connections of interfaces and setting up automated aliases for snap commands.

Their content needs to be provided in JSON through the reviewer form in the store. To ease this process, you can use the `bin/create-snap-declaration.py` script.

For example, the `network manager` snap declares: `slots: [ network-manager ]` and `plugs: [ firewall-control, network, network-bind, network-control, network-manager ]`. If all the plugged interfaces for network-manager are meant to be auto-connected and other snaps are to be allowed to connect to the network-manager slot, the reviewer would use:

``` bash
$ ./bin/create-snap-declaration.py --plug-auto-connection=network-control,firewall-control,network-manager,network,network-bind --slot-connection=network-manager
WARN: adding missing 'installation' for 'network-manager' from base decl
WARN: adding missing 'auto-connection' for 'network-manager' from base decl
slots:
{
  "network-manager": {
    "allow-connection": "true",
    "allow-installation": {
      "slot-snap-type": [
        "app",
        "core"
      ]
    },
    "deny-auto-connection": "true"
  }
}
plugs:
{
  "firewall-control": {
    "allow-auto-connection": "true"
  },
  "network": {
    "allow-auto-connection": "true"
  },
  "network-bind": {
    "allow-auto-connection": "true"
  },
  "network-control": {
    "allow-auto-connection": "true"
  },
  "network-manager": {
    "allow-auto-connection": "true"
  }
}
```

Then paste the output into the reviewer form.
