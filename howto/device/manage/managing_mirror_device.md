# Managing Mirror Device

If you no longer need data to be forwarded to multiple device instances, you can stop the mirror device receiving data from the source device.

## About This Task

This topic describes how to disable a mirror device.

## Before You Start

- You have obtained access to device management. If not, ask your OU administrator to grant you the access. For more information, see [Policy, Role, and Access](/docs/iam/en/dev/access_policy).
- You have completed assigning a device instance as mirror device. For more information, see [Forwarding Data From a Physical Device to Multiple Device Instances](mirroring_device_data).

## Procedure

1. Go to **Device Management > Device**.

2. In the device list, click |view| to enter **Device Details** page of the mirror device.

 .. |view| image:: ../../../media/button_view.png

3. Go to the **Mirroring** tab page.

4. Click **Delete** to stop receiving data from source device.

## Results

The **Status** of the mirror device turns **Inactive**.

.. image:: ../../../media/mirroring_device_4.png

.. note:: - You can also delete the source device. When the source device is deleted, the mirror device stops receiving data. The **Status** of the mirror device turns **Inactive**.
   - If the physical device that sends data to the source device is replaced (the Asset ID of the source device remains unchanged but the device key changes) the data forwarding won't be interrupted. The **Device Key** information in the **Mirroring** tab of the mirror device will be updated.


<!--end-->
