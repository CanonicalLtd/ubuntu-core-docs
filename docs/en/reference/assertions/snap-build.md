The snap-build assertion defines the basic properties of a snap at the time it was built by the developer. These are assertions are created and uploaded to the store with the command `snapcraft sign-build <snap>`.

Currently, the only usage of this assertion is to provide traceability between publisher and snap. It is not checked by snapd or other parts of the system.

The format is as follows:

``` text
type:              snap-build
authority-id:      <authority account id>
revision:          <int>
snap-sha3-384:     <sha3-384 digest as url-safe unpadded base64>
snap-id:           <snap-id>
grade:             <devel|stable>
snap-size:         <int>
timestamp:         <UTC datetime>
sign-key-sha3-384: <key id> # Encoded key id of signing key

<signature>                 # Encoded signature
```

The index is the digest of the snap blob, `snap-sha3-384`. `grade` tells us whether the snap can be published to the candidate or stable channel (if stable) or not (if devel).

An example for this assertion:

``` text
type: snap-build
authority-id: ouMZ22pMaY5EVwoLozfjM4fR31bko4yj
snap-sha3-384: UFLajZv9twDGKvqorGn7ddN_hMPuq0DNlh24VGblYQZSM7EzcLRKGdxdigi6DUti
developer-id: ouMZ22pMaY5EVwoLozfjM4fR31bko4yj
grade: stable
snap-id: xoHNzwxGwQ2D4rSZwI3DKsjtRuy9saeI
snap-size: 6840320
timestamp: 2017-02-20T12:50:12+01:00
sign-key-sha3-384: kKl-kgxTJSR-wm5OT5M-gVxo4zv0Y19AAloJE4dq7C0QlbPsdbof0G5g0lCpg0J_

AcLBXAQAAQoABgUCWKrX9AAKCRCuieT/1PiUiBwuD/0fEfBS1IZ62PS0kyRbUDxBEPN3gzgw6mhX
DaB5bxz2xJ/qQEGfoZdddH7Q45K1lr5pPVL99P2WFwFmdCvWsrcYuK2MZnzwhovD3oPATcFxTnj3
ZttXwW7hDVokdvOwLdyvEaBkuyyO6awidzxFISsqgiaK2DzAwzA6aSXIOaYGDAfALm/YdzvZsfrk
+jTswZy/jbWlZf0z8C0bQGa/euJAWtzRScucHEK4RxM2qq7hok6AIl9xDBalfZOdlyB01ReM+68z
KldXoDQlyvMygx63HTXkmirJjWAUiiQ+5rJz9/zn8j84gT+R7qBgWBRd0l9KCZjalEDlXaNjfau3
NZHJ3FgGqZLx7vkGpqExJEAEDcyiMHj7XV5WSsKTgtTSkE4LvicICYiy6IhgiQ/+2wADMnTRQY0U
2GuN3amiEQ9873aohlmmnJUUxpchNZHxgUSC0nUprmSgEUBNi4giRrD8Gs5Mji2cy9qk7yqxkXu0
NjagDzsTHYPo2XlV93cyNJwPbjlshmS0NA/wAJRiwq0AkqgCaZweHuriJeewFnfAg2wyCz1nfh9X
XWJSX+DR6tLnkZ1evRG9g0a8LzBtKwlrq7+sMAvxglAQlw3JZG/4yRw4NTZJQ4t4+9Eybzkf587f
SDhf+j3c0JNUgb9o8isewL2+LLcvgM8tTSAmRxYqTw==
```
