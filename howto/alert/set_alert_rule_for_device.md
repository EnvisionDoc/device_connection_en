# Tutorial: Setting Different Alert Thresholds for Devices of the Same Model

In certain scenario, the thresholds of alert for devices derived from one model may be different due to their specifications. For instance, a threshold that is set to maximum current has to fluctuate in value for different devices, as each device has a distinct maximum current. You can follow these steps to configure one alert for various devices with fluctuating thresholds:

1. Set maximum current as an atrribute when you are creating a model.
2. Set real-time current as a measurement point of the model.
3. Set the maximum current to different values according to device specifications when you are registering the devices.
4. Set the threshold to maximum current when you are creating an alert rule.

## Scenario

In this scenario, we set different alert thresholds for 2 devices of a same type by configuring the thresholds as their attributes. Then we select the attribute as the condition to trigger of the alert. The detail of this scenario is as follows:

In a smart building, we use two ammeter of the same type to measure the currents that goes through a fridge and a fluorescent lamp, which alert when the ammeter detects that the current is above the maximum current allowed by the fridge or the lamp. Let's suppose the maximum current allowed for a fridge is 1000 mA and a fluorescent lamp 70mA.

In this scenario, we will set different alert thresholds for the 2 ammeters so that they both will alert us when the current is above the maximum current of the device they are gauging.

.. image:: ../../media/device_based_alert_scenario.png
   :width: auto

## Before You Start

- You need to have necessary access to model, device management, and alert. If not, ask your OU administrator to grant you the access. For more information, see [Policy, Role, and Access](/docs/iam/en/2.0.9/access_policy).

## Step 1: Configuring Development Environment

We need EnOS Java SDK for MQTT to simulate two ammeters sending data to EnOS. To install EnOS Java SDK for MQTT, Java SE 8 and Maven 3 are required. Configure your environment by following these steps:

1. Install Java SDK at https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html.

2. Install Maven at http://maven.apache.org/download.cgi.

3. Install your IDE, in this case, IntelliJ, at https://www.jetbrains.com/idea/download/.

4. Create dependency on EnOS Java SDK for MQTT in the _pom.xml_ of your project.

 ```xml
 <dependency>
     <groupId>com.envisioniot</groupId>
     <artifactId>enos-mqtt</artifactId>
     <version>2.1.2</version>
  </dependency>
 ```

## Step 2: Create a Ammeter Model

For steps on how to create a model, see [Creating a Model](../model/creating_model). We will define the model as follows:

 .. csv-table:: Model Information
    :header: "Field","Value"
    :widths: auto

    "Identifier", "current_meter"
    "Name", "Current Meter"
    "Created From", "No"
    "Source Model", "No"
    "Description", "Ammeter to gauge current"

 .. csv-table:: Function Definition
    :header: "Field","Element 1","Element 2"
    :widths: auto

    "Type", "Attribute", "Measurement Point"
    "Name", "Max current allowed", "Real-time current"
    "Identifier", "max_current", "rt_current"
    "Data type", "double", "double"
    "Unit", "mA", "mA"

The created model is as follows:

.. image:: ../../media/tutorial_alert_1.png

.. image:: ../../media/tutorial_alert_2.png

## Step 3: Create Product for Ammeter

For steps on how to create a product, see [Creating a Product](../device/manage/creating_product). We will define the product as follows:

.. csv-table:: Product Information
    :header: "Field","Value"
    :widths: auto

    "Product Name", "currentMeter"
    "Asset Type", "Device"
    "Model", "Current Meter"
    "Data Type", "JSON"
    "Certificate-Based Authentication", "Disabled"
    "Description", "None"

## Step 4: Create Device Instances for the Ammeter

For steps on how to create a device instance, see [Creating a Device](../device/manage/creating_device). In our case, we need to create two instances, one for the ammeter gauging the fridge and the other the ammeter gauging the fluorescent lamp. We will define the instances as follows: 

.. csv-table:: Instance Definition
    :header: "Field","Instance 1", "Instance 2"
    :widths: auto

    "Product", "currentMeter", "currentMeter"
    "Device Key", "fridgeMeter", "fluorescentLampMeter"
    "Device", "fridgeMeter", "fluorescentLampMeter"
    "Time Zone/City", "UTC +08:00", "UTC +08:00"
    "Max Current Allowed", "1000", "70"

The 2 created instances are as follows:

.. image:: ../../media/tutorial_alert_3.png

.. image:: ../../media/tutorial_alert_4.png

.. image:: ../../media/tutorial_alert_5.png

## Step 5: Creating Alert Information for the Instances

We will define the following alert information for the two instances:

1. Alert Severity. For steps on how to create an alert severity, see [Creating Alert Severity](create_alert_severity). The following is a sample severity you can refer to:

 .. image:: ../../media/tutorial_alert_6.png

2. Alert Type. For steps on how to create an alert type, see [Creating Alert Type](create_alert_type). The following is a sample severity you can refer to:

 .. image:: ../../media/tutorial_alert_7.png

3. Alert Content. For steps on how to create an alert type, see [Creating Alert Content](create_alert_content). The following is a sample severity you can refer to:

 .. image:: ../../media/tutorial_alert_8.png

4. Alert Rule. For steps on how to create an alert type, see [Creating Alert Rule](create_alert_rule). For **Condition**, select **Attribute**.

 .. image:: ../../media/tutorial_alert_9.png

## Step 6: Use EnOS Java SDK for MQTT to Simulate Device Instances

In your IDE, create two Java classes, `fridgeMQTT` and `lampMQTT`. In the following sample code, the simulated fridge and lamp will have a current in the range of [0,1500)mA and [0,150)mA. Therefore, the two devices both have a possibility that the current runs above **Max Current Allowed** and an alert is triggered. Copy the following sample code, replace the variants with your own settings and run the code snippets. 

- `env`：Replace it with your EnOS environment address.
- `productKey`：Replace it with the product key of the ammeter product.
- `deviceSecret`：Replace it with the device secret of **fridgeMeter** in `fridgeMQTT` and **fluorescentLampMeter** in `lampMQTT`.


The sample code of `fridgeMQTT` is as follows:

```java
import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
import com.envisioniot.enos.iot_mqtt_sdk.core.exception.EnvisionException;
import com.envisioniot.enos.iot_mqtt_sdk.message.upstream.tsl.MeasurepointPostRequest;
import com.envisioniot.enos.iot_mqtt_sdk.sample.SimpleSendReceive;

import java.util.Random;

public class fridgeMQTT {
    public static final String env = "tcp://url:port";
    public static final String productKey = "productKey";
    public static final String deviceKey = "fridgeMeter";
    public static final String deviceSecret = "deviceSecret";
    private static MqttClient client;
    private static volatile boolean subDeviceLogined = false;
    private static Random random = new Random();
    private static int idInc = 20;
    private static final char[] HEX_CHAR = new char[]{'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};

    public fridgeMQTT() {
    }

    public static void main(String[] args) throws Exception {
        initWithCallback();
        alwaysPostMeasurepoint();
    }

    public static void initWithCallback() {
        System.out.println("start connect with callback ... ");

        try {
            client = new MqttClient(env, productKey, deviceKey, deviceSecret);
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

    public static void alwaysPostMeasurepoint() throws Exception {
        while(true) {
            long ts = System.currentTimeMillis();
            Random random = new Random();
            System.out.println("start post measurepoint ...");

            MeasurepointPostRequest request = MeasurepointPostRequest.builder().addMeasurePoint("rt_current", random.nextDouble() * 1500).build();

            try {
                client.fastPublish(request);
                System.out.println(" post measurepoint success...");
            } catch (Exception var3) {
                var3.printStackTrace();
            }
            System.out.println(client.isConnected() + " post  cost " + (System.currentTimeMillis() - ts) + " millis");
            Thread.sleep(10000L);
        }
    }
}
```
The sample code for `lampMQTT` is as follows:
```java
import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
import com.envisioniot.enos.iot_mqtt_sdk.core.exception.EnvisionException;
import com.envisioniot.enos.iot_mqtt_sdk.message.upstream.tsl.MeasurepointPostRequest;
import com.envisioniot.enos.iot_mqtt_sdk.sample.SimpleSendReceive;

import java.util.Random;

public class lampMQTT {
    public static final String env = "tcp://url:port";
    public static final String productKey = "productKey";
    public static final String deviceKey = "fridgeMeter";
    public static final String deviceSecret = "deviceSecret";
    private static MqttClient client;
    private static volatile boolean subDeviceLogined = false;
    private static Random random = new Random();
    private static int idInc = 20;
    private static final char[] HEX_CHAR = new char[]{'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};

    public fridgeMQTT() {
    }

    public static void main(String[] args) throws Exception {
        initWithCallback();
        alwaysPostMeasurepoint();
    }

    public static void initWithCallback() {
        System.out.println("start connect with callback ... ");

        try {
            client = new MqttClient(env, productKey, deviceKey, deviceSecret);
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

    public static void alwaysPostMeasurepoint() throws Exception {
        while(true) {
            long ts = System.currentTimeMillis();
            Random random = new Random();
            System.out.println("start post measurepoint ...");

            MeasurepointPostRequest request = MeasurepointPostRequest.builder().addMeasurePoint("rt_current", random.nextDouble() * 150).build();

            try {
                client.fastPublish(request);
                System.out.println(" post measurepoint success...");
            } catch (Exception var3) {
                var3.printStackTrace();
            }
            System.out.println(client.isConnected() + " post  cost " + (System.currentTimeMillis() - ts) + " millis");
            Thread.sleep(10000L);
        }
    }
}
```

## Results

1. Go to **Alert > Alert Record**. 

2. In **Filter**, set **Model** to **Current Meter**. 

3. Click **Search**.

You can see the **Current above threshold** alerts are reported when the real-time currents of the two devices run above the attribute as threshold.

.. image:: ../../media/tutorial_alert_10.png

## Next Step

You can configure rule so that an alert is triggered if the rule is met and has continued for specified time. For more information, see []().







