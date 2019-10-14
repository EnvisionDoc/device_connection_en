# Registering a Device

A device is an instance of a model. It belongs to a certain product. A DeviceKey, a unique certificate within the organization, will be issued by the platform for the device. The device can be connected directly to the platform or connected to the IoT platform as a sub-device via edge. This topic describes how to create a device.

## Before You Start

- To register a device, you must have write access to the **Connectivity Management** service. If you don't have the access, contact your OU administrator to obtain the access. For more information about user access in EnOS, see [Policy, Roles, and Access](/docs/iam/en/2.0.9/access_policy).

- The product to which the device belongs has been created. For information about how to create a product, see [Creating a Product](creating_product).

## Step 1: Creating a Device

1. In the EnOS Console, select **Device Management > Device**.

2. Click the **New Device** button and complete the following settings on the **New Device** pop-up window.

   - **Product**: Select the product to which the device belongs. After selecting the product, continue to configure the Attributes settings in the template of the product. Attributes are either required or optional.
   - **Device Name**: The name of the device; same name can be used under the same organization.
   - **Use DST**: Use daylight saving time, the time zone is city.
   - **timezone**: Refers to the local time of a region or a country to select timezone.
   - **Device Key**: A unique identifier for the product under the same organization. If not provided, the system will generate a device key. When self-defined, supports uppercase alphabets (A-Z), lowercase alphabets (a-z), numbers (0-9), dash "-" and underline.
   - Model-defined features: The features that are defined by the device model, when a feature is set to required in the model, you must provide the setting when registering the device. The optional features can be added after the device is registered.

3. Click **Confirm** to create the product.

## Step 2: (Optional) Adding a Tag

A tag describes the common information shared by all instances of the device.

1. From the list of created devices, find the target device and click **View**.

2. Under the **Basic Information** tab, click **Edit** in the **Tags** section.

3. On the pop-up window, click **New tag**, and enter the key-value pair (key:value) for the new tag.

4. Click **OK** to save the tag.

## Results

After creating the device, you will obtain a set of credentials: Device Name (`deviceName`) and Device Secret (`deviceSecret`). The device secret credential issued by EnOS Cloud will be used to activate the device. For more information, see [Device and Cloud Security](../../../learn/deviceconnection_authentication).

## Follow-up Operations

To store the telemetry data in EnOS TSDB, you need to configure the TSDB policy before activating the device. Else the data sent from the device won't be stored in TSDB by default. For more information, see [Configuraing TSDB Storage](/docs/data-asset/en/2.0.9/configuring_tsdb_storage).


When offline, the status of the newly created device is shown as Inactive. To activate the device, you will need to initiate a connection through the device SDK. For more information, see [EnOS SDK Overview](/docs/app-development/en/2.0.9/sdk_overview.html)


## Related Information

- [Creating a Model](../../model/creating_model)
- [Creating a Product](creating_product)
