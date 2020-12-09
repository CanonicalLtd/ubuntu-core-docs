The account assertion ties a name for an account in the snap universe to its identifier and provides the authority's confidence in the name's validity.

It is created on user creation by the store, and kept there while the account exists.

The format is as follows:

``` text
type:              account
authority-id:      <authority account id>
revision:          <int>
account-id:        <account id>
display-name:      <friendly name for the account>
username:          <store user name or nick>
validation:        <uproven|certified>
timestamp:         <UTC datetime>
sign-key-sha3-384: <key id> # Encoded key id of signing key

<signature>                 # Encoded signature
```

The index for this assertion is the `account-id`. Validation `certified` means that the authority is confident in that the display name accurately describes the owner of the account, while `unproven` means that no checks have been performed on that. Header `username` is optional.

Running `snap known account` gives us a list of the account assertions that are present in the system. When a snap is installed, the owner's account assertion is downloaded and added to this list.

An example for this assertion:

``` text
type: account
authority-id: canonical
revision: 14
account-id: mmOOPWIl0sL7FoSA0KRTt83b1eoynkBa
display-name: John Smith
timestamp: 2017-02-20T10:23:51+00:00
username: jsmith
validation: unproven
sign-key-sha3-384: C9mhxTpowHTXM3HOwgg3ZCX-WD05CczlNMdrCBbl2l0d4J_CcjYBS8NQpI-TtQlL

AcLBXAQAAQoABgUCWKrNXwAKCRC03QWqtcJXZZvwD/9GhSwf9GjiVj9k+x3zVvnHpCPeHDJT0MSJ
yRsshygdh2njY7o7MWnQbQ3ThOvJsRMAayQejkLd6ro9/gyapJdafCVrTD6/yPrIfN8LaekVGmTL
tIDluOfQPgboR1a3wA/zXSo077tmbsYTjiZSo73ubNLrEeHi92x7GsECdRVp8B8wPTbgzZUWZOo3
/p8tXkot7wxqLsjgHbT+DlNSXe491ABM4TxVVYsSltUv+h+GAAfdvV2hMoPENLIVukqkGFq1XRGf
zBlfODbNRIXoUQ3eFPThYfYdDEVlxF4cKDt2yt9Wcngs31ti5JdGJ7kqL6Hp5rI/Gkdm2dCdvLhf
+9GDYt1+O9wVSfsZBgcy/shqSwHzP/GyBdPysSRNOSSiS/YFQiTjMWScNM9I1j0oyohlgnV6AOVa
MeaJFf1Kv90IWL7SP81VZuyqUWcohwuQ5JtphMat2uFqRL0pgH5+btmiWiHCqLqdNJJBIcCpSG+t
Iw1Z27hBm8cmYJW2IFop/fhZxEqggEmzh5hooIuk67he/FLuINuOb4qDlD+hqp6vSz5S9Br/0Gu/
/0Vr/YCLo9b0xRIeAVZGHxhJMxrELYkGpBv8woaK3hKT53jPxX9tTUwQazYzMhqwZlZSBSFQtnSq
aVrlphWrz4MOmWZ+6HJSx6Qc4p31K/QIVSip14fUYw==
```
