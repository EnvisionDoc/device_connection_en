# Connecting to EnOS Cloud through SSL/TLS

To ensure device security, you can enable the certificate-based authentication so that the device connects to the cloud through SSL/TLS. You can apply device certificate by calling the EnOS certificate service API, load the certificate to the SDK directory, and then connect to the server through the SSL port. The server URL format is `ssl://{regionUrl}:18883`. See the code sample below.

```java
package com.envision.energy.enos_mqtt_sample;


import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
import com.envisioniot.enos.iot_mqtt_sdk.core.internals.SignMethod;
import com.envisioniot.enos.iot_mqtt_sdk.core.login.LoginInput;
import com.envisioniot.enos.iot_mqtt_sdk.core.login.NormalDeviceLoginInput;
import com.envisioniot.enos.iot_mqtt_sdk.core.profile.DefaultProfile;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class SecureConnectSample {
    private final static Logger LOG = LoggerFactory.getLogger(SecureConnectSample.class);

    /**
     * Refer to https://www.envisioniot.com/docs/device-connection/zh_CN/latest/learn/deviceconnection_authentication.html
     * on how to create an SSL jks file.
     */
    private final static String SSL_JKS_PATH = "<SslPath>";
    private final static String SSL_PASSWORD = "<Password>";

    private final static String SSL_SERVER_URL = "ssl://{regionUrl}:18883";

    private final static String PRODUCT_KEY = "<productKey>";
    private final static String DEVICE_KEY = "<deviceKey>";
    private final static String DEVICE_SECRET = "<deviceSecret>";

    public static void main(String[] args) {

        LoginInput input = new NormalDeviceLoginInput(SSL_SERVER_URL, PRODUCT_KEY, DEVICE_KEY, DEVICE_SECRET);
        final MqttClient client = new MqttClient(new DefaultProfile(input));
        client.getProfile()
                .setKeepAlive(300)
                .setAutoReconnect(true)
                .setSignMethod(SignMethod.SHA256)
                // The following settings are for SSL connection.
                .setSSLSecured(true)
                .setSSLJksPath(SSL_JKS_PATH, SSL_PASSWORD);

        client.connect(new IConnectCallback() {
            public void onConnectSuccess() {
                LOG.info("onConnectSuccess");
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


}


```

You can also use the `setSSLContext()` method to directly set the SSL context, load the certificate content, and complete initializing the MQTT client with certificate-base bi-directional authentication. 
