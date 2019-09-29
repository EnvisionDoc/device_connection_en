# Connecting a Non-Smart Device to EnOS Cloud via Edge

This article helps you quickly learn how to provision the sub-device and edge to the EnOS Cloud, allowing the sub-device to connect and send telemetry to the EnOS Cloud through the edge, as well as how to check the communication status and view the data from EnOS Console.

## About This Scenario

For information about the connection scenario of this task, see "Scenario 2.1" in [Device Connection Scenarios](../learn/connection_scenarios).

## About This Task

In this example, an inverter will be connected to the EnOS Cloud through an edge gateway. Edge will collect data from the inverter and send them to the EnOS Cloud as a proxy. The overall connection scenario is shown in the figure below:

.. image:: ../media/edge_connection_task_description.png   


As shown in the flowchart above, the procedure falls into the following steps:

1. Create device models

   - Gateway model
   - Inverter Model

2. Create products

   - Gateway product
   - Inverter product

3. Register devices

   - Gateway device
   - Inverter device

4. Configure the edge gateway

5. Use the device SDK to simulate gateway sending data as a proxy of the sub-device

6. Check device communication status

7. Check device data

## Prerequisites

Before you start, make sure that you have completed sub-device (the inverter, as briefed above) registration by following [Connecting a Smart Device into EnOS Cloud](gettingstarted_device_connection). In this task, we only create the model, product, and device for the gateway device.

## Step 1: Create an Edge Device Model

In this step, We will create an Edge device model called **Edge_Model**, and we assume that there are no reusable edge device models.

1. In the EnOS Console, click **Model** from the left navigation panel.

2. Click **New Model**, and provide the following settings in the **New Model** window:

   - **Identifier**: Edge_Model
   - **Model Name**: Edge_Model
   - **Category**: NA
   - **Create From**: No
   - **Support Passthrough**: Default
   - **Source model**: NA
   - **Description**: Edge Model

   .. image:: ../media/model_edge.png

3. Click **OK** to complete the operation.

4. Click **Edit**, and click the **Feature Definition** tab in the **Model Details** screen.

5. Click **Add**, and provide the following settings in the **Add Feature** window:

   - **Attribute**
   - **Name**: Version
   - **Identifier**: Version
   - **Data Type**: string
   - **String Length**: 20
   - **Required**: Yes

6. Click **OK** to complete the operation.

For details on device model settings, see [Creating Model](../howto/model/creating_model).

## Step 2: Create an Edge Product

In this step, we create a product called **Edge_Product**. We assume that an edge device of this product model sends data in JSON format and the data transmission is not encrypted using CA certificate.

1. In the EnOS Console, select **Device Management > Product**.

2. Click **New Product**, and provide the following settings in the **New Product** window:

   - **Product Name**: Edge_Product
   - **Asset Type**: Gateway
   - **Device Model**: Edge_Product
   - **Data Format**: Json
   - **Certificate-based Two-way Authentication**: Disable
   - **Product Description**: Edge Product

3. Click **OK** to complete the operation.

For details about product settings, see [Creating Products](../howto/device/manage/creating_product).

## Step 3: Register the Edge Gateway

In this step, we create an edge device named **Edge01**, which belongs to the **Edge_Product** product model created in the previous step.

1. In the EnOS Console, select **Device Management > Device**.

2. Click **New Device**, and provide the following settings in the **New Device** window:

   - **Product**: Edge_Product
   - **Device Name**: Edge01
   - **Use DST**: No
   - **timezone**: UTC+14:00
   - **Device Key**: Optional, generated automatically by system
   - **Version**: Optional, left blank by default

3. Click **Confirm** to complete the operation.

.. image:: ../media/register_edge.png   

## Step 4: Configure Edge Connection

### Using EnOS Edge

Use this procedure if you choose to use the EnOS Edge. When you use the gateway products from other vendors, please refer to their respective gateway configuration instructions.

1. Apply for an Edge gateway device from your Envision project manager or support representative. Your edge device triple needs to be burned into the edge firmware. Ensure that you get the edge device credentials ready. After the edge is prepared, you will obtain an SN for the edge gateway.

2. Go to the **Edge Gateway > Edge Management** configuration page, and activate the edge gateway device that you applied by entering the obtained SN.

3. In the edge gateway, configure the connections and add sub-devices into the connection.

4. Publish the configuration to the edge gateway.

For more information on how to configure the EnOS Edge gateway, see [Adding an EnOS Edge Gateway](../howto/edge/managing_edge).

## Step 5: Use the Device SDK to Simulate Gateway Sending Data as Proxy of Sub-devices

In this step, we use the device SDK to simulate sending the inverter active power to the cloud.

1. Obtain the [Device SDK](https://github.com/EnvisionIot/enos-mqtt-java-sdk). For more information, see the GitHub readme file.

2. Configure the EnOS Cloud connection as instructed in the readme file.

3. Configure the device triple (`ProductKey`,`DeviceKey`,`DeviceSecret`) into the sample connection program. The device triple is obtained when you register the device.

4. Modify the `postSubMeasurepoint` method, configure the name of the measuring point that sends telemetry to the cloud. In this example, we send the active power point of the inverter, set the point name **INV.GenActivePW** and the corresponding point value.

5. Invoke sample methods for the following operations:

   - Gateway logs in. In the following sample code, `environment_address` is the address of the EnOS Cloud, `productKey`, `deviceKey`, and `deviceSecret` are the device triple of the edge gateway.

     ```java
     public static void initWithCallback() {
        System.out.println("start connect with callback ... ");

        try {
            client = new MqttClient(environment_address, productKey, deviceKey, deviceSecret);
            client.getProfile().setConnectionTimeout(60).setAutoReconnect(false);

            client.connect(new IConnectCallback() {
                public void onConnectSuccess() {

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
     ```

   - Gateway adds the topology of sub-devices and sub-device logs through the edge gateway. In the following sample code, "`sub_pk`", "`sub_dk`", "`sub_ds`" are the device triple of the sub-device.

     ```java
     public static void addTopo() throws Exception {
        System.out.println("start add topo ...");
        TopoAddRequest request = (TopoAddRequest)TopoAddRequest.builder().addSubDevice(new SubDeviceInfo("sub_pk", "sub_dk", "sub_ds")).build();
        TopoAddResponse rsp = (TopoAddResponse)client.publish(request);
        System.out.println("-->" + rsp);
        getTopo();
        subDeviceLogin();                    //Sub-device logs in through gateway
     }
     ```


   - Gateway sends data of the sub-device as the proxy. The following sample code sends random float data to the `INV.GenActivePW` measuring point.

     ```java
     public static void postSubMeasurepoint() {
        Random random = new Random();
        System.out.println("start post sub device measurepoint ...");
        MeasurepointPostRequest request = (MeasurepointPostRequest)((MeasurepointPostRequest.Builder)((MeasurepointPostRequest.Builder)MeasurepointPostRequest.builder().setProductKey("sub_pk")).setDeviceKey("sub_dk")).addMeasurePoint("INV.GenActivePW", random.nextFloat()).build();

        try {
            MeasurepointPostResponse rsp = (MeasurepointPostResponse)client.publish(request);
            System.out.println("-->" + rsp);
        } catch (Exception var3) {
            var3.printStackTrace();
        }
     }
     ```

For more information, see [Using the Device SDK](../howto/device/develop/using_java_sdk).

## Step 6: Check the Device Status

In the EnOS Console, click **Device Management > Device**, locate the Edge01 and INV001 devices and confirm that the devices are online.

## Step 7: Check the Device Data

1. In the **Device** page, locate the INV001 device and click **View** to show the **Device Details** page.
2. Click the **Measuring Points** tab, select the **INV.GenActivePW** measuring point, and click **View data** to open the **Data Insights** page.
3. View the latest data of the measuring point on the Data Insights page. If TSDB storage policy has been configured for the measuring point, you can also view the historic data of the measuring point in a chart or table. For more information about data insights, see [Generating Time Series Data Chart](/docs/data-asset/en/2.0.8/howto/storage/generating_data_chart.html).   
