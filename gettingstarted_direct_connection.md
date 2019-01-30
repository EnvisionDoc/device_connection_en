# Getting started with direct device connection (through MQTT)

For devices that support MQTT protocols, you can connect the devices into the EnOS™ Cloud directly.

## Before you start

EnOS™ provides various built-in device models and you can create your assets by instantiating the device models. However, if your device does not have a matching model, you'll need to create a device model. For more information, see [Device modelling overview](device_modelling/model_overview).

## Step 1: Create sites and devices

In EnOS™, the devices and the organization entity where the
devices belong to are managed as *assets*. You'll first need to create your devices and sites in EnOS™.

Use the **Asset Management > Sites and Devices** function to create sites and associate devices into your sites. For more information, see [Creating sites and devices](asset_management/creating_sites_devices).

## Step 2: Organize sites

In practice, devices are typically organized hierarchically.
The hierarchical structure is an *asset tree*. For more information about the asset management mechanism of EnOS™, see [Asset management overview](asset_management/index).

Use the **Asset Management > Asset Tree** function to arrange your sites hierachically according to their organization in the real world. For more information, see [Creating asset tree](asset_management/creating_asset_tree).

## Step 3: Create licenses for the devices to connection

To enable a device to connect to the EnOS™ Cloud through MQTT protocol, you'll need to create a license, which defines properties of the connections allowed, themes to publish/subscribe, and access policies.

Use the **Asset Management > Device Connections** function to create the license and relevant configuration. For more information, see [Creating device connection](asset_management/configuring_mqtt_connection).

## Step 4: Configure the physical devices with connection credentials

The major procedure is as follows:

1. From the **Device Connections**, click the license that you created to show the list of connections under the license.

2. Click the **Edit** icon for a connection and note the **Connection name** and **Connection key**.

3. Configure the physical device with the connection credentials that you noted in 2.

The device can then connect to the MQTT broker by presenting the credentials. The device is authenticates through the credentials and authorized through the definition of the license.


## Step 5: Configure the data transmission format for devices

When a device uploads data through the MQTT protocol, you need to configure the data transceiving format at the device end.
