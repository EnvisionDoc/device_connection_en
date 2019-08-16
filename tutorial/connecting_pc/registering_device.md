# Unit 1: Registering the PC on EnOS Console

Before connecting the PC to EnOS IoT hub, you need to register it on the EnOS Console, which includes defining the computer model, creating a computer product, and registering the PC as a device that can be connected directly to the EnOS Cloud。

## Step 1: Defining a Computer Model

A model is the abstraction of the features of an object that is connected to the IoT hub. The model defines the features of a device, including attributes, measuring points, services, and events. For more information about models, see [Thing Model](/docs/device-connection/en/latest/howto/model/model_overview.html).

This step assumes that there is no model for computers to be reused on EnOS. Take the following steps to create a model named **Computer** and define the needed features.

1. In the EnOS Console, click **Model** from the left navigation panel.

2. Click the **New Model** button, and provide the following settings in the **New Model** window:

   - **Identifier**: Computer
   - **Model Name**: Computer
   - **Category**: NA
   - **Created From**: No
   - **Source Model**: No
   - **Description**: Model for computer

3. Click **OK** to save the basic information of the model. See the example below.

   .. image:: media/create_model.png

4. From the list of created model, click **Edit**, and then click the **Feature Definition** tab on the **Model Details** page.

5. Click **Add** and define the needed features in the **Add Feature** window.

   - Define the following attributes:

     .. csv-table::
        :widths: auto

        "Feature Type", "Name", "Identifier", "Data Type", "String Length", "Description"
        "Attribute", "system", "system", "string", "64 Bytes", "System information of the computer"
        "Attribute", "model", "model", "string", "128 Bytes", "Model information of the computer"
        "Attribute", "cpu_core", "cpu_core", "int", "--", "Number of CPU cores of the computer"
        "Attribute", "mem_total", "mem_total", "double", "--", "Total memory of the computer"

   - Define the following measuring points:

     .. csv-table::
        :widths: auto

        "Feature Type", "Name", "Identifier", "Point Type", "Data Type", "Description"
        "Measuring Point", "cpu_used", "cpu_used", "AI", "double", "Point for getting data of used CPU"
        "Measuring Point", "mem_used", "mem_used", "AI", "double", "Point for getting data of used memory"
        "Measuring Point", "cpu_percent", "cpu_percent", "AI", "double", "Point for getting data of CPU usage percentage"
        "Measuring Point", "mem_percent", "mem_percent", "AI", "double", "Point for getting data of memory usage percentage"

   - Define the following service:

     .. csv-table::
        :widths: auto

        "Feature Type", "Name", "Identifier", "Invoke Method", "Input Parameters", "Output Parameters", "Description"
        "Service", "control", "control", "Synchronous", "interval (int)", "result (enum; 0:success, 1:failure)", "Service that controls the data uploading frequency of the computer"

    - Define the following event:

      .. csv-table::
         :widths: auto

         "Feature Type", "Name", "Identifier", "Severity", "Output Parameters", "Description"
         "Event", "cpu_event", "cpu_event", "Warning", "value (double); message (string)", "Event for monitoring CPU load"

   See the following screen capture of the created features of the model:

   .. image:: media/model_features.png

For details about device model settings, see [Creating a Model](/docs/device-connection/en/latest/howto/model/creating_model.html).

## Step 2: Creating a Computer Product

A product is a collection of devices with the same features. On the basis of the device model, a product further defines the communication specifications for the device.

In this step, create a product called **Computer**. Assume that a device of this product model sends data in JSON format and that the data transmission is not encrypted using CA certificate.

1. In the EnOS Console, select **Device Management > Product**.

2. Click the **New Product** button, and provide the following settings in the **New Product** window:

   - **Product Name**: Computer
   - **Asset Type**: Device
   - **Model**: Computer
   - **Data Type**: JSON
   - **Certificate-Based Authentication**: Disabled
   - **Description**: Personal Computer

3. Click **OK** to save the configuration. See the example below.

   .. image:: media/create_product.png

For details about the configuration of a product, see [Creating a Device Collection (Product)](/docs/device-connection/en/latest/howto/device/manage/creating_product.html).

## Step 3: Registering the PC as a Device

A device is the instance of a product. A device is created from a product so that it inherits not only the basic features of the model, but also the communication features of the product (the device key-secret pair and
device certificate used for secure communication).

In this step, create a device named **PC_Win10**, which belongs to the **Computer** product created in the previous step.

1. In the EnOS Console, select **Device Management > Device**.

2. Click the **New Device** button, and provide the following settings in the **New Device** window:

   - **Product**: Computer
   - **Device Name**: PC_Win10
   - **Timezone/City**: UTC+08:00
   - **Use DST**: No
   - **Device Key**: Optional (it can be generated automatically by the system)
   - **system**: Enter the system information of the computer (for example, Win10)
   - **model**: Enter the model information of the computer (for example, E480)
   - **cpu_core**: Enter the number of CPU cores of the computer (for example, 4)
   - **mem_total**: Enter the total memory of the computer (for example, 8000000000)

   .. note:: Enter estimated values of the attribute information for now. The real attributes will be ingested and updated after the PC device is connected into EnOS.

3. Click **OK** to save the configuration. See the example below.

   .. image:: media/register_device.png   

For details about device settings, see [Registering a Device](/docs/device-connection/en/latest/howto/device/manage/creating_device.html).

After you complete the registration of the PC, find the registered device from the device list, and click the **View** icon in the **Operations** column to open the **Device Details** page. You can get the device triple properties: *Product Key*, *Device Key*, and *Device Secret*, which will be used in connecting the PC into EnOS IoT hub. See the following example:

.. image:: media/device_properties.png   



## Next Unit

[Configuring Storage Policy for the Device Data](configuring_storage_policy)
