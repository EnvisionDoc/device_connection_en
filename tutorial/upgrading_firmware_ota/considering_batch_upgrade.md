# Unit 5: Considering for Batch Upgrade

The EnOS firmware upgrade OTA service supports firmware upgrading for multiple devices. Once the firmware is verified on 1 device, you can create a batch upgrading task, specify the upgrading strategy and methods, and start to push the firmware to multiple devices. For more information about batch upgrading, see [Batch Upgrading Firmware](/docs/device-connection/en/2.0.8/howto/ota/batch_upgrading_firmware.html).

Besides, the EnOS firmware upgrade OTA service also supports the **Upon device request** firmware upgrading mode, by which you can use the *upgradeFirmwareByDeviceReq()* function provided by the EnOS Device SDK for MQTT. For more information, see [Firmware OTA Upgrade Overview](/docs/device-connection/en/2.0.8/howto/ota/ota_overview.html). 