<h2 id="heading--overview">Overview</h2>

The *gadget* snap is responsible for defining and manipulating the system properties which are specific to one or more devices that will usually look similar to one another from an implementation perspective. This snap must necessarily be produced and signed by the device brand, which is defined via the model assertion. The brand knows where and how that device will be used, and designs the gadget snap accordingly.

For example, the brand may know that the device is actually a special VM to be used on a particular cloud, or it may know that it is going to be manufactured in a particular factory. The gadget snap may encode the mechanisms for device initialization - key generation and identity certification - as well as particular processes for the lifecycle of the device, such as factory resets. It is perfectly possible for different models to share a gadget snap.

[Syntax and composition of a gadget snap ›](../../reference/gadget.md)

<h2 id="heading--reference-gadget-snaps">Reference gadget snaps</h2>

Canonical publishes [reference gadget snaps](../../reference/gadget.html#examples-of-production-ready-gagdet-snaps) as well as gadget snaps for main Canonical models such as official Ubuntu Core VMs on various certified public clouds, as well as general purpose computing images for popular physical devices like the 64-bit x86 PC and Raspberry Pi 2 and 3.

<h2 id="heading--the-prepare-device-hook">The `prepare-device` hook</h2>

The optional `prepare-device` [hook](config-hooks.html) is a script that will be called on the gadget at the start of the device initialization process, after the gadget snap has been installed. The hook will also be called if this process is retried later from scratch in case of initialization failures.

The device initialization process is for example responsible of setting the serial identification of the device through an exchange with a device service. The `prepare-device` hook can for example redirect this exchange and dynamically set options relevant to it.

Like other hooks, the script should be an executable with the name of the hook triggering it and be in the `meta/hooks/` directory of your snap.

<h3 id="heading--example">Example</h3>

``` bash
#!/bin/sh

# optionally set the url of the service
snapctl set device-service.url="https://device-service"
# set optional extra HTTP headers for requests to the service
snapctl set device-service.headers='{"token": "TOKEN"}'

# set an optional proposed serial identifier, depending on the service
# this can end up being ignored;
# this might need to be obtained dynamically
snapctl set registration.proposed-serial="DEVICE-SERIAL"

# optionally pass details of the device as the body of registration request,
# the body is text, typically YAML;
# this might need to be obtained dynamically
snapctl set registration.body='mac: "00:00:00:00:ff:00"'
```
