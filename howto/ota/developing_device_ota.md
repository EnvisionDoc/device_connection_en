# Developing OTA Capabilities on Devices

## About This Task

The OTA capability largely depends on the physical configuration and function the device. EnOS provides a device SDK that includes OTA capabilities for hardware developers, which supports the following capabilities:

- Reporting the firmware version: The device reports its version once when it gets connected to EnOS. EnOS stores the device's version number;
- Receiving the upgrade request pushed by EnOS: The device receives the upgrade request pushed by EnOS and downloads the firmware file;
- Sending upgrade requests: The device can send upgrade requests to EnOS and query any available new version;
- Reporting upgrade progress and the reasons for upgrade failure: The device can report its upgrade progress or the reasons for upgrade failure during its upgrade process to EnOS for upgrade management;

.. note:: OTA requires flash memories on devices have sufficient storage. EnOS provides only message channels and file download services for OTA upgrade, and an SDK for developers to call the interfaces. Developers have to switch over to new firmware and reboot independently of EnOS-provided SDK.


### Firmware Upgrade Topics

EnOS provides message topics for firmware upgrade, and the upgrade request supports MQTT-based message transmission and subscription:

1. The device reports its firmware version to EnOS:The device reports its current firmware version after it connects to EnOS through this topic:

   Request topic: `/sys/${productkey}/${devicekey}/ota/device/inform`

   Response topic: `/sys/${productkey}/${devicekey}/ota/device/inform_reply`

2. The device receives the OTA request from EnOS: The device receives the OTA upgrade notifications pushed from EnOS, including firmware name, version, MD5 and download URL through this topic:

   Request topic: `/sys/${productkey}/${devicekey}/ota/device/upgrade`

   Response topic:  `/sys/${productkey}/${devicekey}/ota/device/upgrade_reply`

3. The device reports its firmware upgrade progress: The device requests the firmware download through HTTPS, and reports the firmware upgrade event, error code and download progress (%) through this topic:

   Request topic: `/sys/${productkey}/${devicekey}/ota/device/progress`

   Response topic: ` /sys/${productkey}/${devicekey}/ota/device/progress_reply`

4. The device requests available new firmware versions: EnOS creates the OTA upgrade policy as "Upon device request". The device send the upgrade request through this topic and receives the available versions returned from EnOS.

   Request topic: `/sys/${productkey}/${devicekey}/ota/device/getversion`

   Response topic:  `/sys/${productkey}/${devicekey}/ota/device/getversion_reply`


## Before You Start

- You have understood the OTA upgrade process in EnOS. See [Firmware OTA Upgrade Overview](ota_overview).

## Step 1: Installing Device SDK

Download [enos-mqtt-sdk-java](https://github.com/EnvisionIot/enos-mqtt-sdk-java). If Maven is sued, its dependencies are given as follows:

```
<dependency>
  <groupId>com.envisioniot</groupId>
  <artifactId>enos-mqtt</artifactId>
  <version>2.1.2</version>
</dependency>
```

## Step 2: Downloading Device Firmware

   - Download the file by using the REST API `getFile`. You may view the API documentation by going to **EnOS API > API Documentation > Common File Service > getFile** in the navigation bar in the EnOS Console.

   - Through cloud service SDK
     - [Java SDK](https://github.com/EnvisionIot/enos-api-sdk-java)
    

## Sample Codes

The sample code for developing the OTA capabilities by using the Java SDK is as follows:

```java
import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
import com.envisioniot.enos.iot_mqtt_sdk.core.exception.EnvisionException;
import com.envisioniot.enos.iot_mqtt_sdk.core.msg.IMessageHandler;
import com.envisioniot.enos.iot_mqtt_sdk.core.msg.IMqttDeliveryMessage;
import com.envisioniot.enos.iot_mqtt_sdk.core.profile.DefaultProfile;
import com.envisioniot.enos.iot_mqtt_sdk.message.downstream.ota.OtaUpgradeCommand;
import com.envisioniot.enos.iot_mqtt_sdk.message.upstream.ota.*;

import java.util.List;
import java.util.concurrent.TimeUnit;

/**
 * device firmware over-the-air
 */
public class OtaSample {

    //Please use your own productKey, deviceKey, and deviceSecret
    static String productKey = "testPK";
    static String deviceKey = "testDK";
    static String deviceSecret = "testDS";

    //Use the actual mqtt-broker url
    static String brokerUrl = "tcp://{mqtt-broker-url}";

    static MqttClient client;

    public static void main(String[] args) throws Exception {
        client = new MqttClient(new DefaultProfile(brokerUrl, productKey, deviceKey, deviceSecret));
        initWithCallback(client);

        //Report firmware version first
        reportVersion("initVersion");

        //        upgradeFirmwareByCloudPush();


        //        upgradeFirmwareByDeviceReq();
    }

    public static void upgradeFirmwareByCloudPush() {
        client.setArrivedMsgHandler(OtaUpgradeCommand.class, new IMessageHandler<OtaUpgradeCommand, IMqttDeliveryMessage>() {
            @Override
            public IMqttDeliveryMessage onMessage(OtaUpgradeCommand otaUpgradeCommand, List<String> list) throws Exception {
                System.out.println("receive command: " + otaUpgradeCommand);

                Firmware firmware = otaUpgradeCommand.getFirmwareInfo();

                //TODO: download firmware from firmware.fileUrl

                //Mock reporting progress
                reportUpgradeProgress("20", "20");
                TimeUnit.SECONDS.sleep(2);

                reportUpgradeProgress("25", "25");
                TimeUnit.SECONDS.sleep(20);

                reportUpgradeProgress("80", "80");
                TimeUnit.SECONDS.sleep(20);

                //Firmware upgrade success, report new version
                reportVersion(otaUpgradeCommand.getFirmwareInfo().version);

                return null;
            }
        });
    }

    public static void upgradeFirmwareByDeviceReq() throws Exception {
        List<Firmware> firmwareList = getFirmwaresFromCloud();
        String version = null;
        for (Firmware firmware : firmwareList) {
            version = firmware.version;
            StringBuffer sb = new StringBuffer();
            sb.append("Firmware=>[");
            sb.append("version=" + firmware.version);
            sb.append("signMethod=" + firmware.signMethod);
            sb.append("sign=" + firmware.sign);
            sb.append("fileUrl=" + firmware.fileUrl);
            sb.append("fileSize=" + firmware.fileSize);
            sb.append("]");
            System.out.println(sb.toString());
        }
        if (version != null) {
            reportUpgradeProgress("20", "20");
            TimeUnit.SECONDS.sleep(10);
            reportUpgradeProgress("80", "80");
            TimeUnit.SECONDS.sleep(20);
            reportVersion(version);
        }
    }

    public static void reportVersion(String version) throws Exception {
        OtaVersionReportRequest.Builder builder = new OtaVersionReportRequest.Builder();
        builder.setProductKey(productKey).setDeviceKey(deviceKey).setVersion(version);
        OtaVersionReportRequest request = builder.build();
        System.out.println("send =>" + request.toString());
        client.fastPublish(builder.build());
    }

    private static void reportUpgradeProgress(String progress, String desc) throws Exception {
        OtaProgressReportRequest.Builder builder = new OtaProgressReportRequest.Builder();
        builder.setStep(progress).setDesc(desc);
        client.fastPublish(builder.build());
    }

    private static List<Firmware> getFirmwaresFromCloud() throws Exception {
        OtaGetVersionRequest.Builder builder = new OtaGetVersionRequest.Builder();
        builder.setProductKey(productKey).setDeviceKey(deviceKey);
        OtaGetVersionRequest request = builder.build();
        OtaGetVersionResponse response = client.publish(request);
        System.out.println("send getversion request =>" + request.toString());
        System.out.println("receive getversion response =>" + response.toString());
        return response.getFirmwareList();
    }

    private static void initWithCallback(MqttClient client) {
        System.out.println("start connect with callback ... ");
        try {
            client.connect(new IConnectCallback() {
                @Override
                public void onConnectSuccess() {
                    System.out.println("connect success");
                }

                @Override
                public void onConnectLost() {
                    System.out.println("onConnectLost");
                }

                @Override
                public void onConnectFailed(int reasonCode) {
                    System.out.println("onConnectFailed : " + reasonCode);
                }

            });
        } catch (EnvisionException e) {
            e.printStackTrace();
        }
    }
}
```

## Firmware Upgrade Protocol

### Reporting Version 

The device can send its current firmware version to EnOS. You can create an upgrade task for only the device that has reported its firmware version.

Upstream TOPIC

- Request topic: `/sys/${productkey}/${devicekey}/ota/device/inform`
- Response topic: `/sys/${productkey}/${devicekey}/ota/device/inform_reply`

Request data format:

```JSON
{
    "id":"123",
    "version":"1.0",
    "method":"ota.device.inform",
    "params": {
        "version":"xxxxxxxx"
    }
}
```

Response data format:

```JSON
{
    "id": "123",
    "code": 200,
    "data": {}
}
```

Parameter descriptions:

.. list-table::
   :widths: auto

   * - Parameters
     - Type
     - Required or Optional
     - Description
   * - id
     - Long
     - Optional
     - Message ID (reserved value)
   * - version
     - String
     - Required
     - Protocol version, 1.0 at present
   * - method
     - String
     - Required
     - Request method, which must be ota.device.inform
   * - params
     - Object
     - Required
     - Reported version number
   * - version
     - String
     - Required
     - Reported version number
   * - code
     - Integer
     - Required
     - Result return code. 200 indicates success.

### Receiving Upgrade Request From EnOS

After the firmware upgrade task is created in the console, qualified online devices will receive the firmware information pushed from EnOS. Offline devices will receive the upgrade request after backing online next time.

Downstream TOPIC:

- Request topic: `/sys/${productkey}/${devicekey}/ota/device/upgrade`
- Response:  `/sys/${productkey}/${devicekey}/ota/device/upgrade_reply`

Request data format:
```JSON
{
    "id":"123",
    "version":"1.0",
    "method":"ota.device.upgrade",
    "params": {
        "version":"v1.0",
        "sign":"xxxxxxx",
        "signMethod":"md5",
        "fileUrl":"/1b30e0ea83002000/ota/kadak13",
        "fileSize":1024
    }
}
```

Response data format:
```JSON
{
    "id": "123",
    "code": 200,
    "data": {}
}
```

Parameter descriptions:

.. list-table::
   :widths: auto

   * - Parameters
     - Type
     - Required or Optional
     - Description
   * - id
     - Long
     - Optional
     - Message ID (reserved value)
   * - version
     - String
     - Required
     - Protocol version. 1.0 at present.
   * - method
     - String
     - Required
     - Request method, which must be ota.device.getversion
   * - params
     - Object
     - Optional
     - None
   * - code
     - Integer
     - Required
     - Result return code. 200 indicates success.
   * - data
     - List
     - Optional
     - Returned detail in JSON format
   * - version
     - String
     - Required
     - Firmware version 
   * - sign
     - String
     - Required
     - Firmware signature
   * - signMethod
     - String
     - Required
     - Firmware signature algorithm
   * - fileUrl
     - String
     - Required
     - Firmware file url
   * - fileSize
     - Long
     - Required
     - Firmware file size in bytes

### Reporting Upgrade Progress

After the device starts to download the firmware, it reports its upgrade progress or reasons for upgrade failure through this topic. You can view the upgrade progress in the console.

Upstream TOPIC:
- Request topic: `/sys/${productkey}/${devicekey}/ota/device/progress`
- Response topic: `/sys/${productkey}/${devicekey}/ota/device/progress_reply`

Request data format:
```JSON
{
    "id":"123",
    "version":"1.0",
    "method":"ota.device.progress",
    "params": {
        "step":"1",
        "desc":"xxxxxxxx"
    }
}
```

Response data format:
```JSON
{
    "id": "123",
    "code": 200,
    "data": {}
}
```

Parameter descriptions:

.. list-table::
   :widths: auto

   * - Parameters
     - Type
     - Required or Optional
     - Description
   * - id
     - Long
     - Optional
     - Message ID (reserved value)
   * - version
     - String
     - Required
     - Protocol version. 1.0 at present.
   * - method
     - String
     - Required
     - Request method, which must be ota.device.process
   * - params
     - Object
     - Required
     - Reported progress parameter
   * - step
     - String
     - Required
     - Reported upgrade progress. Its normal range is [0, 100], where -1 indicates failure; -2 indicates download failure; -3 indicates verification failure; -4 indicates burning failure
   * - desc
     - String
     - Optional
     - Description for current step, which may contain error information in case of any exception
   * - code
     - Integer
     - Required
     - Result return code. 200 indicates success.

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 764 | Device task not found | OTA task of this device is not found |
| 765 | Device task not start | The device has not started OTA upgrade yet |


### Requesting Upgrade

In case of non-forced upgrade, the upgrade task for device to request actively upgrade may be created in the console; at this point, the cloud will not actively push any upgrade request but wait until the device requests actively upgrade to obtain upgradeable version information; if there are multiple versions available in the cloud, a list of upgradeable versions will be returned and the device or its owner may select a proper one from it for upgrade purpose.


Upstream TOPIC

- Request topic: `/sys/${productkey}/${devicekey}/ota/device/getversion`
- Response topic:  `/sys/${productkey}/${devicekey}/ota/device/getversion_reply`

Request data format

```JSON
{
    "id":"123",
    "version":"1.0",
    "method":"ota.device.getversion",
    "params": {
    }
}
```

Response data format:

```JSON
{
    "id":"123",
    "code":200,
    "data": [
         {
            "version":"v1.0",
            "sign":"xxxxxxx",
            "signMethod":"md5",
            "fileUrl":"/1b30e0ea83002000/ota/kadak13",
            "fileSize":1024
        }
    ]
}
```


Parameter descriptions:

.. list-table::
   :widths: auto

   * - Parameters
     - Type
     - Necessary or not
     - Description
   * - id
     - Long
     - Optional
     - Message ID (reserved value)
   * - version
     - String
     - Required
     - Protocol version. 1.0 at present
   * - method
     - String
     - Required
     - Request method, which must be ota.device.getversion
   * - params
     - Object
     - Optional
     - None
   * - code
     - Integer
     - Required
     - Result return code. 200 indicates success.
   * - data
     - List
     - Optional
     -  Returned detailed information in JSON format. If there are multiple available versions for this device, the information for multiple versions will be returned
   * - version
     - String
     - Required
     - Firmware version
   * - sign
     - String
     - Required
     - Firmware signature
   * - signMethod
     - String
     - Required
     - Firmware signature algorithm
   * - fileUrl
     - String
     - Required
     - Firmware file URL
   * - fileSize
     - Long
     - Required
     - Firmware file size in bytes


### Exception Handling

The exceptions that occur during a firmware upgrade is indicated by _step_ and _desc_ when the device reports the firmware upgrade progress. A _step_ less than 0 indicates exception. _desc_ contains description to this exception.

Values of step provided by EnOS are as follows:

.. list-table::
   :widths: auto

   * - step value
     - Meaning
   * - -1
     - Upgrade failure
   * - -2
     - Download failure
   * - -3
     - Verification failure
   * - -4
     - Burning failure

In case of other custom exceptions, set _step_ less than -4 and _desc_ as the description to that exception.
