# Device connection overview

EnOS™ supports direct device connections through the MQTT protocol and connections through the EnOS™ Edge.

The following figure shows the methods to connect devices into the EnOS™ cloud.

.. image:: media/device_connection_methods.png

As shown above, EnOS™ supports the following connection methods:

## Method 1: Connect through EnOS™ IoT Hub via MQTT protocol (cloud service)

This method requires the device end to support MQTT protocol and is applicable to most of the new IoT devices.

## Method 2: Connect through on-site EnOS™ Edge

In this case, the EnOS™ Edge is deployed on-site with the devices. This method applies to most traditional devices and systems that do not support the MQTT protocol.

## Method 3: Connect through remote EnOS™ Edge

In this case, the EnOS™ Edge is deployed remotely at the cloud side. This method applies to most traditional devices and systems that do not support the MQTT protocol.

## Method 4: Connect through EnOS™ Cloud Edge clusters (cloud service)

This requires that the device to be connected has a unique ID and can upload data through supported communication protocols. This method is frequently used for the photovoltaic device connection.

## Key components and concepts

Some of the key components and concepts in the figure are described as follows.

### IoT Hub

IoT Hub is a cloud broker service that EnOS™ provides for device connection.

The cloud broker provides the following functions:

- Safe and reliable large-scale bi-directional message transmission from the devices to the cloud.
- Forwarding the data from the client to corresponding subscribers on EnOS™.
- Providing license authorization and creation of thing and policy themes.
- Exposing interface for connections from MQTT clients.

### MQTT

MQTT is a lightweight open-source protocol for Internet of Things based on TCP/IP. MQTT has several key features:

- Support publication and subscription.
- Support data disk persistence.
- The publisher and subscriber use the client ID as a unique token. When multiple clients use the same client ID, the client that initiates the connection after is accepted and the broker disconnect the former connection.

### EnOS™ Edge

Edge is the front end of Envision EnOS™ IoT platform for data acquisition. It’s used to collect on-site device data or connected to a third party system for data acquisition and transmission of data to the EnOS™ Cloud. Edge, as software, supports data acquisition, multiple communication conventions, local caching and breakpoint continuation. It can either deployed in a cloud machine or an on-site hardware of a specified brand model. An edge must have a legal serial number (SN) assigned by Envision to be recognized by EnOS™ Cloud.

.. image:: media/Basic_concepts_Edge_stru.png

### API Gateway

API Gateway is the central access interface of EnOS™. The API Gateway provides the following services:

- Hosting the EnOS™ APIs.
- Hosting user-built APIs to help developers make their services developed with EnOS™ available to partners as APIs.
- Handling API requests, which access relevant resources (including device assets, personnel, applications, and privileges) stored in EnOS™ Cloud to be added, deleted, modified, or inquired by developers.

For more information about the API hosting, see [Getting started with API management](/docs/app-development/en/1.0/api_service/getting_started_api_service.html) in *Application Development*.

## Related information
- [Direct device connection overview](direct_connection_overview)
- [EnOS™ Edge connection overview](edge_connection_overview)
