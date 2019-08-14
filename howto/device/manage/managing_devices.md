# Managing Devices

This topic describes the device management operations such as how to view and change device status, how to obtain device secret credentials, and more.

## Before You Start

- To manage devices, you must have write access to the **Connectivity Management** service. If you don't have the access, contact your OU administrator to obtain the access. For more information about user access in EnOS, see [Policy, Roles, and Access](/docs/iam/en/latest/access_policy).

- To learn about the activation and running status of a device, see [Secret-based One-way Authentication](../../../learn/deviceconnection_authentication).

## Viewing Device Status

1. In the EnOS Console, select **Device Management > Device**.

2. In the table of devices, you can view the device status in the **Status/Enable** column.

The "Inactive" status is displayed only when the product is not activated. Once the product is activated, the running status - online or offline - will be displayed in the table.

## Device Online/Offline

When a device is in operation, you can change its status by turning it on or off.

If the device is found to be abnormal, it can be disabled in EnOS.

1. In the EnOS Console, select **Device Management > Device**.

2. In the table of devices, you can specify a device as online or offline by the switch in the **Status/Enable** column.

## Retrieving the Device Triple Information

The `productKey`, `deviceKey`, and `deviceSecret` are considered as the device triple information.

1. In the EnOS Console, select **Device Management > Device**.

2. In the table of devices, for the target device, click **View** to open the **Device Details** page.

3. Find the `productKey`, `deviceKey`, and `deviceSecret` of the device.

## Updating Devices in Batch

You can update devices in batch by downloading the template, editing and then uploading it.

1. Go to **Device Management > Device** .

2. Click **Batch Import** . In the **Batch Import and Update** pop-up, select **Batch Update** for **Operations** .

3. In **Product** , select the product that the devices to be edited belong to.

4. In **Template** , you can select one of the following methods to download a template:

   - Download an empty template: select this option then click **Empty template(.xlsx)** to download an empty template.
   - Generate template from existing device: select this option and then choose a device from the drop-down list. Then click **Template (.xlsx)** to download a template based on this device.

   The downloaded template is named *Template_productKey.xlsx*, **productkey** referring to the product key of the device.

5. Edit and save your template.

6. Click |upload_file|, find the edited template and click **Open** . Then click **OK** to upload the template.

 .. |upload_file| image:: ../../../media/button_upload_file.png

   EnOS starts parsing the template. When it completes parsing, the result will be displayed in a pop-up. If there is at least one valid record, you can click **Update** to finish importing. For those invalid records, if there are any, you can click **Export Invalid Records** to download the _errmsg.txt_ file and view the location of the invalid records in the file and the cause.

## Adding Sub-devices to a Gateway Device

Perform the following steps to add sub-devices to gateway devices other than EnOS Edge. To add sub-devices to EnOS Edge, see [Managing Edge](/docs/enos-edge/en/latest/howto/console_configuration/managing_edge).

1. Go to **Device Management > Device** .

2. For those devices whose **Asset Type** is **Edge** . Click |view| in the **Operations** column.  

3. In **Device Details** page, go to **Sub-device** tab. 

4. Click **New Sub-device**, select devices to be added to this gateway device and click **OK** . You can only select those that have not been added to other gateway devices.

.. |view| image:: ../../../media/button_view.png