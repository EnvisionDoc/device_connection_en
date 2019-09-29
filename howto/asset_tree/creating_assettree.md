# Creating an Asset Tree and Node

This article describes how to create an asset tree and bind an asset to the asset tree.

## Before You Start

To create an asset tree, you must have write access to the **Asset Tree** service. If you don't have the access, contact your OU administrator to obtain the access. For more information about user access in EnOS, see [Policy, Roles, and Access](/docs/iam/en/2.0.8/access_policy).

## Creating an Asset Tree

.. note:: You can create a maximum of 10 asset trees within an OU.

1. In the EnOS Console, click **Asset Tree** from the left side navigation panel.

2. Click  **+**  on the upper left corner to create an asset tree.

3. In the **Create Asset Tree** window, provide the following settings:

   - **Name**: Enter the asset tree name.
   - **Select Model**: Select a model for the tree from the drop-down list.
     If the asset tree does not need a model, you can use the built-in model `None`, which has four empty elements.
   - **Timezone/City**: Select the timezone or city for the asset tree.
   - If you selected a model, provide corresponding attribute values ​​according to the specifications defined in the thing model.

   .. image:: ../../media/creating_asset_tree.png

4. Click **Confirm** to complete creation.

## Binding a Device to Asset Tree

After creating the asset tree, you can bind an asset to the tree as a node by the following methods:

- Create an asset based on the model
- Add an existing asset to the asset tree

### Creating an Asset Based on the Model

1. Click **+** next to the asset tree.

   .. image:: ../../media/creating_asset.png

2. In the "New Sub-node" pop-up window, select the way you want to add a sub-note. In this scenario, **Create an Asset and Bind** . Click **Next** .

3. Fill in the basic information for the asset. If the model you selected contains attributes, fill in the attribute values for the asset.

   .. image:: ../../media/creating_asset_basic_information.png

4. Click **Confirm** to finish creating an asset and binding.

### Binding an Existing Asset

1. Click **+** next to the asset tree.

2. In the "New Sub-node" pop-up window, select the way you want to add a sub-note. In this scenario, **Bind to existing asset** . Click **Next** .

3. In the asset list, select the asset to be bound to the tree and click **Confirm**. You may also use the filters to help you out.

   Only those assets that have not been bound to this asset tree are displayed. You can select at most 100 devices at one time.

   .. image:: ../../media/creating_asset_by_importing.png

## Results

You created an asset tree and bound an asset to the tree as a node.

## Related Information

- [Asset Tree Overview](assettree_overview)
- [Managing an Asset Tree](managing_assettree)
