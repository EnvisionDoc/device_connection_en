# Unit 4: Verifying the Firmware and Device Performance

After the firmware file is uploaded to the cloud, you can push the new firmware version to the RPi device.

1. Log into the EnOS Console, and select **Device Management > OTA**.

2. Find the uploaded firmware, click **Verify**, and provide the following information:

   - **Firmware Version**: Select the current firmware version of the device to be upgraded from the drop-down list.
   - **Device Key**: Select the device key of the RPi device.

   .. image:: media/verify_firmware.png

3. Click **Verify** to complete verifying the new firmware. The status of the firmware file will be changed to **Verified**.

   .. image:: media/firmware_verified.png

Meanwhile, the firmware upgrading command will be sent to the RPi device. See the following example:

```
{id='2', method='ota.device.upgrade', version='1.0', params={fileSize=136, sign=ddd36e29b7e876b3cbb286c2f3d7d3d7, fileUrl=/upload/firmware/1559640389247/firmware_2.0.zip, version=2.0, signMethod=md5}}
```

Upon receiving the command, the RPi device will start downloading the firmware file, decompressing the firmware file, and run the upgrade. After the upgrade is completed, the device will reply with the updated firmware version number. See the following example:

```
{id='null', method='ota.device.inform', version='null', params={version=2.0}}
```

## Result

You can check the upgrading result of the firmware by clicking **Verify**. See the following screen capture:

.. image:: media/firmware_upgrade_success.png

Check the new performance of the LED light to verify the firmware upgrade result.

If the upgrading is failed, double check the firmware file or the device status, and upload a new firmware to verify firmware upgrading again. For more information about the verifying process, see [Verifying Firmware](/docs/device-connection/en/2.0.8/howto/ota/verifying_firmware.html). 

## Next Unit

[Considering for Batch Upgrade](considering_batch_upgrade)

<!-- end -->