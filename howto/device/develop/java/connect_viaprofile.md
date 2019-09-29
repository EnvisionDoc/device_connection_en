# Connecting to EnOS by using profiles

In addition to connecting to the server by using dynamic codes, SDK also provides a way of connection by using profiles, where what the user needs to do is to configure the profiles in advance and provide necessary parameter information.

```java
//store config by mqtt sdk
//Mon Jan 14 20:39:20 CST 2019
{
  "serverUrl": "tcp://10.27.21.6:11883", //mandatory
  "productKey": "fKInRgVP", //mandatory
  "deviceKey": "zscai_test_activate",//mandatory
  "productSecret": "c3PW03srd6c",//needed when login by securemode=3
  "deviceSecret":"", //needed when login by securemode=2
  "operationTimeout": 60,//timeout seconds of each operation
  "keepAlive": 60,//conneciton keepAlive  seconds
  "connectionTimeout": 30, //connect timeout seconds
  "maxInFlight": 10000, //maxInFlight mqtt requests
  "autoReconnect": true,//auto reconnect the mqtt client when connect lost
  "sslSecured":false,//enable the ssl connection
  "sslAlgorithm": "SunX509",//ssl algorithm
  "sslJksPath": "",//path of jks file
  "sslPassword": "",//password of jks file
  "subDevices": //if subDevies is config, subdevice will auto login by sdk when mqtt connection build
   [
    {
      "productKey": "subProduct",
      "deviceKey": "subdevice",
      "deviceSecret": "xxx"
    },
    {
      "productKey": "subProduct",
      "deviceKey": "subdevice",
      "deviceSecret": "xxx"
    }
  ]
}
```

Where: regionURL, productKey and deviceKey are mandatory parameters, while either deviceSecret or productSecret must be provided to complete the necessary parameter signature verification.
If the deviceSecret parameter is provided in the profiles, securemode=2 will be used for login; if not, the productSecret will be provided so that securemode=3 will be used for login.
The user can use the codes given below to complete the connection by means of profiles.

Note that, if the sub-device information is configured in the profiles, SDK will make the sub-devices log in automatically after successful connection, where *such sub-devices need to be pre-configured in the cloud to become the sub-devices of this getaway device*.  If the user needs to manually manage the status of sub-devices, the sub-devices may not be configured in the profiles, and the user can manually manage the go-live of sub-devices based on SubDeviceLoginRequest.
Similarly, if the sub-devices use the securemode=3 dynamic activation mode for login, the cloud will return the corresponding device key to the device end, and the device end should perpetually save the device key information, which will be used later for signature authentication in login. Likewise, this process is also encapsulated in java sdk.

The sample codes for connecting the server by using profiles are given as follows:

```java
package com.envision.energy.enos_mqtt_sample;


import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
import com.envisioniot.enos.iot_mqtt_sdk.core.internals.SignMethod;
import com.envisioniot.enos.iot_mqtt_sdk.core.profile.FileProfile;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.File;

public class ConfigFileBasedConnectExample {
    private final static Logger LOG = LoggerFactory.getLogger(ConfigFileBasedConnectExample.class);

    /**
     * Refer to /docs/device-connection/en/latest/learn/deviceconnection_authentication.html
     * on how to create an SSL jks file.
     */
    private final static String SSL_JKS_PATH = "<path>";
    private final static String SSL_PASSWORD = "<password>";

    private final static String SSL_SERVER_URL = "ssl://{regionUrl}:18883";

    private final static String PRODUCT_KEY = "<productKey>";
    private final static String DEVICE_KEY = "<deviceKey>";
    private final static String DEVICE_SECRET = "<deviceSecret>";

    private final static File CONFIG_FILE = new File(System.getProperty("java.io.tmpdir"), DEVICE_KEY + ".config");

    private static void persistConfigFile() throws Exception {
        final String absPath = CONFIG_FILE.getAbsolutePath();
        System.out.println("profile abs path: " + absPath);

        FileProfile profile = new FileProfile(absPath);
        profile.setProductKey(PRODUCT_KEY);
        profile.setDeviceKey(DEVICE_KEY);
        profile.setDeviceSecret(DEVICE_SECRET);
        profile.setSignMethod(SignMethod.SHA256);
        profile.setKeepAlive(300);
        profile.setAutoReconnect(true);

        // Use SSL for connection
        profile.setServerUrl(SSL_SERVER_URL);
        profile.setSSLSecured(true);
        profile.setSSLJksPath(SSL_JKS_PATH, SSL_PASSWORD);

        profile.persist();
    }

    public static void main(String[] args) throws Exception {
        if (!CONFIG_FILE.exists()) {
            persistConfigFile();
        }

        // Use the configuration file for connection.
        // We don't need to explicitly provide configuration again.
        FileProfile profile = new FileProfile(CONFIG_FILE.getPath());
        final MqttClient client = new MqttClient(profile);

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