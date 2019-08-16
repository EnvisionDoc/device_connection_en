# Creating Devices in Batch

You can create devices of the same product in batch by using this feature.

## About This Task

When there are lots of devices that need to be created, you can use the following methods to create them in batch:
- download a blank template, fill in device information and then upload the template to create devices in batch;
- generate a template based on an existing device, fill in device infirmation and then upload it to create devices in batch.

##  Before You Start

- Created The product that the device belongs to. For information about how to create a product, see [Creating a Device Collection (Product)](creating_product)。
- Obtained the permissions for device management; if not, contact your OU administrator to obtain such permissions. See [Policies, Roles and Permissions](/docs/iam/en/latest/access_policy).

## Procedure

1. Select **Device Management > Device**.

2. Select **Batch Import** and select the product the devices belong to.

3. Select one of the following methods to download the template:

  - Click **Download an empty template**, then click **Empty tempate(.xlsx)** to download the blank template;
  - If the devices to be imported have similar attributes to an existing device, select **Generate template from existing device**, select a device in the drop-down list based on its device key, and then click **Template (.xlsx)** to download the template.

  .. note:: The downloaded template is named as \"Template*\_product\_key*\.xlsx\", where *product_key* refers to the product key of the product the device belongs to, and you can find it in **Device Management > Product**.

  .. image:: ../../../media/device_batch_import.png

4. Fill in the template with the device attributes in the required format. The template contains the following fixed fields:

  .. csv-table::
     :widths: auto

     "Field", "Description"
     "DeviceKey", "The device key. It is recommended that you use the SN, IMEI or MAC address as the device key. Supports upper case and lower case lettters, numbers, hyphen (-), underline (_), period (.) and colon (:). 4-64 characters. The DeviceKey must be unique within an OU."
     "Device Name", "Used to search for devices in the device list. Supports Chinese characters, upper case and lower case letters, numbers, hyphen (-), underline (_), period (.) and colon (:). No more than 64 characters."
     "Time Zone", "Refers to the time zone where the device is located. If the DST is used in this zone, you should select the cities to which DST applies in the drop-down list."

  Other fields in the template include the attributes of the device. The attribute name, data type and whether the field is required are all inherited from the model that the device belongs to.

  The template generated from the existing device includes the same fixed fields as a blank template, and the attributes of the existing device is recorded at the 3rd line in this template file (.xlsx). Delete the existing device before entering new device information. The following sample is a template generated from an existing device.

  .. image:: ../../../media/batch_import_device_sample_template.png

5. Click **Upload File**, browse and select the completed template and upload it. The template to be uploaded must meet the following requirements:
  - The format must be .xlsx
  - The file size should be no more than 2M
  - A maximum of 1000 records are supported in one template file. If you need to upload more than 1000 device records, split the device records into multiple template files and upload them one at a time.
  - The number of the devices under one product should not exceed 10, and the number of the devices in the list plus the device records in the template should not exceed 100,000. Otherwise a prompt saying that the upper limit is reached will be displayed.

6. Click **OK**, and EnOS starts to parse the template.

## Results

View the results:
- If any invalid record is included in the template, you can select **Export Invalid Records** to download .txt file named _errmsg.txt_. The file contains records in the format of "Line *n*: *error_message*", where n refers to the line where the invalid record is located, and *error_message* refers to the error. Common errors include:

 - The data format is illegal or its length exceeds the limit;
 - No data in a required field;
 - DeviceKey is not unique;


- If the template includes valid records, you can click **Import** to import these records into EnOS. View the devices imported in batch in the device list.

## Next Steps

Any devices not connected to EnOS will be tagged as "not activated". If you wish to activate these devices, you need establish connection by using the client SDK. For more information, see [EnOS SDK Quick Start](/docs/app-development/en/latest/gettingstarted_sdk.html)

## Related information

- [Creating Models](../../model/creating_model)
- [Creating Products](creating_product)
- [Creating Devices](creating_device)
