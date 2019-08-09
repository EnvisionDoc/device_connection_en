# Device and Cloud Security

To ensure the security of the devices and data transmission, the device needs to be authenticated before exchanging data with the EnOS IoT Hub. EnOS supports _Secret-based One-way Authentication_ and _Certificate-based Two-way Authentication_.

The overall authentication process for device connection is as follows:

1. Create a device in **Device Management > Device** .

2. Configure the device information in an EnOS Edge.

3. Connect Edge to EnOS cloud. The device authentication keys will be sent to EnOS cloud for authentication.

   - If authentication succeeds, the device gets online and can communicate with EnOS cloud.
   - If authentication fails, the connection is closed.

The specific flow chart is as follows:

.. image:: ../media/secret_communication.png

## Secret-based Authentication

Secret-based authentication refers to the authentication of devices based on authentication keys, i.e. `ProductKey`, `DeviceKey` and `DeviceSecret`.

Before discussing the secret-based authentication mechanism, you need to understand the following concepts:

- **Product authentication pair**: `ProductKey` and `ProductSecret`.

  - `ProductKey`: Globally unique identifier issued by EnOS to the product.
  - `ProductSecret`: The product secret issued by EnOS, which is paired with `ProductKey`. It's used for dynamic activation of devices. To initiate the dynamic activation, a device sends a request containing `ProductKey`, `ProductSecret` and `DeviceKey` to EnOS for `DeviceSecret` upon its first connection to EnOS.

- **Device authentication pair**: `DeviceKey` and `DeviceSecret`.

  - `DeviceKey`: A globally unique device identifier that is user-defined or auto-generated when a user registers a device on EnOS. 
  - `DeviceSecret`: The device secret issued by EnOS, which is paired with `DeviceKey`.

### Device Authentication Operations 

Perform the following operations in the following order to connect a device to EnOS:

1. **Registration**

  Create a device instance in EnOS by either using the console or calling an API. The device is now **Inactive**.

2. **Authentication**

  You can either dynamically or statically authencitate a device. Once the device is authenticated, its state changes from **Inactive** to **Online**. If an **Online** device has not send any data to EnOS within specified time range, it becomes **Offline**.

### Device Authentication Mode and Device States

You can dynamically or statically activate the device.

- **Dynamic Authentication**: To use dynamic authentication, go to **Device Management > Product** . Find the product that the device is created from and click **View** in the **Operations** column. In **Basic Information** tab page, enable the **Enable Dynamic Activation** switch. A device can be authenticated as follows:

  1. After being created on EnOS, the device sends a request carrying the `ProductKey`, `ProductSecret`, and `DeviceKey` for `DeviceSecret`. If the authentication is successful, a `DeviceSecret` will be returned to the device.

  2. The device tries to get authenticated with the `ProductKey`, `DeviceKey`, and `DeviceSecret`.

  Once the device has been authenticated, its status will change from **Inactive** to **Online**. The device will then be able to send data to EnOS. If no data is sent within a certain period of time, the status of the device will change to **Offline**.

- **Static Authentication**: This is the default way of authentication. Devices using this authentication method carry the `DeviceSecret` given when created on EnOS, together with `ProductKey` and `ProductSecret`. The process of static activation is as follows:

  1. The device sends an authentication request carrying the `ProductKey`, `DeviceKey`, and `DeviceSecret`.

  2. Once the device is successfully authenticated, its status will change from **Inactive** to **Online**. The device will now be able to communicate with EnOS. If no data is sent within a certain period of time, the status of the device will be changed to **Offline**.

When the device is not working properly or you do not want to receive its data, set it to **Disabled**. The device will then go **Offline**.

## Certificate-based Authentication

The secret-based authentication refers to device identity authentication with authentication keys. It is a one-way authentication mechanism, that is, the IoT Hub validates the identity of the device, however, the device does not verify the identity of the IoT Hub. To enforce two-way authentication, the certificate-based authentication mechanism can be used.

When certificate-based authentication is enabled，EnOS enforces the following security schemes to secure the connection between the EnOS Edge and EnOS IoT Hub:

- The communication between the EnOS Edge and EnOS IoT Hub is based on certificate-based bi-directional authentication.
- Suppors RSA algorithm to verify signature and uses a 2048-bit RSA key

Before discussing the certificate-based two-way authentication mechanism, you need to understand the following concepts:

- **Certificate Authority (CA)**: An entity that issues digital certificates.
- **CA certificate**: A digital certificate issued by CA. It conforms to the certificate format defined by the [IETF RFC 5280](https://tools.ietf.org/html/rfc5280) specification.
- **Device certificates**: The certificates signed and issued by the CA certificate owner. Device certificates are generated in the following process:

  - Authentication keys are generated when you create a device on EnOS.
  - A certificate signing request (CSR) is generated based on the authentication keys.
  - The CSR is sent to the CA.
  - The CA certificate signs and issues the device certificates and registers the device certificates in CA. Meanwhile, the device certificates are also stored in the devices.

### X.509 Certificate Authentication

#### Setup Phase

The following diagram illustrates the process of secure communication between the edge and EnOS cloud based on X.509 certificate:

**1. EnOS cloud acquires X.509 certificate**

.. image:: ../media/certificate_service_secure_communication_01.png

1a. EnOS cloud creates authentication keys and CSR locally, acquires the X.509 certificate with the CSR by calling the X.509 certificate service API.

1b. CA issues the X.509 certificate and sends the certificate to EnOS cloud.

1c. EnOS cloud receives and stores the X.509 certificate.

**2. Edge acquires X.509 certificate**

.. image:: ../media/certificate_service_secure_communication_02.png

2a. Edge devices are pre-configured with `ProductKey`, `ProductSecret`, and device serial number (SN) stored in themselves. When powered on and connected to the network, Edge reports its `ProductKey`, `ProductSecret`, and serial number to EnOS cloud for dynamic activation. EnOS cloud returns the `DeviceSecret` to Edge if authentication is successful.

2b. On EnOS cloud, the serial number of the Edge device is used as the `DeviceKey` to create the Edge device instance. The device can be created either on the EnOS Console or by calling the API.

2c. Edge receives a response from EnOS cloud, creates authentication keys and CSR, and calls the API to obtain its X.509 certificate. Meanwhile, the device authentication keys are used to log the device in to the cloud, and the device will be activated upon its first successful login.

2d. EnOS cloud receives the CSR. After verifying its identity, forwards the CSR to CA.

2e. The CA receives the CSR, issues the edge certificate and sends the certificate to EnOS cloud.

2f. EnOS cloud receives the issued X.509 certificate, binds it with the device id, and then sends the edge certificate to the Edge.

2g. The edge receives the certificate, saves it locally, for example, in the Trusted Platform Module (TPM).

#### Communication Phase

**3. Edge communicates with EnOS cloud, authenticated with X.509 certificate.**

.. image:: ../media/certificate_service_secure_communication_03.png

3a. Edge validates the certificate of EnOS cloud.

3b. EnOS cloud validates the certificate of Edge.

When authentication succeeds, connection is established between Edge and EnOS cloud.

#### Certificate Revocation

In some scenarios, user needs to revoke the X.509 certificate granted to Edge. The following diagram illustrates the revocation process.

**4. EnOS cloud revokes the X.509 certificate granted to Edge**

.. image:: ../media/certificate_service_secure_communication_04.png

4a. EnOS calls an API to revoke the X.509 certificate. 

4b. The CA receives the revoke request from EnOS cloud, authenticates the request, revokes the certificate, and updates the CRL.

### Edge Security Best Practices

In the certificate-based authentication, consider using the following best practices:

- Create a private key for Edge and keep it secret in a storage such as TPM.
- Use TLS 1.2 when communicating with EnOS cloud, and verify that the server certificate is valid.
- Each Edge must have a unique public/private key pair.
- The authentication keys used for authentication by EnOS cloud should not be used for other purposes.
- Revoke the authentication key when the Edge is reset.
- Make sure your operating system on which Edge runs is secured through certain mechanisms, for example, firewall.
- Ensure that you have a way to update root certificates and CRL.
- Ensure that the clock on the Edge is not tampered with.

## Next Steps

EnOS enforces the secret-based authentication mechanism by default. The keys and secrets are generated or specified when you [Creating a Product](../howto/device/manage/creating_product) and [Creating a Device](../howto/device/manage/creating_device).

When you enable the certificate-based authentication, you need to use the certificate APIs to generate the device certificates as instructed in [Getting Started with Certificate-Based Authentication](../quickstart/gettingstarted_java_ssl_connection) to establish secure connection between devices and IoT Hub.
