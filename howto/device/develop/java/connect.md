# Connecting to EnOS Cloud

Use the following code to connect the device to EnOS Cloud.

In the following sample:
- `serverUrl` is the address of the server. If using TCP connection, the format of the server URL can be `tcp://{regionUrl}:11883`. 
- The <`productKey`, `deviceKey`,`deviceSecret`> (or <`productKey`, `deviceKey`,  `productSecret`> when the device is to be dynamically activated) are the device credentials issued by EnOS when you register the device. For more information about device registeration, see [Connecting Smart Device into EnOS Cloud](/docs/device-connection/en/2.0.9/quickstart/gettingstarted_device_connection).


```java
package com.envision.energy.enos_mqtt_sample;

import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
import com.envisioniot.enos.iot_mqtt_sdk.core.internals.SignMethod;
import com.envisioniot.enos.iot_mqtt_sdk.core.login.LoginInput;
import com.envisioniot.enos.iot_mqtt_sdk.core.login.NormalDeviceLoginInput;
import com.envisioniot.enos.iot_mqtt_sdk.core.profile.DefaultProfile;
import com.google.common.base.Preconditions;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class ConnectSample {
    private final static Logger LOG = LoggerFactory.getLogger(ConnectSample.class);
    private final static String PLAIN_SERVER_URL = "tcp://{regionUrl}:11883";

    private final static String PRODUCT_KEY = "<productKey>";
    private final static String DEVICE_KEY = "<deviceKey>";
    private final static String DEVICE_SECRET = "<deviceSecret>";

    private final static boolean USE_ASYNC_WAY = true;

    public static void main(String[] args) {

        LoginInput input = new NormalDeviceLoginInput(PLAIN_SERVER_URL, PRODUCT_KEY, DEVICE_KEY, DEVICE_SECRET);
        final MqttClient client = new MqttClient(new DefaultProfile(input));

        // The connection will be closed if there is no data communication between the device and EnOS

        client.getProfile().setKeepAlive(300);

        // Enable auto reconnection then the device will try to reconnect when the connection is closed.
        // Note that reconnection happens only if the initial connection succeeded.
        client.getProfile().setAutoReconnect(true);

        // Use SHA-256 for authentication
        client.getProfile().setSignMethod(SignMethod.SHA256);

        if (USE_ASYNC_WAY) {
            /*
             * No block when async connection is used.
             * When auto reconnection is enabled, the following callback functions will be called every time a reconnection occurs.
             * You should avoid initializing resources multiple times.
             */
            client.connect(new IConnectCallback() {
                public void onConnectSuccess() {
                    LOG.info("onConnectSuccess");
                    afterConnect(client);

                    // Close the MQTT client when there is no more data
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

            LOG.info("connection is not ready here");
        } else {
            /**
             * Returns the connection if success, else the
			 * connection will be dropped.
             */
            try {
                client.connect();
                afterConnect(client);
            } catch (Exception e) {
                LOG.info("failed to connect: " + e.getMessage());
                client.close();
            }

        }
    }

    private static void afterConnect(final MqttClient client) {
        Preconditions.checkArgument(client.isConnected(), "connection must be ready here");
        LOG.info("connection is ready now");

        // Publish data from here.
        // .....
    }

}


```

