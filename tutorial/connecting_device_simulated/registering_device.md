# Unit 1: Registering Devices on EnOS Console

Before connecting devices to EnOS IoT hub, you need to register the devices on the EnOS Console, which includes defining the device model, creating a product, registering the devices, and creating an asset tree for the devices.

This tutorial takes a battery device as an example, focusing on how to register a smart device that connects directly to the EnOS Cloud.

## Step 1: Defining a Model

A model is the abstraction of the features of an object that is connected to IoT hub. The device model defines the features of a device, including attributes, measuring points, services, and events. For more information about models, see [Thing Model](/docs/device-connection/en/latest/howto/model/model_overview.html).

This step assumes that there is no device model to be reused on EnOS. Take the following steps to create a model named **battery** and define the needed features.

1. In the EnOS Console, click **Model** from the left navigation panel.

2. Click **New Model**, and provide the following settings in the **New Model** window:

   - **Identifier**: battery
   - **Model Name**: battery
   - **Category**: NA
   - **Created From**: No
   - **Source Model**: No
   - **Description**: Model for battery

3. Click **OK** to save the basic information of the model.

   .. image:: media/create_model.png

4. From the list of created model, click **Edit**, and then click the **Feature Definition** tab of the **Model Details** screen.

5. Click **Add** and create the following features in the **Add Feature** window:

   - Adding an attribute

     .. csv-table::
        :widths: auto

        "Feature Type", "Name", "Identifier", "Data Type", "String Length", "Description"
        "Attribute", "brand", "brand", "string", "64 Bytes", "Brand information of the device"

   - Adding a measuring point

     .. csv-table::
        :widths: auto

        "Feature Type", "Name", "Identifier", "Point Type", "Data Type", "Description"
        "Measuring Point", "temperature", "temperature", "AI", "double", "Point to ingest the device temperature data"

   - Adding a service

     .. csv-table::
        :widths: auto

        "Feature Type", "Name", "Identifier", "Invoke Method", "Description"
        "Service", "start_charging", "start_charging", "Asynchronous", "Service that controls the charging status of the device"

   See the following screen capture of the created features of the model:

   .. image:: media/model_features.png

For details about device model settings, see [Creating a Model](/docs/device-connection/en/latest/howto/model/creating_model.html).

## Step 2: Creating a Product

A product is a collection of devices with the same features. On the basis of the device model, a product further defines the communication specifications for the device.

In this step, create a product called **Battery_Product**. Assume that a device of this product model sends data in JSON format and that the data transmission is not encrypted using CA certificate.

1. In the EnOS Console, select **Device Management > Product**.

2. Click **New Product**, and provide the following settings in the **New Product** window:

   - **Product Name**: Battery_Product
   - **Asset Type**: Device
   - **Model**: battery
   - **Data Type**: JSON
   - **Certificate-Based Authentication**: Disabled
   - **Description**: Computer Battery

3. Click **OK** to save the configuration.

   .. image:: media/create_product.png

For details about the configuration of a product, see [Creating a Device Collection (Product)](/docs/device-connection/en/latest/howto/device/manage/creating_product.html).

## Step 3: Registering a Device

A device is the instance of a product. A device is created from a product so that it inherits not only the basic features of the model, but also the communication features of the product (the device key-secret pair and
device certificate used for secure communication).

In this step, create a device named **battery1**, which belongs to the **Battery_Product** created in the previous step.

1. In the EnOS Console, select **Device Management > Device**.

2. Click **New Device**, and provide the following settings in the **New Device** window:

   - **Product**: Battery_Product
   - **Device Name**: battery1
   - **Timezone/City**: UTC+08:00
   - **Use DST**: No
   - **Device Key**: Optional (it can be generated automatically by the system)
   - **brand**: Enter the brand information of the battery (an attribute defined for the model)

3. Click **OK** to save the configuration.

   .. image:: media/register_device.png   

For details about device settings, see [Registering a Device](/docs/device-connection/en/latest/howto/device/manage/creating_device.html).

After you complete the device registration, find the registered device from the device list, and click the **View** icon in the **Operations** column to open the **Device Details** page. You can get the device triple properties: *Product Key*, *Device Key*, and *Device Secret*, which will be used in connecting the device to EnOS IoT hub.

.. image:: media/device_properties.png   



## Next Unit

[Connecting the Device into EnOS Cloud](connecting_device)
