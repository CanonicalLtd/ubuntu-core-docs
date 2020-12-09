<h2 id="heading--what-is-refresh-control">What is Refresh Control?</h2>

Refresh Control is a mechanism for controlling the revisions of a snap that will be offered as updates to a device. A *gating snap* can control the revisions of a *gated snap* so that, if the *gating snap* is installed on a device, only revisions of the *gated snap* which have been validated by the publisher of the *gating snap* can be installed or refreshed to.

A gating snap can control revisions of one or more gated snaps. Similarly, a gated snap can require verification from more than one gating snap, and a particular revision of the gated snap will only be installable or refreshable to, if it satisfies the constraints of all its gating snaps. However, because it can be confusing to device operators, use of multiple gating snaps to control one specific gated snap is not recommended. The best practice is to use a single gating snap for each gated snap.

This is especially useful for device manufacturers to mediate releases of the *core* snap, allowing time for testing and validation of new revisions on their device models and compatibility with their software. Note this is separate from the ability for device managers to control the schedule of refreshes and revision control via a snap proxy.

<h2 id="heading--preliminary-setup">Preliminary setup</h2>

Establishing the relationship between a gating and gated snap is required only once and can only be performed by a user with reviewer permission for the gating snap's store.

Ask a store reviewer to edit the package declaration for the gating snap on the [snap's page](https://dashboard.snapcraft.io/snaps/whatever/). The reviewer should:

-   Click on "review capabilities"
-   Under "Refresh Control" put a json list with the snap IDs of one or more snaps you want gated: ["xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", ...]. Keep in mind this gating snap will be able to control updates for all the gated snaps.

<h2 id="heading--gating-snap-publishers-signing-key-setup">Gating snap publisher's signing key setup</h2>

Since gating involves pushing [`validation` assertions](https://docs.ubuntu.com/core/en/reference/assertions/validation) which are signed by the publisher indicating their authorization to restrict updates of snaps, some GPG keys must be generated and published to the store. If a store key already exists, it can be used. If not, one must be generated and registered.

1.  `snapcraft create-key` to create a key. (by default it's named "default" but a key name can be given to create a custom one so e.g. each snap is signed with its own key). Do not forget the passphrase used to secure the key or misplace the key files.

        $ snapcraft create-key my-key
        Passphrase:
        Confirm passphrase:
        $ snapcraft list-keys
            Name          SHA3-384 fingerprint
        -   my-key       mfomTtPB1cE3IFs51NtdnFoPlQwJxFgxOMU_q0mPkH7M2gKB-4m28d99XrVjA53B  (not registered)

2.  `snapcraft register-key <the-key-name>` to register it with the assertion server.

        $ snapcraft register-key my-key
        Enter your Ubuntu One e-mail address and password.
        If you do not have an Ubuntu One account, you can create one at https://dashboard.snapcraft.io/openid/login
        Email: roadmr.developer@example.com
        Password:
        Second-factor auth: 111111
        Registering key ...
        Done. The key "my-key" (mfomTtPB1cE3IFs51NtdnFoPlQwJxFgxOMU_q0mPkH7M2gKB-4m28d99XrVjA53B) may be used to sign your assertions.

<h2 id="heading--example-gating-workflows">Example gating workflows</h2>

At this point, the publisher can issue validations asserting which revision of the gated snap is allowed to be installed when the gating snap is installed on a device. For example, specify that if the roadmr-gating snap is installed, the roadmr-gated snap can only be installed or refreshed to revision 2.

``` text
$ snapcraft validate roadmr-gating roadmr-gated=2 --key-name my-key
Getting details for roadmr-gated
Signing validations assertion for roadmr-gated=2
```

This generates the assertion and pushes it to the assertions service. A copy of the assertion will also be left on the current directory.

``` text
type: validation
authority-id: Q8HSISyvcdrniz52oa8Qjh6HlZwv0wiT
series: 16
snap-id: gs2epiCF5LPioNKnajAOTiBhOogG26RN
approved-snap-id: xZkR9MIMbusYipfJ80DGWf1nPKkD77hk
approved-snap-revision: 2
revoked: false
timestamp: 2018-05-30T21:00:04.092765Z
sign-key-sha3-384: mfomTtPB1cE3IFs51NtdnFoPlQwJxFgxOMU_q0mPkH7M2gKB-4m28d99XrVjA53B
```

To verify this was pushed up to assertions service:

``` text
$ snap known --remote validation series=16 snap-id=gs2epiCF5LPioNKnajAOTiBhOogG26RN approved-snap-id=xZkR9MIMbusYipfJ80DGWf1nPKkD77hk approved-snap-revision=2
type: validation
authority-id: Q8HSISyvcdrniz52oa8Qjh6HlZwv0wiT
series: 16
snap-id: gs2epiCF5LPioNKnajAOTiBhOogG26RN
approved-snap-id: xZkR9MIMbusYipfJ80DGWf1nPKkD77hk
approved-snap-revision: 2
revoked: false
timestamp: 2018-05-30T21:00:04.092765Z
sign-key-sha3-384: mTVMTtPB1cE3IFs51NtdnFoPlQwJxFgxOMU_q0mPkH7M2gKB-4m28d99XrVjA53B
....
```

As described earlier, this means that, if the gating snap is installed on the system, the gated snap can *only be installed or refreshed to the approved revision* (2 in this case). If the gating snap is not installed on the system, the gated snap is not restricted and will be installable or updatable to whichever revision is available in the requested channel. The revision of the gating snap is not relevant here; what's relevant is the latest validation assertion which tells which revision of roadmr-gated is allowed.

The snapcraft gated command can tell you which snaps are gated by a particular snap:

``` text
$ snapcraft gated roadmr-gating
Name                Revision  Required    Approved
roadmr-gated        2         True        2018-05-30T21:00:04Z
```

Here we can see roadmr-gating, if installed, will constrain roadmr-gated to revision 2 (or nothing at all, if rev2 is not installable).

At this point one can install the gating snap:

``` text
$ snap install roadmr-gating
roadmr-gating 2018-05-30-01 from 'snapdeveloper' installed
$ snap list roadmr-gating
Name           Version        Rev    Tracking    Developer      Notes
roadmr-gating  2018-05-30-01    1    stable      snapdeveloper  -
```

Note that trying to install the gated snap fails:

``` text
$ snap install roadmr-gated
error: snap "roadmr-gated" not found
```

Even though it is indeed available on stable (but at revision 1, not the validated one, which would be 2):

``` text
$ snap info roadmr-gated
name:      roadmr-gated
publisher: snapdeveloper
snap-id: xZkR9MIMbusYipfJ80DGWf1nPKkD77hk
channels:
  stable:    2018-05-30-02 (1) 4kB -
```

Once revision 2 is published...

``` text
$ snap info roadmr-gated
name:      roadmr-gated
publisher: snapdeveloper
snap-id: xZkR9MIMbusYipfJ80DGWf1nPKkD77hk
channels:
  stable:    2018-05-30-03 (2) 4kB -
```

...installing can proceed.

``` text
$ snap install roadmr-gated
roadmr-gated 2018-05-30-03 from 'snapdeveloper' installed
$ snap list roadmr-gating roadmr-gated
Name            Version         Rev     Tracking    Developer        Notes
roadmr-gated    2018-05-30-03   2       stable      snapdeveloper    -
roadmr-gating   2018-05-30-01   1       stable      snapdeveloper    -
```

If at this point revision 3 of the gated snap is published...

``` text
$ snap info roadmr-gated
name:      roadmr-gated
publisher: snapdeveloper
snap-id:   xZkR9MIMbusYipfJ80DGWf1nPKkD77hk
tracking:  stable
refreshed: 2018-05-30T21:08:53Z
installed:   2018-05-30-03 (2) 4kB -
channels:
  stable:    2018-05-30-04 (3) 4kB -
```

...snap refresh will do nothing because even though v3 is available, it's not validated.

``` text
$ snap refresh
All snaps up to date.
$ snap list roadmr-gating roadmr-gated
Name            Version         Rev Tracking    Developer        Notes
roadmr-gated    2018-05-30-03   2   stable      snapdeveloper    -
roadmr-gating   2018-05-30-01   1   stable      snapdeveloper    -
```

Validation is independent of snap release/publishing. For example, we can validate the existing gated snap v3 with the existing gating snap at revision 1 (meaning no update of gating snap is needed):

``` text
$ snapcraft validate roadmr-gating roadmr-gated=3 --key-name my-key
Getting details for roadmr-gated
Signing validations assertion for roadmr-gated=3
```

At this point a refresh will take gated-1 to rev 3 which was validated.

``` text
$ snap refresh
roadmr-gated 2018-05-30-04 from 'snapdeveloper' refreshed
$ snap list roadmr-gating roadmr-gated
Name            Version       Rev    Tracking   Developer        Notes
roadmr-gated    2018-05-30-04 3      stable     snapdeveloper    -
roadmr-gating   2018-05-30-01 1      stable     snapdeveloper    -
```

<h2 id="heading--gating-multiple-snaps">Gating multiple snaps</h2>

A validation can be issued to restrict the revisions of more than one gated snap (assuming the “publisher setup” was done to allow the gating snap to control the indicated gated snaps):

``` text
$ snapcraft validate roadmr-gating roadmr-gated=2 facundo-gated=5
Getting details for roadmr-gated
Getting details for facundo-gated
Signing validations assertion for roadmr-gated=2
Signing validations assertion for facundo-gated=5
```

In this example, if roadmr-gating is installed on a device, roadmr-gated will only install or refresh to revision 2, and facundo-gated will only install or refresh to revision 5.

<h2 id="heading--notes">Notes</h2>

-   At the moment, validations can only be issued to gate snaps which have a release on the stable channel *at the moment of creating the validation*. (There’s [a snapcraft bug for this](https://bugs.launchpad.net/snapcraft/+bug/1775658)). This is because `snapcraft validate` uses the details API to get the details from the stable channel only, and it doesn’t allow specifying another channel to get the gated snap’s details. Once the validation is in place, validations are channel-agnostic because they operate at the revision level.

```{=html}
<!-- -->
```
``` text
$ snapcraft status gated-1
Track    Arch    Channel    Version        Revision
latest    amd64  stable     -              -
                 candidate  -              -
                 beta       -              -
                 edge       2018-05-30-04  3

$ snapcraft validate gating-1 gated-1=3
Getting details for gated-1
Snap 'gated-1' was not found in the 'stable' channel.
```
