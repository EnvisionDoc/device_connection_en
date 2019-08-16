# Tutorial Overview

## Scenario

Personal computer is one of the best choices for experimenting device connection into EnOS. With an open source plug-in, you can ingest the system and hardware data of your PC and upload the ingested data to EnOS Cloud for further processing and analysis, so that you can monitor the CPU load and memory usage. Besides, you can also send commands from EnOS Cloud to control the data ingesting interval.

The scenario is depicted in the following chart:

.. image:: media/scenario_connect_pc.png

This tutorial walks you through modeling and registering your PC as a device on the EnOS Cloud,  ingesting system and hardware data of the PC, updating the attributes of the PC with the ingested data, uploading the CPU and memory usage data to the cloud, and sending commands from the cloud to control the data ingesting interval. In this tutorial, you will:

- Define a model for computer and register the PC as device on EnOS Console
- Configure TSDB storage policy for the PC system data
- Develop a program with the EnOS Java SDK for MQTT to connect the PC into EnOS
- Run the program to ingest data of the PC and upload the data into EnOS Cloud
- Monitor the CPU load the the PC
- Send commands from EnOS Cloud to control the data ingesting interval
- Develop a stream data processing job for calculating the PC memory usage percentage

### [Start >](registering_device)

## Prerequisites

- You have signed up to EnOS for an individual account or an enterprise account to access the EnOS Console.
- Your account must have been assigned full access to the model service, device connectivity & management service, and data asset management service.

## Units

This tutorial includes the following units:

> [Unit 1. Registering the PC on EnOS Console](registering_device)
>
> 20 minutes

> [Unit 2. Configuring Storage Policy for the Device Data](configuring_storage_policy)
>
> 20 minutes

> [Unit 3. Connecting the PC into EnOS and Ingesting Data](connecting_device)
>
> 30 minutes

> [Unit 4. Monitoring CPU Load](monitoring_cpu_load)
>
> 10 minutes

> [Unit 5. Controlling Data Upload Interval](controlling_upload_interval)
>
> 10 minutes

> [Unit 6. Calculating Memory Usage Percentage](calculating_memory_usage)
>
> 20 minutes
