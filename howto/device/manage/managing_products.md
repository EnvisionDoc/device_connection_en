# Managing Products

On the **Product** page, you can perform operations such as enabling dynamic registration, viewing device logs, and debugging devices.

## Before You Start

To manage products, you must have write access to the **Connectivity Management** service. If you don't have the access, contact your OU administrator to obtain the access. For more information about user access in EnOS, see [Policy, Roles, and Access](/docs/iam/en/2.0.9/access_policy).

## Viewing Logs

On the **Product Details** page, you can view the logs of important behaviors of all the devices under the product.

1. In the EnOS Console, select **Device Management > Product**.

2. In the table of created products, for the target product, click **View** to open the **Product Details** page.

3. Click the **Logs** tab to open the log details page.

4. Under the **Activity Logs** tab, enter the target device key and set a time period to view the log of that device for the set period of time.

5. To view logs of upstream and downstream messages, click the **Upstream** and **Downstream** tabs.

   - The **Upstream** page mainly displays the logs of the following upstream messages:

     - Messages sent by the device to the topic queue
     - Messages flowed from the topic queue to the rules engine
     - Messages forwarded from the rules engine to other services and applications in the EnOS Cloud

   - The **Downstream** page mainly displays the logs of messages sent from the cloud to the device.
  <!--This requires a list of error codes-->

## Debugging Online Devices

On the **Product Details** page, you can debug devices under the product, simulate a real device to connect to the cloud, report defined attributes, and handle events. Based on the data of the simulated device, the cloud can complete application development and debugging. Once the real device is online, the virtual device will automatically go offline.

1. In the EnOS Console, select **Device Management > Product**.

2. In the table of created products, for the target product, click **View** to open the **Product Details** page.

3. Click the **Debugging** tab to open the debugging page.

4. Select the device you want to debug from the drop-down list.

5. Simulate a scenario using the debugging feature.

6. Click **Run**.

<!--Ask Xu Wei for a list-->
