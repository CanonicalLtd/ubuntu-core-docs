The validation assertion tells us that a certain revision for a snap that is gated by another snap has been validated for a given series. It is closely related to the [snap-declaration](snap-declaration.md) assertion.

The format is as follows:

``` text
type:                   validation
authority-id:           <authority account-id>
revision:               <int>
series:                 <list of series which should accept this assertion>
snap-id:                <snap-id>
approved-snap-id:       <snap-id>
approved-snap-revision: <int>
timestamp:              <UTC datetime>
revoked:                <bool>
sign-key-sha3-384: <key id> # Encoded key id of signing key

<signature>                 # Encoded signature
```

The index is the tuple \<`series`, `snap-id`, `approved-snap-id`, `approved-snap-revision`\>. This assertion means that refreshing to revision number `revision` of snap `approved-snap-id` for `series` has been aproved by `authority-id`, being `snap-id` the snap that was gating the update, of which `authority-id` must be the owner. `approved-snap-id` must be part of the `refresh-control` list in the snap-declaration assertion of `snap-id` for this to be enforced.

In other words, the recomended revision for `approved-snap-id` in a system that has `snap-id` installed is the one in the `revision` header. There will be no automatic installation of newer revisions until a validation with a newer revision is released by the store. Note however that this does not forbid a forced update by the device owner.

The validation can be revoked by using the optional flag `revoked`.

An example of this type:

``` text
type: validation
authority-id: canonical
revision: 1
series: 16
snap-id: kkOOPWIl0sF7FoSA0KRTt83b1eoynkBa
approved-snap-id: JIpOmfrI0JpaN3uNQQgNv5x3fW06nOYX
approved-snap-revision: 37
timestamp: 2017-02-20T10:23:51+00:00
sign-key-sha3-384: C9mhxTpowHTXM3HOwgg3ZCX-WD05CczlNMdrCBbl2l0d4J_CcjYBS8NQpI-TtQlL

AcLBXAQAAQoABgUCWKrI2QAKCRC03QWqtcJXZRFbEACDcCy15wPNpjcJyFYEt05A4WWzGPQMlC09
1lv62WAancrRaenLOi3xmeIPgsuPk9xKgrn16dBlytCK5GUHiHsafR+34mt/ridaVSKY5zCIkvcJ
mgIH3jMB1RZiY2gtlxXD14G+nK67PKvM6BBfMdBB9ItLAdh5LM3ycbE/8ZqY7aYuTLKfOIPYGjUY
2rRlOaCggeXsru9wbnNwCwGBHZ9ltBgsISZQiSMGnH38OMNgjNOQy0PIB+elmYLEoTKXc+qU1x5w
/R8cwUzWwcd56Ty/WpGdAfBrpO4/afFgVtGrixEWtVigIoIigPb7wytmQYcnl/Y0bQMVNZiYFQdF
tZ9MGGTzA/ieRrjJHOQEj+VfDA4rHU2vUTGlZtseukhGNhSNgI52zhDyynhB/Hxy1t0rJ8eJju2a
G27p2GuJvGrxfjxcge47LZ0WJaKe9R82/AsrFLlnJ4d29K0RUPnYohwlDwWTAOTE2SAz6/kIRNIv
sOuHdIXIMarsbbRIG1kC++Rl/XI9jWvoM++fT1kHCjXgKfi+CELo8YcOKHPbjWSd7DiAmSnR5I6f
ovydzkMPTzzbsxyKePQHicKiqPs4dSfcEnPIGTpvgpGE4WEI3zYGYmsGgFOJqEnuy0IvVRCtJXU4
yk/p3Jqwss3W0YbfypCWrvMBxho1mN5SRhaQv3r+kw==
```
