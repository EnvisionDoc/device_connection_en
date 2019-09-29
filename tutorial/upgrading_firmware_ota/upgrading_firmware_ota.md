# Tutorial Overview

## Scenario

Upgrade the firmware of the Raspberry Pi that has been connected to EnOS cloud to add 1 more controlling command to the LED light on the RPi. Before upgrading the firmware, the LED light has 3 status (0 standing for light off, 1 standing for light on, and 2 standing for flickering). After upgrading the firmware, the LED light has 1 more status (3 standing for flickering by another frequency). When the firmware is upgraded, verify the new performance of the LED light on the RPi.

The scenario is depicted in the following chart:

.. image:: media/scenario_ota.png

This tutorial walks you through a typical path of upgrading the firmware of a device through the **Cloud forced push** upgrading mode, which is:

- Developing OTA capabilities on the RPi
- Preparing the new firmware file that contains the new commands
- Uploading the new firmware file on EnOS Console
- Verifying the upgraded firmware and the new performance of the device
- Considering for batch upgrade of multiple devices

### [Start >](developing_ota_capability)

## Prerequisites

You have completed connecting an RPi with an LED light to EnOS. For more information, see [Tutorial - Connecting Raspberry Pi into EnOS](/docs/device-connection/en/2.0.9/tutorial/connecting_device_rasberrypi/index.html).

## Units

This tutorial includes the following units:

> [Unit 1. Developing OTA Capabilities on the Raspberry Pi](developing_ota_capability)
>
> 30 minutes

> [Unit 2. Preparing the New Firmware File](preparing_firmware)
>
> 30 minutes

> [Unit 3. Uploading the Firmware File](uploading_firmware)
>
> 15 minutes

> [Unit 4. Verifying the Firmware and Device Performance](verifying_firmware)
>
> 15 minutes

> [Unit 5. Considering for Batch Upgrade](considering_batch_upgrade)
>
> 15 minutes
