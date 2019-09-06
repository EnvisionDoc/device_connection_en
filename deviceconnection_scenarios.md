# Use case scenarios

## Scenario 1: Photovoltaic monitoring application

The devices are typically connected to the EnOS™ Cloud through the local edge.

### Business background

An application developer plans to build a cloud-based distributed photovoltaic
monitoring application to achieve centralized real-time monitoring of the operational
state of photovoltaic devices across different sites from the cloud. The application also provides event alarming and data analysis services to achieve effective management and optimization of photovoltaic power generation assets.

The application developer, as a third party operator, cannot change the
inverters to be connected, and therefore needs to adapt to various inverters in the market.

### Business analysis

#### Assumptions

- Two types of devices need to be connected: inverter and smart meter.
The brand and model of inverters and meters vary in different projects.

- No third party systems are involved, the device data needs to be collected and sent directly to the EnOS™ Cloud.

- The total number of devices to be connected to a site does not exceed 20, and
each device collects data from 20 points on average, with an average sampling
cycle of 1 minute.

- The inverters and smart meters support standard Modbus-RTU protocol.

- Wired network is available in the site for connection to the public network.

#### Analysis

Because of the presence of multiple types of third-party devices, and the fact that the communication system can’t be altered, data acquisition needs to be completed with Edge.

### Business construction

1. Device modelling (in EnOS™ Cloud).

   Device modelling is application-facing, which indicates that all projects can reuse the models. The major procedure is as follows:

   - Create the photovoltaic domain.
   - Create the photovoltaic site model.
   - Create the inverter model and electric meter model.

2. Select an appropriate local edge and install the software.  

   The configuration varies based on project conditions. In this case, consider the following facts to choose the edge product:

   - Because each site has limited number of sampling points and limited sampling frequencies, edge hardware with a low configuration is sufficient.
   - As the inverters and smart meters are connected via RS485 bus, a serial port server is required to achieve conversion from serial port to Ethernet port.  

   Purchase an Edge based on the recommended list and install the Edge software.

3. Create device templates (in EnOS™ Cloud).

   Assume that there are 20 inverters of the same brand and the same model in a site, and there is one smart meter. You need to create a device template for the inverter and a device template for the smart meter.

4. Connect devices to EnOS™ (in EnOS™ Cloud).

   The major procedure is as follows:

   - Create devices as cloud assets on EnOS™ (model instantiation).
   - Create edge.
   - Configure the connection from the edge to the cloud and add devices into the connection.

5. On-site wiring.

   The major procedure is as follows:

   - Use a 485 bus to connect all inverters and smart meters serially in a daisy chain, and then connect the bus to a serial port server.
   - Connect the serial port server to the edge device via wired network, and complete relevant configurations.
   - Connect the edge device to the wired network in the site, and ensure that the edge device can connect to the public network.

6. Test communication.

   Publish the cloud configuration to edge and check the communication, ensure that all device data is correctly collected to the cloud.

By now, the device data has been collected and sent to the cloud. The rest steps are data acquisition, processing, and analysis via EnOS™ APIs with the photovoltaic applications.

## Scenario 2: Household energy storage battery monitoring application

The devices are typically connected directly to the cloud via the MQTT protocol.

### Business background

A household energy storage battery vendor plans to build a cloud-based
battery monitoring application to achieve centralized real-time monitoring of operational state of different household energy storage batteries from the cloud. The application also provides alarm and data analysis services.

The vendor has its own energy storage battery products, and is able to develop and reinvent batteries.

### Business analysis

#### Assumptions

- The battery has its own communication system, and the vendor has the R&D
capability to modify the external communication system of the batteries.

- There is no third party system involved, and the battery devices are
connected directly to the EnOS™ Cloud platform.

- There is a local wired network with access to public network connected to the battery communication system.

#### Analysis

As the vendor has the capability and privilege to modify the device, the vendor can develop the self-registration service for the devices to enable devices to achieve data interchange with the EnOS™ IoT Hub through the MQTT protocol.

As the devices are standard, all devices share the same template. Therefore, the mapping can be defined natively in the device configuration file. There is no need to create a device template in the EnOS™ Cloud.

### Business construction

1. Device modelling (in EnOS™ Cloud):

   - Create a home energy storage domain.
   - Create a home site model.
   - Create a battery model.

2. Modify the device, develop the self-registration service and integrate the MQTT Client:

   - Modify the battery device, develop the device self-registration service in PLC (by calling the EnOS™ API via Web Service protocol), and integrate the MQTT Client.
   - Determine MQTT parameters and relevant message structure based on platform MQTT
   access standards.
   - Download the license, device id, and key that the platform issues to the device as firmware. This allows the automatic connection of the device to EnOS™ cloud platform upon power-on.


3. After the battery is powered on and connected to the grid, it automatically connects to the EnOS™ Cloud for device registration and asset creation, and transmit the data, which is already mapped to the model points, to the cloud.

By now, you have completed automatic connection and registration of the battery device. The rest steps are data acquisition, processing, and analysis via EnOS™ API through the energy storage application.
