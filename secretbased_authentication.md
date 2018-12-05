# Secret-based One-way Authentication

Secret-based authentication refers to the authentication of devices based on device triple, i.e. `ProductKey`, `DeviceKey` and `DeviceSecret`.

## Concepts

The following operations and states are involved in the connection between the device and the EnOS IoT Hub:

- **Registration**
  A device is created in the cloud either on the interface through Portal or by calling the Restful interface.

- **Login**
  The device must be successfully logged in before it can send data. The device trigraph authentication is required for login.

- **Activation**
  The device will be activated upon its first successful login, which updates its status from **Inactive** to **Active**. The **Active** status contains two sub-statuses: **Online** and **Offline**.


## Device Activation Mode and Device Status

Devices created for the first time on the EnOS platform are enabled but not activated by default; they are waiting to be activated. Devices can be activated in two ways: dynamic activation, and static activation.
- **Dynamic Activation**: The process of dynamic activation is as follows:
  1. When trying to connect for the first time, the device will carry the `ProductKey`, `ProductSecret`, and `DeviceKey` to request for activation. If the authentication is successful, a `DeviceSecret` will be returned to the device.
  2. The device tries to log in using the `ProductKey`, `DeviceKey`, and `DeviceSecret`.
  3. Once the device is successfully logged in, its status will change from **Inactive** to **Online**. The device will now be able to send data. If no data is sent within a certain period of time, the status of the device will change to **Offline**.

  To use dynamic activation, you will need to enable **Dynamic Activation** in the product configuration.

- **Static Activation**: The process of static activation is as follows:
  1. The device sends a login request carrying the `ProductKey`, `DeviceKey`, and `DeviceSecret`.
  2. Once the device is successfully logged in, its status will change from **Inactive** to **Online**. The device will now be able to send data. If no data is sent within a certain period of time, the status of the device will change to **Offline**.

When the device is not working properly or you do not want to receive its data, you can set it to **Disabled**, after which the device will automatically go offline and its status will change to **Offline**.

Three dimensions are used to describe the overall state of the device: Operational State, Activation State, and Communication State, as shown in the following table:

<table>
   <tr>
     <th>Operational State</th>
     <th>Activation State</th>
     <th>Communication State</th>
   </tr>
   <tr>
     <td>Disable</td>
     <td></td>
     <td>Offline</td>
   </tr>
   <tr>
     <td>Enable</td>
     <td>Inactive</td>
     <td></td>
   </tr>
   <tr>
     <td></td>
     <td>Activated</td>
     <td>Online</td>
   </tr>
   <tr>
     <td></td>
     <td></td>
     <td>Offline</td>
   </tr>
</table>


## Authentication Process

The main authentication process for device connection is as follows:
- a. Pre-register device in the cloud
- b. On the edge side, configure the device information registered in the cloud, and mainly burn into the device trigraph
- c. Power-on on edge side, connect to network, and try to log in. Device trigraph authentication in the cloud
  - Successful: Returned successfully, the device is instructed to send data
  - Failure: Return failed, connection is interrupted
- d. The device sends data via MQTT protocol
- e. The cloud issues instructions via the MQTT protocol
- f. The device side responds to the cloud requests

The specific flow chart is as follows:
![](media/secret_communication.png)