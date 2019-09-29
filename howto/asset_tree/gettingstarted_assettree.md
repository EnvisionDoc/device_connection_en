# Asset Tree Management Quick Start

This article describes how to create an asset tree and bind assets (such as devices) to the node of an asset tree through a sample scenario.

## Before You Start

To create an asset tree, you must have write access to the **Asset Tree** service. If you don't have the access, contact your OU administrator to obtain the access. For more information about user access in EnOS, see [Policy, Roles, and Access](/docs/iam/en/2.0.8/access_policy).

## Sample Scenario

A smart building project needs to collect `PM2.5`, `temperature`, `humidity` and `noise` data of the building. Each data has a corresponding sensor and these sensors are connected to a gateway. The gateway is responsible for collecting and forwarding data to EnOS Cloud.

Each building has different types of sensors. Some buildings have all of the four types of sensors, and some buildings might have only some of them.

In this scenario, we have two buildings, each building has the following sensors:

- **1#Building**
  - PM2.5
  - Temperature
  - Humidity
  - Noise

- **2#Building**
  - PM2.5
  - Temperature
  - Noise

## About the Task

According to the above scenarios, you need to create models for the types of assets listed in the sample and build an asset tree based on the asset hierarchy.

1. Create an asset tree called "Sample Company"

2. Create logical assets for two buildings, **Building 1** and **Building 2**

3. Create a model for each type of asset, you need to create the following six models:

   - The model that describes features of the devices:

     3a. Model for PM2.5 sensor

        - Measuring point: PM2.5

     3b. Model for temperature sensor

        - Measuring point: temperature

     3c. Model for humidity sensor

        - Measuring point: humidity

     3d. Model for noise sensor

        - Measuring point: noise

     3e. Model for gateway device

        - Attribute: version
        - Measuring point: cpuRates

      After creating the models, you can create the products based on the models.

3. Create the asset tree based on the asset hierarchy of this scenario.

   2a. Create the asset tree for the organization.

   2b. Create a logical asset **Building 1** under the asset tree.

   2c. Create a device for each sensor under **Building 2**.

   2d. Repeat b and c for **Building 2**.

   If the gateway is treated as a device, you can attached the gateway device under the **Building 1** and **Building 2** as shown in the following figure.

   The asset tree for this scenario is as follows:

   .. image:: ../../media/building_tree.png

## Procedure

### Step 1: Creating a Model

See [Creating a Model](../model/creating_model) to create models for each type of asset involved in the scenario.

### Step 2: Creating an Asset Tree

See [Creating an Asset Tree](creating_assettree) to create asset tree and nodes for the assets involved in the scenario.

## Related Information

- [Asset Tree Overview](assettree_overview)
- [Creating an Asset Tree](creating_assettree)
- [Managing an Asset Tree](managing_assettree)

<!--end-->