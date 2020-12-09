<h2 id="heading--introduction">Introduction</h2>

A Brand Store provides snaps (via [channels](https://docs.snapcraft.io/reference/channels)) to systems that are configured to use the store and that are authenticated by the store. There are several parts of this system. This guide explains conceptually what these parts are and how they fit together.

Let’s start by taking a look at Ubuntu Single Sign On (SSO) accounts.

<h2 id="heading--ubuntu-sso-accounts">Ubuntu SSO accounts</h2>

An Ubuntu SSO account grants a logged in user a set of privileges. For example, a Brand Store is administered by an SSO account. Developers upload snaps to a Brand Store under an SSO account. Ubuntu [assertions](assertions) are signed by keys associated with an SSO account. SSO accounts also authenticate users to launchpad.net, forum.snapcraft.io, and additional sites and services.

You can create an SSO account here: https://login.ubuntu.com/

You can then see additional account data here: <https://dashboard.snapcraft.io/dev/account/>

Note the snap account-id. This is a critical identifier that is used in many parts of the Brand Store ecosystem. It is often referred to simply as the account-id.

<h3 id="heading--brand-sso-account">Brand SSO account</h3>

The first step in creating a Brand Store is creating a dedicated Brand SSO account. When requesting the Brand Store, you provide the Brand Store SSO account to Canonical in order to make that account the Brand Store administrator. (You can add other administrators later).

[note]
It is strongly recommended to dedicate the Brand Store SSO account to a single purpose of Brand activities. It should not be used for any other purposes. It should be considered a critical project asset and its use be strictly limited and controlled. (You can always create SSO accounts for these other purposes, although each SSO account requires a unique email address.)
[/note]

There are several activities that must be done under the authority of this Brand SSO account, including:

-   Brand Store administration
-   Generating keys used to sign assertions, such as the model and serial assertions
-   Gadget snap publishing (if you are using a non-Canonical gadget snap)
-   Kernel snap publishing (if you are using a non-Canonical kernel snap)

These activities are central to managing your Brand Store, your images, and your devices and are therefore considered brand activities.

The Brand Store and its Brand Store SSO account:

![image](https://assets.ubuntu.com/v1/90807b1f-brand-store-1.png)

<h3 id="heading--brand-sso-keys">Brand SSO Keys</h3>

Keys created by the Brand Store SSO account are normally used to sign the model assertion included in every image that is configured to point at a Brand Store. You also need to generate a special serial key that is uploaded to the Serial Vault, which provides each device a signed serial assertion that is used to authenticate the device to the Brand Store.

[Snapcraft](https://snapcraft.io) (the tool used to build snap packages), uses SSO accounts too. For example, you use `snapcraft login` to connect your terminal session to an SSO account. Then you can generate (`snapcraft create-key NAME`) and register (`snapcraft register-key NAME`) keys under that account.

[note]
It is technically possible to use different SSO accounts to administer your Brand Store and manage model specific activities such as signing the model and uploading the model’s custom gadget snap. Using a single Brand SSO account simplifies things and is therefore recommended in many situations.
[/note]

You use a Brand SSO account to create keys used for brand activities:

![image](https://assets.ubuntu.com/v1/83399278-brand-store-2.png)

<h2 id="heading--assertions">Assertions</h2>

[Assertions](assertions) are signed pieces of information. That is, an assertion provides data, and the entire statement is signed by a key bound to an Ubuntu SSO account. This allows the source of the information to be authenticated and the information it contains to be validated.

The snap system uses assertions in many places. For example, the model assertion (discussed next) provides key information defining the device **model**. It is signed by a key under a Brand SSO account. This means it can be authenticated and the data it contains can be relied on as being correct.

<h2 id="heading--models">Models</h2>

Every system that is connected to a Brand Store contains a signed **model assertion**.

[note]
Any system that does not have a customized model assertion cannot use a Brand Store. (Classic systems with no custom model assertion now have a generic one that points at the Ubuntu Store.)
[/note]

The model assertion contains data such as:

-   The Brand Store it points to
-   The model name
-   The Brand Store SSO account-id
-   The gadget snap name (and on Core systems, the kernel snap)
-   And more

As noted, the model assertion is **signed** by a key generated under the Brand Store SSO account and is placed in an image. The image is then ready to go to production and distribution. (When you create an image using the `ubuntu-image` tool, the model assertion and various other required assertions are inserted into the image automatically. If you manually create an image, you need to take these steps yourself. See [Seeding a Classic Image](seeding-classic-image).)

[note]
As mentioned above, it is strongly recommended to use the Brand Store SSO account to generate the key that signs the model assertion. This document assumes you use the Brand Store SSO account for all such purposes.
[/note]

Using a Brand SSO account key to sign a model used in an image:

![image](https://assets.ubuntu.com/v1/c683de7a-brand-store-3.png)

<h2 id="heading--systems-pointing-at-the-brand-store">Systems pointing at the Brand Store</h2>

Here we overview what has been discussed so far:

-   Systems contain a model assertion.
-   The model assertion contains the account-id of the Brand SSO account and was signed by a key made under that account.
-   The model assertion points at the Brand Store.

[note]
This picture still does not show the required system authentication through which a system may gain access to its Brand Store.
[/note]

Its model points at a Brand Store are rejected unless authentication via a Serial Vault is also used:

![image](https://assets.ubuntu.com/v1/6a584f30-brand-store-4.png)

-   Models point systems to their Brand Store
-   Systems can be rejected by the Brand Store as being unauthenticated

<h2 id="heading--authentication-through-the-serial-vault">Authentication through the Serial Vault</h2>

A normally configured Brand Store authenticates a system trying to access it. Authentication is done based on an authentication token called a **macaroon** (see figure below). The macaroon is provided by the Canonical **Authentication Service**, included in the Store API, after the device has obtained a signed serial assertion. Without a proper serial assertion, the device cannot obtain the macaroon and is rejected by the Brand Store. A proper signed serial assertion is given to a system by the **Serial Vault**. Obtaining a signed serial assertion from the Serial vault normally occurs when the system first boots up.

This process involves a couple of pieces:

-   The Serial Vault needs to be configured to support the model. This setup includes the snap account-id of the Brand SSO account, a model name (which must be used in the model assertion mentioned previously), and a key generated by the Brand SSO account. Setting up the Serial Vault configuration currently requires communicating with Canonical.
-   The system needs a gadget snap that has a prepare-device hook script that points the device to the Serial Vault. This hook script sets a few snapd variables: One has the URL for the Serial Vault, another defines the device’s serial number.

On first boot (or soon thereafter), a system obtains a signed serial assertion from its Serial Vault, uses it to obtain the macaroon from the Authentication Service, is authenticated by the Brand Store its model points to, and then uses the Brand Store’s channels to find and install snaps:

![image](https://assets.ubuntu.com/v1/0d9651a1-brand-store-5.png)

-   Gadget points at Serial Vault
-   Model points at Brand Store
-   Brand Store authenticates system by macaroon
-   Snaps are then installed over channels

```{=html}
<!-- LINKS -->
```
```{=html}
<!-- IMAGES -->
```
