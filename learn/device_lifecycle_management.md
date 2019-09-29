# Device Lifecycle Management

EnOS Device Management offering allows you to perform end-to-end device lifecycle management from plan and design phase to the decommissioning phase.

The following figure shows the services that the EnOS Device Management offering provides to help you achieve end-to-end lifecycle management:

.. image:: ../media/device_lifecycle_management.png

## Plan & Design Phase

In the plan and design phase, you'll determine how to abstract your device models and manage your asset hierachy. You'll also determine the connection scheme to use based on the type of devices to connect, the device deployment conditions, and your security requirements.

<!--Here, we'll link to a topic where plan & design best practices are talked about-->

### Modelling devices

Application developers, who translate business needs into requirements of data and processing logics, can use device models to describe devices of thousands of models from different manufacturers by unifying them into a small number of common models and thus facilitates application processing. EnOS™ Cloud has accumulated a large set of device models in its model library to facilitate abstraction of your asset data. [Learn more >>](../howto/model/model_overview)


### Managing Asset Tree

Asset owners, who understand the enterprise asset management business, can quickly create the asset topology to manage assets in the cloud in terms of different dimensions. [Learn more >>](../howto/asset_tree/assettree_overview)

### Planning Connection Scheme

The connection scheme is usually selected according to the hardware capabilities of the device and the security requirements for the device connection.

For more information about the connection scenarios, see [Device Connectivity](connection_scenarios).

For more information about the security options and mechanisms, see [Device and Cloud Security](deviceconnection_authentication).

## Provisioning Phase

In the provisioning Phase, based on the selected connection scheme, IoT Engineers
- Register devices to obtain the device identities, and when certificate authentication is required, generate the certificates
- Use device SDKs to perform device-end development so that devices are authenticated to the cloud and start to transmit data

Learn how to quickly provision a typical smart IoT device on EnOS Cloud and start to sending telemetries between the device and the cloud:

- [Getting Started to Connect a Smart Device](../quickstart/gettingstarted_device_connection)

Learn how to provision a traditional industry equipment that connects through an edge gateway:

- [Getting Started to Connect a Non-Smart Device](../quickstart/gettingstarted_edge_connection)

Learn how to adopt x.509 certificate based authentication:

- [Securing Connection Through Certificate-Based Authentication](../quickstart/gettingstarted_java_ssl_connection)

## Servicing Phase

In the servicing phase, you can

- Gain an overview of your device and message dynamics through the device management dashboard that EnOS provides natively in the Console. You can also develop your own application for data presentation.
- Control your devices remotely from the cloud, such as to enable or disable the devices, or trigger services as defined by the device model.
- Use the Asset Alert Service to monitor alerts on your devices as defined by the alert triggering rules against real-time measure point telemetry. For more information, see [Device Alert](../howto/alert/alert_overview).
- Manage, consume, and store the device data according to your business needs. For more information, see [Data Asset Management](/docs/data-asset/en/2.0.8/data_asset_overview.html).

## Maintanance Phase

In the maintanance phase, you can

- Use the OTA Service to manage the device firmware in the cloud and achieve firmware upgrade over-the-air. For more information, see [Firmware Upgrade Over-the-air](../howto/ota/batch_upgrading_firmware).

- Renew the device certificate when a certificate expires.

## Decommissioning Phase

In the decommissioning phase, you can
- Disable and delete the digital twin of a device from the cloud
- Revoke the device certificate that was issued to the device
- Archive the data of the decommissioned devices
