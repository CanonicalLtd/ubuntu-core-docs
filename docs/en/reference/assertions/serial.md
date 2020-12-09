The serial assertion is a statement binding a device identity with the device public key.

Every Ubuntu Core device belongs to a particular model. It also has a unique device identity and the ability to prove that identity with cryptographic keys. The precise implementation of this cryptography may vary from device to device, based on the hardware and software capabilities of that device, and is part of the gadget snap.

The format is as follows:

``` text
type:               serial
authority-id:       <authority account id>
revision:           <int>
brand-id            <account id>
model               <model id>
serial              <unique id for the device>
device-key          <public key and type>
device-key-sha3-384 <digest of the device key>
timestamp           <UTC datetime>
sign-key-sha3-384:  <key id> # Encoded key id of signing key

BODY: map of hardware constraints/details  # expected to be YAML

<signature>                 # Encoded signature
```

The index is the tuple \<`brand-id`, `model`, `serial`\>, being `serial` a unique identifier for the device. `brand-id` and `model` must match an existing model assertion.

The crucial information provided by this assertion is the serial which is a unique identifier for this device in the context of the model. In other words, the brand should never assign the same serial to more than one device of the same model. This assertion must then be signed by the brand.

The assertion also provides a unique key for the device in the `device-key` header, being the format the same as for the key in the account-key assertion. A digest to identify it is also provided. The device key might change over time, in a controlled fashion, but at any given time there is only one device key per device.

The (optional) body will contain device details in YAML format.

This example was extracted from a kvm Ubuntu Core instance with `sudo snap known serial`:

``` text
type: serial
authority-id: canonical
brand-id: canonical
model: pc-amd64
serial: 03961d5d-26e5-443f-838d-6db046126bea
device-key:
    AcbBTQRWhcGAARAA0y/BXkBJjPOl24qPKOZWy7H+6+piDPtyKIGfU9TDDrFjFnv3R8EMTz1WNW8d
    5nLR8gjDXNh3z7dLIbSPeC54bvQ7LlaO2VYICGdzHT5+68Rod9h5NYdTKgaWDyHdm2K1v2oOzmMF
    Z+MmL15TvP9lX1U8OIVkmHhCO7FeDGsPlsTX2Wz++SrOqG4PsvpYsaYUTHE+oZ+Eo8oySW/OxTmp
    rQIEUoDEWNbFR5/+33tHRDxKSjeErCVuVetZxlZW/gpCx5tmCyAcBgKoEKsPqrgzW4wUAONaSOGc
    Zuo35DxwqeGHOx3C118rYrGvqA2mCn3fFz/mqnciK3JzLemLjw4HyVd1DyaKUgGjR6VYBcadL72n
    YN6gPiMMmlaAPtkdFIkqIp1OpvUFEEEHwNI88klM/N8+t3JE8cFpG6n4WBdHUAwtMmmVxXm5IsM3
    uNwrZdIBUu4WOAAgu2ZioeHLIQlDGw6dvVTaK+dTe0EXo5j+mH5DFnn0W1L7IAj6rX8HdiM5X5fP
    4kwiezSfYXJgctdi0gizdGB7wcH0/JynaXA/tI3fEVDu45X7dA/XnCEzYkBxpidNfDkmXxSWt5N/
    NMuHZqqmNHNfLeKAo1yQ/SH702nth6vJYJaIX4Pgv5cVrX5L429U5SHV+8HaE0lPCfFo/rKRJa9i
    rvnJ5OGR4TeRTLsAEQEAAQ==
device-key-sha3-384: _4U3nReiiIMIaHcl6zSdRzcu75Tz37FW8b7NHhxXjNaPaZzyGooMFqur0EFCLS6V
timestamp: 2016-11-08T18:16:12.977431Z
sign-key-sha3-384: BWDEoaqyr25nF5SNCvEv2v7QnM9QsfCc0PBMYD_i2NGSQ32EF2d4D0hqUel3m8ul

AcLBUgQAAQoABgUCWCIWcgAARegQAB4/UsBpzqLOYOpmR/j9BX5XNyEWxOWgFg5QLaY+0bIz/nbU
avFH4EwV7YKQxX5nGmt7vfFoUPsRrWO4E6RtXQ1x5kYr8sSltLIYEkUjHO7sqB6gzomQYkMnS2fI
xOZwJs9ev2sCnqr9pwPC8MDS5KW5iwXYvdBP1CIwNfQO48Ys8SC9MdYH0t3DbnuG/w+EceOIyI3o
ilkB427DiueGwlBpjNRSE4B8cvglXW9rcYW72bnNs1DSnCq8tNHHybBtOYm/Y/jmk7UGXwqYUGQQ
Iwu1W+SgloJdXLLgM80bPzLy+cYiIe1W1FSMzVdOforTkG5mVFHTL/0l4eceWequfcxU3DW9ggcN
YJx8MPW9ab5gPibx8FeVb6cMWEvm8S7wXIRSff/bkHMhpjAagp+A6dyYsuUwPXFxCvHSpT0vUwFS
CCPHkPUwj54GjKAGEkKMx+s0psQ3V+fcZgW5TBxk/+J83S/+6AiQ06W8rkabWCRyl2fX81vMBynQ
nu147uRGWTXfa31Mys9lAGNHMtEcMmA106f2XfATqNK99GlIIjOxqEe5zH3j51JtY+5kyJd9cqvl
Pb0rZnPySeGxnV4Q2403As67AJrIExRrcrK2yXZjEW3G2zTsFNzBSSZr0U8id1UJ/EZLB/em2EHw
D2FXTwfDiwGroHYUFAEu1DkHx7Sy
```
