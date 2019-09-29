# Forwarding Data From a Physical Device to Multiple Device Instances

You can use one device instance to receive data from a physical device and then forward the data to multiple other device instances. For explanation of these feature, the following concepts are defined:

- _Source device_: The device instance that receives data from a physical device.
- _Mirror device_ ：The device instance that receives data forwarded from the source device.

.. image:: ../../../media/mirroring_device_overview.png
 :width: 500px

In some scenarios, we need to bind one device asset to multiple nodes in an asset tree, so that the data of the device asset can be shared. For example, in photovoltaic industry, the data of one weather station is shared among multiple photovoltaic power plants. However, one device asset can be bound to only one node in an asset tree. This is when this feature comes in. Perform the following steps to solve this problem:

1. Create multiple device instances for this physical device whose data is to be shared. 

2. Assign one instance as source device and others mirror.

3. Let the source device receive data from the physical device and forward the data to mirror devices.

.. image:: ../../../media/mirroring_device.png
 :width: 500px

## Rules and Limitations

- Source device and mirror devices must exist in the same OU.

- You can only assign **Inactive** devices as mirror devices.

- You cannot assign a source device as the mirror device of another source. Neither can you assign a mirror device as the source of another device.

- A source device can forward data to multiple mirror devices. A mirror device can have only one source device at any given time.

- A source device can forward data to a maximum of 100 mirror devices.

## Task Description

This topic describes how to forward the data from a physical device to multiple device instances within an OU.

## Before You Start

- You have obtained access to device management. If not, ask your OU administrator to grant you the access. For more information, see [Policy, Role, and Access](/docs/iam/en/2.0.8/access_policy).
- You have created a source device that has started receiving data from a physical device. For more information, see [Creating a Device](creating_device).

## Procedure

1. Go to **Device Management > Device**。

2. Create a device instance. Or select an existing device instance whose **Status** is **Inactive**.

 .. image:: ../../../media/mirroring_device_1.png

3. In the device list, click |view| to enter **Device Details** page.

 .. |view| image:: ../../../media/button_view.png

4. Go to the **Mirroring** tab page.

5. In **Device Key** field, enter the device key of the instance you have created in "Before You Start" section. Or click |view| and select from the list.

 .. image:: ../../../media/mirroring_device_2.png
 
 .. |view| image:: ../../../media/button_view.png

6. Click **Confirm** to finish configuring the source device.

## Results

In the device list, the **Status** of the device to which we assigned a source device is now **Mirrored**. 

.. image:: ../../../media/mirroring_device_3.png

## Next Step

[Managing Mirror device](managing_mirror_device)










