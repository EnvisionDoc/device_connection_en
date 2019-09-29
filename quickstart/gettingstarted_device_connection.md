# Connecting a Smart Device into EnOS Cloud

This article helps you quickly learn how to provision a direct-connecting device to the EnOS Cloud, how to send device telemetry, and how to check device communication state from the EnOS Console.

## About the Scenario

For information about the connection scenario of this task, see "Scenario 1.1" in [Device Connection Schemes](../learn/connection_scenarios).

## About This Task

Here we take the household PV inverter connection as an example. The inverter device triple is burned into the inverter during manufacturing. After you power on and connect the inverter to the network, the inverter is connected to the IoT Hub based on the device triple authentication. The overall process is shown below:

.. image:: ../media/device_connection_task_description.png

As shown in the flowchart above, the procedure falls into the following steps:

1. Create a device model for the inverter
2. Create a product for the inverter
3. Register the inverter
4. Configure storage policy for measuring point data
5. Simulate the inverter to send data via device SDK
6. Check device communication status
7. View device data

## Step 1: Create a Device Model

This step assumes that there is no device model to be reused. We create a new model named **Inverter_Demo** with the features defined as follows:

.. list-table::
   :widths: auto

   * - Feature Type
     - Name   
     - Identifier   
     - Data Type   
     - Value   
   * - Attribute
     - Inverter type     
     - invType
     - enum  
     - {0:Central,1:String}      
   * - Attribute
     - Component capacity
     - capacity     
     - float
     - kWp      
   * - Measuring Point
     - Active power     
     - INV.GenActivePW
     - double
     - kW      
   * - Service
     - Control     
     - INV.Control
     - \
     - Invoke Method: Asynchronous
   * - Event
     - Error information
     - Error
     - \
     - Event Type: Error

The steps to create this model are as follows:

1. In the EnOS Console, click **Model** from the left navigation panel.

2. Click **New Model**, and provide the following settings in the **New Model** window:

   - **Identifier**: Inverter_Demo
   - **Model Name**: Inverter_Demo
   - **Model Name (en)**: Inverter_Demo
   - **Category**: NA
   - **Create From**: No
   - **Source Model**: NA
   - **Description**: Inverter model for demo project

3. Click **OK** to complete the operation.

   .. image:: ../media/model_inverter.png

4. Click **Edit**, and click the **Feature Definition** tab in the **Model Details** screen.

5. Click **Add**, and provide the following settings in the **Add Feature** window:

   - **Attribute 1**

     - **Name**: Inverter_Type
     - **Identifier**: invType
     - **Data Type**: enum
     - **Enum Items**:
       - Value: 0; Description: Central
       - Value: 1; Description: String
     - **Required**: Yes

   - **Attribute 2**

     - **Name**: Inverter_Capacity
     - **Identifier**: capacity
     - **Data Type**: float
     - **Unit**: kWp
     - **Required**: Yes

   - **Measuring Point**

     - **Name**: Active_Power
     - **Identifier**: INV.GenActivePW
     - **Data Type**: double
     - **Point Type**: AI
     - **Unit**: kW

   - **Service**

     - **Name**: Control
     - **Identifier**: INV.Control
     - **Invoke Method**: Asynchronous
     - **Input Parameters**:
       - Parameter Name: control
       - Identifier: control
       - Data Type: enum
       - Enum Items:
         - Value: 0; Description: Stop
         - Value: 1; Description: Start

     - **Output Parameters**:
       - Parameter Name: execResult
       - Identifier: execResult
       - Data Type: enum
       - Enum Items:
         - Value: 0; Description: Failure
         - Value: 1; Description: Success

   - **Event**
     - **Name**: Error
     - **Identifier**: Error
     - **Severity**: Error

6. Click **OK** to complete the operation.

For details on device model settings, see [Creating Model](../howto/model/creating_model).


## Step 2: Create a Product

In this step, we create a product called **Inverter_Product**. We assume that a device of this product model sends data in JSON format and the data transmission is not encrypted using CA certificate.

1. In the EnOS Console, select **Device Management > Product**.

2. Click **New Product**, and provide the following settings in the **New Product** window:

   - **Product Name**: Inverter_Product
   - **Asset Type**: Device
   - **Device Model**: Inverter_Demo
   - **Data Format**: Json
   - **Certificate-based Two-way Authentication**: Disable
   - **Product Description**: Inverter product for demo

3. Click **OK** to complete the operation.

   .. image:: ../media/create_product.png

For details about product settings, see [Creating Products](../howto/device/manage/creating_product).


## Step 3: Register the Device

In this step, we create a device named **INV001**, which belongs to the **Inverter_Product** product model created in the previous step.

1. In the EnOS Console, select **Device Management > Device**.

2. Click **New Device**, and provide the following settings in the **New Device** window:

   - **Product**: Inverter_Product
   - **Device Name**: INV001
   - **Use DST**: No
   - **timezone**: UTC+14:00
   - **Inverter Type**: 0: Central, indicating centralized inverter
   - **Inverter Capacity**: 5.0
   - **Device Key**: Optional, generated automatically by system

3. Click **Confirm** to complete the operation.

   .. image:: ../media/register_device.png   

For details about device settings, see [Creating a Device](../howto/device/manage/creating_device).

After you complete the device registration, obtain the device triple: `ProductKey`,`DeviceKey`,and `DeviceSecret`, which will be used in the following step.

## Step 4: Configuring Storage Policy for Measuring Point Data

After registering the device and before connecting the device to EnOS Cloud, we need to configure storage policy for the data of measuring point INV.GenActivePW. Detailed steps are as follows:

1. If TSDB resource is not requested yet for the organization, log in the EnOS Console, select **Resource Management**, and request TSDB resource under the **Data Asset Management** tab. For more information about requesting resources, see [Resource Management Overview](/docs/enos/en/2.0.8/resourcemanagement/overview.html). 
2. In the EnOS Console, select **Time Series Data > Storage Policy** and configure storage policy (with appropriate storage type and storage time) for the measuring point data. For more information about configuring storage policy, see [Configuring TSDB Storage](/docs/data-asset/en/2.0.8/configuring_tsdb_storage.html).

## Step 5: Use Java SDK to Simulate Device Sending Telemetry

In this step, we use the sample code of Java SDK to simulate sending the inverter active power to the cloud.

1. Obtain the [Device SDK](https://github.com/EnvisionIot/enos-mqtt-java-sdk). For information about how to set up the development environment, see the GitHub readme file.

2. Configure the EnOS Cloud connection as instructed in the readme file.

3. Configure the address of the EnOS cloud (`environment_address`) and the device triple (`ProductKey`,`DeviceKey`,`DeviceSecret`) into the sample connection program. The device triple is obtained when you register the device.

4. Modify the `initWithCallback` method to establish connection between the device and the cloud.

5. Modify the `postMeasurepoint` method to configure the name of the measuring point that sends telemetry to the cloud. In this example, we send the active power point of the inverter, set the point name **INV.GenActivePW** and the corresponding point value.

   The following sample code is for connecting the device to EnOS and simulating posting data to the cloud:

   ```java
   import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
   import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
   import com.envisioniot.enos.iot_mqtt_sdk.core.exception.EnvisionException;
   import com.envisioniot.enos.iot_mqtt_sdk.message.upstream.tsl.MeasurepointPostRequest;
   import com.envisioniot.enos.iot_mqtt_sdk.sample.SimpleSendReceive;

   import java.util.Random;

   public class demo1 {
       public static final String url = "tcp://{environment_address}";
       public static final String productKey = "ProductKey";
       public static final String deviceKey = "{DeviceKey}";
       public static final String deviceSecret = "{DeviceSecret}";
       private static MqttClient client;
       private static volatile boolean subDeviceLogined = false;
       private static Random random = new Random();
       private static int idInc = 20;
       private static final char[] HEX_CHAR = new char[]{'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};

       public demo() {
       }

       public static void main(String[] args) throws Exception {
           initWithCallback();
           postMeasurepoint();
       }

       public static void initWithCallback() {
           System.out.println("start connect with callback ... ");

           try {
               client = new MqttClient(url, productKey, deviceKey, deviceSecret);
               client.getProfile().setConnectionTimeout(60).setAutoReconnect(false);
               client.connect(new IConnectCallback() {
                   public void onConnectSuccess() {
                       SimpleSendReceive.subDeviceLogin();
                       System.out.println("connect success");
                   }

                   public void onConnectLost() {
                       System.out.println("onConnectLost");
                   }

                   public void onConnectFailed(int reasonCode) {
                       System.out.println("onConnectFailed : " + reasonCode);
                   }
               });
           } catch (EnvisionException var1) {
           }

           System.out.println("connect result :" + client.isConnected());
       }

       public static void postMeasurepoint() {
           Random random = new Random();
           System.out.println("start post measurepoint ...");
           MeasurepointPostRequest request = (MeasurepointPostRequest)MeasurepointPostRequest.builder().addMeasurePoint("INV.GenActivePW", random.nextDouble()).build();

           try {
               client.fastPublish(request);
               System.out.println(" post measurepoint success...");
           } catch (Exception var3) {
               var3.printStackTrace();
           }
       }

   }
   ```

6. Use the `handleServiceInvocation()` method to handle the service invocation request from the cloud.

   ```java
   public static void handleServiceInvocation() {
        IMessageHandler<ServiceInvocationCommand, ServiceInvocationReply> handler = new IMessageHandler<ServiceInvocationCommand, ServiceInvocationReply>() {
            public ServiceInvocationReply onMessage(ServiceInvocationCommand request, List<String> argList) throws Exception {
                System.out.println("rcvn async serevice invocation command" + request + " topic " + argList);
                return (ServiceInvocationReply)ServiceInvocationReply.builder().addOutputData("execResult", 0).build();
            }
        };
        client.setArrivedMsgHandler(ServiceInvocationCommand.class, handler);
    }
   ```

For more information, see [Using the Device SDK](../howto/device/develop/using_java_sdk).

## Step 6: Check the Device Connection Status

In the EnOS Console, click **Device Management > Device**, locate the device and check the status of the INV001 device and confirm that the device is **Online**.

.. image:: ../media/device_status.png

## Step 7: Check the Device Data

1. From the device list, locate the device and click the **View** icon to show the **Device Details** page.
2. Click the **Measuring Points** tab, find the **INV.GenActivePW** measuring point, and click **View data** to open the **Data Insights** page.
3. View the latest data of the measuring point on the Data Insights page. If TSDB storage policy has been configured for the measuring point, you can also view the historic data of the measuring point in a chart or table. For more information about data insights, see [Generating Time Series Data Chart](/docs/data-asset/en/2.0.8/howto/storage/generating_data_chart.html).   

## Step 8: Use Online Debugging Tool to Set Measuring Point Value

1. Click **Device Management > Product**, Click **View** icon for the product that the device belongs to.

2. In the product details page, click **Debugging**。

3. Select the device to debug.

4. Select the model point from the **Debugging** drop-down list and the **Method** to use. In this example, we select to **SET** the **INV.GenActivePW** point, and click **Run**.

   .. image:: media/debug_postmeasurepoint.png

When the value of the measuring point is set successfully, you'll receive response similar to the following from your Java development environment:

```
AnswerableMessageBody{id='21', method='thing.service.measurepoint.set', version='1.0', params={INV.GenActivePW=2.0}}
```

<!--end-->
