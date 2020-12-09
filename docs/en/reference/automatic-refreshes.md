Installed snaps are automatically refreshed a few times a day, assuming a different revision is available on the channel being tracked.

More details about when it is to be triggered may be obtained via:

``` bash
$ snap refresh --time
timer: 00:00~24:00/4
last: today at 09:08 BST
next: today at 15:09 BST
```

Check the state of recent updates with:

``` bash
$ snap changes
ID   Status  Spawn               Ready               Summary
870  Done    today at 09:08 BST  today at 09:10 BST  Auto-refresh snap "core"
```

and to view the details for a specific update, pass its ID to the *change* command:

``` bash
$ snap change 870
Status  Spawn               Ready               Summary
Done    today at 09:08 BST  today at 09:08 BST  Ensure prerequisites for "core" are available
Done    today at 09:08 BST  today at 09:08 BST  Download snap "core" (7999) from channel "edge"
Done    today at 09:08 BST  today at 09:08 BST  Fetch and check assertions for snap "core" (7999)
Done    today at 09:08 BST  today at 09:08 BST  Mount snap "core" (7999)
Done    today at 09:08 BST  today at 09:08 BST  Run pre-refresh hook of "core" snap if present
Done    today at 09:08 BST  today at 09:08 BST  Stop snap "core" services
Done    today at 09:08 BST  today at 09:08 BST  Remove aliases for snap "core"
Done    today at 09:08 BST  today at 09:08 BST  Make current revision for snap "core" unavailable
Done    today at 09:08 BST  today at 09:08 BST  Copy snap "core" data
Done    today at 09:08 BST  today at 09:09 BST  Setup snap "core" (7999) security profiles
Done    today at 09:08 BST  today at 09:09 BST  Make snap "core" (7999) available to the system
Done    today at 09:08 BST  today at 09:10 BST  Automatically connect eligible plugs and slots of snap "core"
Done    today at 09:08 BST  today at 09:10 BST  Set automatic aliases for snap "core"
Done    today at 09:08 BST  today at 09:10 BST  Setup snap "core" aliases
Done    today at 09:08 BST  today at 09:10 BST  Run post-refresh hook of "core" snap if present
Done    today at 09:08 BST  today at 09:10 BST  Start snap "core" (7999) services
Done    today at 09:08 BST  today at 09:10 BST  Remove data for snap "core" (7977)
Done    today at 09:08 BST  today at 09:10 BST  Remove snap "core" (7977) from the system
Done    today at 09:08 BST  today at 09:10 BST  Clean up "core" (7999) install
Done    today at 09:08 BST  today at 09:10 BST  Run configure hook of "core" snap if present
Done    today at 09:08 BST  today at 09:10 BST  Run health check of "core" snap
Done    today at 09:08 BST  today at 09:10 BST  Consider re-refresh of "core"

......................................................................
Make snap "core" (7999) available to the system

2019-10-14T09:09:13+01:00 INFO Requested daemon restart.

......................................................................
Automatically connect eligible plugs and slots of snap "core"

2019-10-14T09:09:13+01:00 INFO Waiting for restart...
2019-10-14T09:10:00+01:00 INFO cannot auto-connect slot core:bluez to bluez:client, candidates found: core:bluez, bluez:service

......................................................................
Consider re-refresh of "core"

2019-10-14T09:10:06+01:00 INFO No re-refreshes found.
```

For more details on *snap refresh*, see [Keeping snaps up to date](https://snapcraft.io/docs/keeping-snaps-up-to-date).
