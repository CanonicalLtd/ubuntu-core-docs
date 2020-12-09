The snap-declaration assertion defines some of the properties of the snap, such as the snap-id, the official name, the publisher, and so on. This assertion is downloaded along the snap when installing a snap from a store and has information on actions to take when performing the installation.

The format is as follows:

``` text
type:               snap-declaration
authority-id:       <authority account id>
revision:           <int>
series:             <series this assertion was created for>
snap-id:            <string>
snap-name:          <string>
publisher-id:       <the owner of this snap-id space>
timestamp:          <UTC datetime>
refresh-control:    <list of snap-ids that have gated updates>
  - [snap-id1]
  - [snap-id2]
  - ...
auto-aliases:       <optional list of aliases that are automatically enabled>
  - [alias1]
  - ...
plugs:              <map from [interface] to plug side rules>
  [interface]:      <optional plug side rules for [interface]>
    allow-installation:     <true|false>
    deny-installation:      <true|false>
    allow-connection:       <true|false>
    deny-connection:        <true|false>
    allow-auto-connection:  <true|false>
    deny-auto-connection:   <true|false>
  ...
slots:              <map from [interface] to slot side rules>
  [interface]:      <optional slot side rules for [interface]>
  ...               <similar options as rule entries for plugs>
sign-key-sha3-384: <key id> # Encoded key id of signing key

<signature>                 # Encoded signature
```

The index is the tuple \<`series`, `snap-id`\>. `snap-id` is a key with the same format as the account ids.

This assertion gives control on several aspects of the snap behaviour to the the authority:

-   `refresh-control` gives a list of snaps that are gated when the one specified by `snap-id` is installed, so they are not automatically refreshed until they are "validated". Validation is performed by using [validation asserts](validation.md), which specify the revision of the gated snap that should be installed if the gating snap has been installed.

-   `auto-aliases` gives a list of the aliases that we want to automatically enable when installing the snap. Aliases provide short names for applications contained in the snap, so we do not need to use the full command name `<snap-name>`{=html}.`<command>`{=html}. They are defined inside snap.yaml, but they are not activated by default unless included in the `auto-aliases` list. Having this inside a signed assertion lets snapd create the aliases only if curated by a trusted authority.

-   `plugs` and `slots` define flags per interface. This lets define restrictions on how the snap plugs/slots used by the snap are handled. For instance, we can allow or deny connections with `allow-connection` and `deny-connection`. With `allow-auto-connection` or `deny-auto-connection` we let snapd know if it should automatically connect plugs/slots on snap installation.

snap-declaration assertions for installed snaps can be seen by issuing `snap known snap-declaration`.

As an example, for the modem-manager snap from the Ubuntu store we have:

``` text
type: snap-declaration
format: 1
authority-id: canonical
revision: 9
series: 16
snap-id: KtwxgRlwCAVKFw92BUdt1WloH1Va3QPo
plugs:
  modem-manager:
    allow-auto-connection: true
publisher-id: canonical
slots:
  modem-manager:
    allow-connection: true
snap-name: modem-manager
timestamp: 2016-10-25T15:35:43.646671Z
sign-key-sha3-384: BWDEoaqyr25nF5SNCvEv2v7QnM9QsfCc0PBMYD_i2NGSQ32EF2d4D0hqUel3m8ul

AcLBUgQAAQoABgUCWA970AAABF0QAMw+M28Rrm0m/3Gm5PYesQcQWKhGwmN0j3qfYG2LsSRiM0TU
j7K7hvCPc9v0P4sL6Ewv/CEZAkVxPYd9eUMqiyKYBRMp9QeiL7KW3RWdHok0FUN7ia7ZxcPlpKoM
uwV7qYDKktw/TJWX9bK15W6DnghlKtU464u7IqcHVmH2YzPBbcpJBuIhLHgYC2K5oj3ZvIjHqnV/
ELRDtwW3UTTkonycc2IUTCd10qu590z7DWzORWdts9ZARBJXfc3lohYkSd1v4wDYZHRO9RF/bJix
LBALp3kUR6X3OnLLJQAjVhIEY70B/5kLApuhrOpmi84Uawf+Uh91Ze++Bwatrw6QGw9cwkFgoLaj
9neiV4y6HvQh7gsgXap1XOZeOeWVMISgqaXGER78Lx6nc6/Loz8Yhjp4p9xi2Ia4j7fLpXMkWIU4
aoGudS1hQBsbeiNQvG6I+DraMN7xypMbOkGKwqNJ7prU63D3BmZiFl17ajT3SfffEO1/H6qqRVFS
A8X9HXVGPmI2TGst36cBgjdd9f+jj9ZqISKs8jdHfPKEpOBdH4wo1rodXO1y/GxZeP2Z710qep4t
8ynSRPi0l3boyM15D3IfnXMjLzUoace9vC6gltOHpW8GFPZvheQwknRvtfwRpZM2VsgaSw6cuz3+
7K/m9/Ff04A86/gvRlzduXIjEvKJ
```
