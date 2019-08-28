# Send Command to Device

The following section introduces how to handle the downstream commands sent from EnOS cloud to device. On the device end, response messages can be configured in the `onMessage` callback function, so that the SDK can respond the messages to the cloud. You can configure the error codes and error messages to indicate failure of commands to the device. You can choose to use 2000 and above numbers for such custom error codes and error messages.

In the following code sample, the setting measurepoint and disabling device event are monitored.

```java
package com.envision.energy.enos_mqtt_sample;

import com.envisioniot.enos.iot_mqtt_sdk.core.IConnectCallback;
import com.envisioniot.enos.iot_mqtt_sdk.core.MqttClient;
import com.envisioniot.enos.iot_mqtt_sdk.core.login.LoginInput;
import com.envisioniot.enos.iot_mqtt_sdk.core.login.NormalDeviceLoginInput;
import com.envisioniot.enos.iot_mqtt_sdk.core.msg.IMessageHandler;
import com.envisioniot.enos.iot_mqtt_sdk.core.profile.DefaultProfile;
import com.envisioniot.enos.iot_mqtt_sdk.message.downstream.tsl.ServiceInvocationCommand;
import com.envisioniot.enos.iot_mqtt_sdk.message.downstream.tsl.ServiceInvocationReply;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.List;
import java.util.Map;
import java.util.Timer;
import java.util.TimerTask;

public class ServiceInvokeExample {
    private final static Logger LOG = LoggerFactory.getLogger(ServiceInvokeExample.class);

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

                // Set service handler to handle service command from cloud
                client.setArrivedMsgHandler(ServiceInvocationCommand.class, createServiceCommandHandler(client));
                LOG.info("waiting commands from cloud");
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

    private static IMessageHandler<ServiceInvocationCommand, ServiceInvocationReply> createServiceCommandHandler(final MqttClient client) {
        return (ServiceInvocationCommand request, List<String> argList) -> {
            LOG.info("receive command: {}", request);

            // argList: productKey, deviceKey, serviceName
            // If the request is for sub-device, the productKey and deviceKey
            // are used to identify the target sub-device.
            String productKey = argList.get(0);
            String deviceKey = argList.get(1);
            String serviceName = argList.get(2);
            LOG.info("productKey: {}, deviceKey: {}, serviceName: {}, params: {}",
                    productKey, deviceKey, serviceName, request.getParams());

            if (serviceName.equals("sample.echo")) {
                Map<String, Object> params = request.getParams();
                String message = (String) params.get("message");
                LOG.info("arg message: {}", message);

                // Set the reply result
                return ServiceInvocationReply.builder().addOutputData("reply", message).build();
            } else if (serviceName.equals("sample.disconnect")) {
                Map<String, Object> params = request.getParams();
                int delayMS = (Integer) params.get("delayMS");
                LOG.info("arg delay: {}", delayMS);

                final Timer timer = new Timer();
                timer.schedule(new TimerTask() {
                    @Override
                    public void run() {
                        LOG.info("now close connection ...");
                        client.close();
                        timer.cancel();
                    }
                }, delayMS);
                return ServiceInvocationReply.builder().build();
            }

            return ServiceInvocationReply.builder().setMessage("unknown service: " + serviceName).setCode(220).build();
        };
    }
}

```

.. note:: If the user responds a null reply, the device side does not respond after executing the callback method. If the user responds an effective reply, the SDK will serialize the reply and send it to the cloud automatically. If the callback method is executed successfully, the user can set a response code 200 or not. For failed execution, the user can set a customized error code 2000 or above to be sent to the cloud. 

<!--End-->