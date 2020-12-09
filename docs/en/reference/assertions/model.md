The model assertion is a statement by a brand about the properties of a device model. It should contain all information needed to create an Ubuntu Core image.

The format is as follows:

``` text
type:              model
authority-id:      <authority account id>
revision:          <int>
series             <string>
brand-id           <account id>
serial-authority   <list<string>> # optional list of serial authorities. Use “generic” to have the snap store generate a serial.
model              <model id>
classic            <true|false> # optional
store              <string> # optional
display-name       <descriptive string>
architecture       <debian architecture name>
gadget             <gadget snap name>
kernel             <kernel snap name>
required-snaps     <string>
timestamp          <UTC datetime>
sign-key-sha3-384: <key id> # Encoded key id of signing key

<signature>                 # Encoded signature
```

The index for this assertion is the tuple \<`series`, `brand-id`, `model`\>. `series` allows the brand to define which release of the platform the device uses. “rolling” is the name of the development series that bridges stable series, which have names like “16” or “18”. `brand-id` is the account id of the brand, and `model` is a string that identifies a set of devices as desired by the brand.

The (optional) `classic` flag tells us if this is an all-snap system (false) or not (true). If not set, `architecture`, `gadget`, and `kernel` are mandatory. If set, `kernel` is forbidden and `architecture` and `gadget` are optional. If not present, an all-snap system is assumed.

The optional `store` header specifies a particular branded virtual store to be used for this model. The store in question has custom content specific to that model, curated by that brand, and managed by the store owner or the relevant brand. Each model knows which store it needs to speak to in order to get the appropriate content. If left out the device defaults to use the main Ubuntu store.

`gadget` and `kernel` specify respectively the gadget and kernel snaps used to build the device images. `required-snaps` (optional) is a list of snaps that must be installed at all times, and cannot be removed from the device.

This assertion is needed when [creating an Ubuntu Core device image](../../guides/build-device/image-building.md).

For instance, this is an Ubuntu Core model assertion for amd64 architecture:

``` text
type: model
authority-id: canonical
series: 16
brand-id: canonical
model: pc-amd64
architecture: amd64
gadget: pc
kernel: pc-kernel
timestamp: 2016-08-31T00:00:00.0Z
sign-key-sha3-384: 9tydnLa6MTJ-jaQTFUXEwHl1yRx7ZS4K5cyFDhYDcPzhS7uyEkDxdUjg9g08BtNn

AcLBXAQAAQoABgUCV9A82wAKCRDgT5vottzAEhq1D/4z66k0JS7sQrD54Ccros3HaAABF+7KwGqV
ggg6Mk+N2QKNxpl7fxHeyB82KUy49v4Kp8cg4icPUfrZb1DyzjgyuJIzZfCp1+LLQ4ShJ0ZW9MLW
p7r/FbITtbmGlCKjVtaSwLYTkZNfae/MTTuTB1nLXH939vdicRPtRQ1MsoQ6v8wUYeE4/F+SUxL9
ekYf4G8sz+vzcO5BK9+1T3Wo/aLHDi0N4EOS3K4ia1BVITZKvyeIUEHOLQJAHKk43dAL0PqMFW+W
IHhDXQoUeiURBfy6zcrRynaIj5tzlhFmJ3pjlmLQLlVCeGJ4yuZ6xb0YIl+oHpYzZrxTad2mEMUY
si4qIyxVNGj7LZCloLRsDFBMh8RS9a8L0/Cq3hA2Q1Ugyw2D5U7J427SVYCDS9rrihNVvMFscou6
vrZHMnAVl/F/TRUDYy29idiiibBQU02D1l4Qu7QnDQQCZygq1n+aeW5ZPwtF/KclkJm0YRUkqbtR
FG2TYLmQ06MPmRuqVRaAdjfhnZ9YtFBDhI+obn99q/OmG2e7d4WNU3JPG1h5arIQGNeR9kVzBER1
iO0V3iYjD0DxOsd2QVOdI/o8HqCRfycTMo/7TydVdWKXKpKdzeezfz/df2LRDCE712NVFhY0hDC6
BvV4mMoqS17K7OMHfDohh0DFfp0yFl9oYfLY55G5HA==
```
