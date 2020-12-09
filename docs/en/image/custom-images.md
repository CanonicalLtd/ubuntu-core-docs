Building a bespoke image for a [Supported platform](../platforms.md) enables an Ubuntu Core device to be customised at the point of deployment. Customisation options include configuration for both hardware and software, specific kernels, and which snap packages to pre-install.

See [Image building](image-building.md) for an overview of building reference Ubuntu Core images for a supported platform.

```{=html}
<div class="p-notification--positive"><p markdown="1" class="p-notification__response"><span class="p-notification__status">Note:</span>
Building for non-reference architectures is also easily accomplished but
requires upstream enablement. For further details, <a
href="https://ubuntu.com/core#get-in-touch">contact Canonical.</a> </p></div>
```
<h2 id="heading--prerequisites">Prerequisites</h2>

To build a custom image, first use the *snapcraft* command to login to the [Snap Store](https://snapcraft.io/store):

``` bash
$ snapcraft login
```

Snapcraft can be installed with `sudo snap install snapcraft --classic`, see [Snapcraft overview](https://snapcraft.io/docs/snapcraft-overview) for further details, and visit [Create a developer account](https://snapcraft.io/docs/creating-your-developer-account) if you don't yet have an account.

Before creating a custom model assertion, you will need to retrieve your developer ID and generate a properly formatted timestamp. The *snapcraft* command can be used to retrieve your developer id:

``` bash
$ snapcraft whoami
email: <email>
developer-id: bJzr2XzZ56Qv6Z51HIeziXvxtn1XItIq
```

Use the following *date* command to output the correctly formatted timestamp for the model assertion:

``` bash
$ date -Iseconds --utc
2020-03-10T12:34:57+00:00
```

<h2 id="heading--custom-model-assertion">Custom model assertion</h2>

The following is a tweaked JSON-structured custom model assertion based on [ubuntu-core-18-amd64](http://cdimage.ubuntu.com/ubuntu-core/18/stable/current/ubuntu-core-18-amd64.model-assertion):

``` json
{
  "type": "model",
  "series": "16",
  "model": "ubuntu-core-18-amd64",
  "display-name":"Ubuntu Core 18 (amd64)",
  "architecture": "amd64",
  "kernel": "pc-kernel=18",
  "gadget": "pc=18",
  "base": "core18",
  "required-snaps": ["hello", "hello-world"],
  "authority-id": "bJzr2XzZ56Qv6Z51HIeziXvxtn1XItIq",
  "brand-id": "bJzr2XzZ56Qv6Z51HIeziXvxtn1XItIq",
  "timestamp": "2020-03-10T12:05:38+00:00"
}
```

We've saved the above example in a file called `my-model.json`, and it contains the following modified properties:

-   `base`: provides the run-time environment
    *core18* is the current standard base and as is built from [Ubuntu 18.04 LTS](http://releases.ubuntu.com/18.04/). See [Base snaps](https://forum.snapcraft.io/t/base-snaps/11198) for more details.
-   `authority-id`, `brand-id`: defines the authority signing the assertion
    reference assertions are signed by `canonical`. Non-reference assertions are signed by their [brand store](build-store/index.md). For a custom model assertion, this needs to be the developer ID.
-   `timestamp`: UTC formatted time and date
    used to denote the assertion's creation time.
-   `required-snaps`: one or more snaps to be pre-installed for deployment any snap can be listed here. If there's a dependency on a different base, such as *core*, this will be installed too.

For a complete list of model assertion keywords, see [Model assertion](../reference/assertions/model.md).

<h2 id="heading--signing-a-model-assertion">Signing a model assertion</h2>

The difference between building an image from a reference model assertion and building from a modified model assertion is that the modified model assertion needs to be digitally signed. This is accomplished in four stages:

1.  create a key
2.  export/register the key
3.  sign the model assertion
4.  build the image

```{=html}
<div class="p-notification--positive"><p markdown="1" class="p-notification__response"><span class="p-notification__status">Note:</span>
Rather than creating a key for every device, the same key is typically used across all models or model family.
</p></div>
```
First, sign in to the [Snap Store](https://snapcraft.io/store) (*snap login*) and check whether there is already a published key available. You can list any published snaps with the *snap keys* command:

``` bash
$ snap login
[...]
Login successful

$ snap keys
No keys registered, see `snapcraft create-key`
```

If you have no registered keys, create one as follows:

``` bash
$ snap create-key my-models
Passphrase:
Confirm passphrase: <passphrase>

$ snap keys
Name         SHA3-384
my-key-name  E-n0AOKPFjIyy4S_i9JxTT4tkuaZf7rP9D2ARCmBNXjlgTGDjL8euFSlb87U0NPl
```

With a key created, use the *snapcraft* command to upload and register it with the store:

``` bash
$ snapcraft register-key
Registering key ...
```

A custom model assertion is signed by piping the assertion through the *snap sign* command with the key name as its sole argument:

``` bash
$ cat my-model.json | snap sign -k my-key-name > my-model.model
```

The resulting `my-model.model` file contains the signed model assertion and can now be used to build the image.

<h2 id="heading--building-the-image">Building the image</h2>

With a [signed model assertion](#heading--signing-a-model-assertion), the Ubuntu Core image can now be built just like a reference image, using the [ubuntu-image](image-building.md#heading--building-with-ubuntu-image) command:

``` bash
$ ubuntu-image snap my-model.model
Fetching snapd
Fetching pc-kernel
Fetching core18
Fetching pc
Fetching hello
Fetching hello-world
Fetching core
WARNING: model has base "core18" but some snaps ("hello", "hello-world") require "core" as base as well, for compatibility it was added implicitly, adding "core" explicitly is recommended
```

As with when building a reference image, you can now use the image to boot either real or virtual hardware. See [Building the image](image-building.md#heading--building-with-ubuntu-image) for more details.

From within a running session on a custom image, you can run the pre-installed snaps:

``` bash
$ hello-world
Hello World!
```

Use *snap list* to see which snaps are installed:

``` bash
$ snap list
Name         Version       Rev   Tracking  Publisher   Notes
core         16-2.43.3     8689  stable    canonical✓  core
core18       20200124      1668  stable    canonical✓  base
hello        2.10          38    stable    canonical✓  -
hello-world  6.4           29    stable    canonical✓  -
pc           18-2          36    18        canonical✓  gadget
pc-kernel    4.15.0-88.88  399   18        canonical✓  kernel
snapd        2.43.3        6434  stable    canonical✓  snapd
```

The *snap known model* command will show the read-only custom model assertion used to build the image:

``` bash
$ snap known model
type: model
authority-id: bJzr2XzZ56Qv6Z51HIeziXvxtn1XItIq
series: 16
brand-id: bJzr2XzZ56Qv6Z51HIeziXvxtn1XItIq 
model: ubuntu-core-18-amd64
architecture: amd64
base: core18
display-name: Ubuntu Core 18 (amd64)
gadget: pc=18
kernel: pc-kernel=18
required-snaps:
  - hello
  - hello-world
timestamp: 2020-03-10T12:05:38+00:00
sign-key-sha3-384: 9aZR3b1UX9kqiVVxzfUrKYzYjHX-gC8jGNc4hTCpGfpPyaFdWR7K68HLoY1EH3yR
[...]
```
