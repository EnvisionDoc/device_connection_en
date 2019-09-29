# About Device Management

EnOS™ Device Management Offering helps you quickly and securely connect physical devices to EnOS Cloud and start to transfer data, manage device lifecycle, and map the physical asset structure to the digital world.

## Device Connectivity

Supports bi-directional communication between the device and the cloud:
- Data ingestion from device to cloud
- Remote control from cloud to device

Provides device SDK to enable devices to transmit messages to the EnOS IoT Hub.

- Provides MQTT device SDK to meet the real-time requirements for long connections.
- Provides CoAP device SDK to meet the low power requirements for short connections.
- Support SDKs for Java, C, and Python programing languages.

Supports various connection schemes and provides solutions to meet the requirements of various scenarios for the heterogeneous network devices of enterprises. For more information, see [Device Connectivity](learn/connection_scenarios).

## Device Lifecycle Management

Supports complete device lifecycle management, including:

- Device registration
- Device configuration
- Firmware upgrade
- Real-time monitoring
- Device decommision

For more information, see [Device Lifecycle Management](learn/device_lifecycle_management).

## Device and Cloud Security

- Supports _secret-per-device_ authentication mechanism, reducing the security risk that the device may be hacked. This machanism is suitable for devices that can be burned with pre-allocated device key into each chip in batches. Each device carries a unique key-secret pair as the device leaves the factory.

- Supports _secret-per-product_ mechanism, where devices of the same product model are pre-burned with key-secret pair (the _product key-secret pair_). The device can dynamically acquire the device secret during authentication. This machanism is suitable for situations where unique key-secret pairs cannot be burned into each device in mass production.

- Supports -certificate-based-authenticate, where data is encrypted and decrypted through the CA certificate to ensure the security of communication between the device and the cloud.

For more information, see [Device and Cloud Security](learn/deviceconnection_authentication).

## Target Personas

EnOS Device Management Service primarily serves the following roles:

### IoT Engineer

IoT Engineer, who performs the on-site installation, including installing the edge gateway devices and connecting the cables between edge gateways and devices, sets up device connections, and debugs the communication between the devices and the cloud.

### Edge Developer

Edge Developer, who develops the MQTT client applications of the edge according to the EnOS standard device protocols. The goal of the applications is to collect the telemetry data of the edge and transfer the data through supported protocols to the EnOS Cloud in the supported formats.

### Asset Manager

Assets Manager, who creates and manages the asset hierarchy (asset tree) based on the business case scenarios.

### Application Developer

Application Developer, who develops applications to acquire device telemetry and configuration information via EnOS APIs and SDKs to fulfill requirements of certain business case scenarios.

## Related Services of EnOS Device Management

### Stream Analytics Service

Stream Analytics enables you to define the asset calculation logic, subscribe to the asset data, and perform data calculations based on the predefined calculation logic for your organization. For example, calculating a 5-minute average of the measurement points or a 10-minute average, and etc. [Learn more >>](/docs/data-asset/en/2.0.8/learn/index.html)

## Next Steps

Learn how to quickly provision a typical smart IoT device on EnOS Cloud and start to sending telemetries between the device and the cloud:

- [Getting Started to Connect a Smart Device](quickstart/gettingstarted_device_connection)

Learn how to provision a traditional industry equipment that connects through an edge gateway:

- [Getting Started to Connect a Non-Smart Device](quickstart/gettingstarted_edge_connection)
