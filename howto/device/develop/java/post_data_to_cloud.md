# Send Data to EnOS Cloud

Use the following code to connect the device to EnOS Cloud.

In the following sample:
- `serverUrl` is the address of the server. If using TCP connection, the format of the server URL can be `tcp://{regionUrl}:11883`. 
- The <`productKey`, `deviceKey`,`deviceSecret`> (or <`productKey`, `deviceKey`,  `productSecret`> when the device is to be dynamically activated) are the device credentials issued by EnOS when you register the device. For more information about device registeration, see [Connecting Smart Device into EnOS Cloud](/docs/device-connection/en/2.0.8/quickstart/gettingstarted_device_connection).

```java
package com.envision.energy.enos_mqtt_sample;

import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
import com.envisioniot.enos.iot_mqtt_sdk.core.login.LoginInput;
import com.envisioniot.enos.iot_mqtt_sdk.core.login.NormalDeviceLoginInput;
import com.envisioniot.enos.iot_mqtt_sdk.core.profile.DefaultProfile;
import com.envisioniot.enos.iot_mqtt_sdk.message.upstream.tsl.MeasurepointPostRequest;
import com.envisioniot.enos.iot_mqtt_sdk.message.upstream.tsl.MeasurepointPostResponse;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.Random;

public class MeasurePointPublishSample {
    private final static Logger LOG = LoggerFactory.getLogger(MeasurePointPublishSample.class);

    private final static String PLAIN_SERVER_URL = "tcp://{regionUrl}:11883";

    private final static String PRODUCT_KEY = "productKey";
    private final static String DEVICE_KEY = "deviceKey";
    private final static String DEVICE_SECRET = "deviceSecret";

    public static void main(String[] args) {
        LoginInput input = new NormalDeviceLoginInput(PLAIN_SERVER_URL, PRODUCT_KEY, DEVICE_KEY, DEVICE_SECRET);
        final MqttClient client = new MqttClient(new DefaultProfile(input));

        client.connect(new IConnectCallback() {
            public void onConnectSuccess() {
                LOG.info("onConnectSuccess");
                publishMeasurePoints(client);
                client.close();
            }

            public void onConnectLost() {
                LOG.info("onConnectLost");
                client.close();
            }

            public void onConnectFailed(int reason) {
                LOG.info("onConnectFailed");
                client.close();
            }
        });
    }


    private static void publishMeasurePoints(final MqttClient client) {
        try {
            MeasurepointPostRequest request = MeasurepointPostRequest.builder()
                    .addMeasurePoint("sample.float_mp", new Random().nextFloat())
                    .addMeasurePoint("sample.string_mp", "test.string.mp")
                    .build();

            MeasurepointPostResponse response = client.publish(request);
            if (response.isSuccess()) {
                LOG.info("measure points are published successfully");
            } else {
                LOG.error("failed to publish measure points, error: {}", response.getMessage());
            }
        } catch (Exception e) {
            LOG.error("failed to publish measure points", e);
        }
    }
}
```

When connection is successful, commands can be sent from device to EnOS Cloud. The following code is for login operation of a sub-device in a callback function.  

If network connection is broken for gateway devices, the service will regard all the sub-devices in the topology of the gateway device as offline. While the MQTT client allows automatic re-connection, when the network connection recovers, the `onConnectLost` callback function will be invoked. When the re-connection takes effect, the online logic of the sub-devices will be included in the `onConnectSuccess` callback method.

```java
@Override
public void onConnectSuccess()
{
    try
    {
        System.out.println("start register login sub-device , current status : " + client.isConnected());
        SubDeviceLoginRequest request = SubDeviceLoginRequest.builder().setSubDeviceInfo(subProductKey, subDeviceKey, subDeviceSecret).build();
        SubDeviceLoginResponse rsp = client.publish(request);
        System.out.println(rsp);
    }
    catch (Exception e)
    {
        e.printStackTrace();
    }
}
```

.. note:: Note that the `productKey` of the sub-device should be retrieved from the EnOS Console or through EnOS REST API. The `deviceKey` and `deviceSecret` can also be registered through the `DeviceRegisterRequest` of the SDK.

Now, use the following code sample to send the measurepoint data of a sub-device to the cloud.

```java
public static void postMeasurepoint()
{
      Map<String, String> struct = new HashMap<>();
      struct.put("structKey", "structValue");
      MeasurepointPostRequest request = MeasurepointPostRequest.builder()
                     .setProductKey(subProductKey).setDeviceKey(subDeviceKey)
                     .addMeasurePoint("p1", "string")
                     .addMeasreuPointWithQuality("p2", "value", 2)
                     .addMeasurePoint("p3", 100.2)
                     .addMeasurePoint("p4", struct)
                     .build();
      client.fastPublish(request);
}
```

In this sample, the `fastPublish` method is used. In this way, no response is provided for measurepoints. There are another 2 publish methods in the client.

```java
/**
 * publish the sync request and wait for the response
 * @throws Exception
 */
public <T extends IMqttResponse> T publish(IMqttRequest<T> request) throws Exception

/**
 * publish the request and register the callback , the callback will be called when rcv the response
 * @throws Exception
 */
public <T extends IMqttResponse> void publish(IMqttRequest<T> request, IResponseCallback<T> callback)
```

The difference is that:
- The publish method with callback is asynchronous
- The publish method with response parameters is synchronous. 

Note that if MeasurepointPostRequest calls a push command wongly,  the service does not respond any error messages until session timeout.

The device end provides an API for uploading measurepoint data with quality which you can use to send measurepoint data with quality.
<!--what is the name of this API?-->