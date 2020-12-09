The system-user assertion is a permit by the brand for local system users to be created on its specified devices. The most common use case is for creating system users in the factory line or on first boot, by using a USB dongle which would contain this assertion.

The format is as follows:

``` text
type:           system-user
authority-id:   <authority account-id>
revision:       <int>
brand-id:       <account-id>
email:          <user e-mail>
series:         <list of series which should accept this assertion>
models:         <models which should accept this assertion>
name:           <optional person’s name>
username:       <system user name>
password:       <encoded password>
ssh-keys:       <list>
since:          <UTC datetime>
until:          <UTC datetime>
sign-key-sha3-384: <key id> # Encoded key id of signing key

<signature>                 # Encoded signature
```

The index is the tuple \<`brand-id`, `email`\>. `series` is as specified by the model assertion. These assertions must be signed by the brand.

The `password` header must be encoded and salted, following the format specified by [crypt](http://manpages.ubuntu.com/manpages/bionic/en/man1/crypt.1.html)(3). The `until` header is in this case required.

The simple addition of such assertions to a device assertion database should not be enough to trigger the user creation. This must be initiated explicitly (via `snap create-user`, or in the context of the auto-import mechanism for assertions from removable devices, which requires physical access to the device).

This would be an example of this assertion:

``` text
type: system-user
authority-id: 324hfanjkfqASdFQWfnawefhu8Jauhdj
brand-id: 324hfanjkfqASdFQWfnawefhu8Jauhdj
email: test@localhost
models:
  - pc
name: Default Test User
password: $6$OCvKy4w/Ppxp7IvC$WPzWiIW.4y18h9htjbOuxLZ.sjQ5M2hoSiEu3FpMU0PMdHQuQdBOqvk8p6DMdS/R/nU/rXidClD23CbSkSgp30
series:
  - 16
since: 2016-10-24T07:12:10+00:00
until: 2017-10-24T07:12:10+00:00
username: test
sign-key-sha3-384: kKd-kgxTJSR-wm5OT5M-gVxo4zv0Y19AAloJE4dq7C0QlbPsdbof0G5g0lCpg0J_

AcLBXAQAAQoABgUCWKrupwAKCRCuieT/1PiUiMbrD/0WcjG5Yw6kPZ4afE6vePpZfySvPLbguna+
kMUtTYulfIVNK+VMR3ZeNQfHhe/WGIlxXUWqfIlp6EZ4ha7LBeeHYgdq6cOOrapa53wboQTOTCDp
EZna2R5lDnlVlm06jKOoPhQZNi0P30GiS9e1xY0CNu/hzxLgwArHYj1hqof0zdu/A68GyfU9AWRe
ne4QGFIwWuQE8vksgbRuCoRiu0zamU6WKdby4pad7Q1SHUdwn8+0i0/zmgRnvDeBloACNWHRYFy3
PGjJ5ylIkXoBaIYGfYusdO+NvSUnLP4Rj6izsUbBIVeHyAqAkMz6y68NUpQupO4vqih3GSi1pqoj
H5+p/e5UzcB7hqB8YOrwTtp5EsTPq/AOD0bu9EZdVS5eIAkGuvR3MJX+uGvTAXCCFMIPIxcNTPwp
6HUiyQ3xMZL6FUg4ucxQH6jRZiZ5SMiuJ2OYl53f7v3MdFMe5lnYSw1nBger9nEXti3pdsS9Czpk
3WdVRWSjvMupJUmlCJUTNeLwPn2gYCiZpFJntIltRq3McKqh6WrhTozsoVsLjTyx7bQEHp00QycJ
1pfdMtVNbQKyF30dswc5TctzZEOY/YazMI8CqB3ZuCGyUpkJ0px2FgJQqVHYdtIDePp33aTy170A
5dHym5SUQZqowwHN/PSc7rTyGn4fJCdRpRsjj72uSQ==
```
