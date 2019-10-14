# Managing Asset Trees

This article describes the asset tree management operations.

## Before You Start

To perform the management operations, you must have write access to the **Asset Tree** service. If you don't have the access, contact your OU administrator to obtain the access. For more information about user access in EnOS, see [Policy, Roles, and Access](/docs/iam/en/2.0.9/access_policy).

## Deleting a Node

Click the |delete| button next to a node to delete it. If this asset has sub-nodes, you must select **Remove all the sub-nodes under this asset** to delete it.

.. |delete| image:: ../../media/button_delete_asset.png

.. image:: ../../media/deleting_asset.png

.. note:: By deleting a node, you only deletes its association to the asset tree, not the device created in **Device Management > Device Asset** or **Device Management > Logic Asset** .

## Deleting an Asset

Go to **Device Management > Device Asset** or **Device Management > Logic Asset** . Click |delete| to delete the device or logical asset.

.. |delete| image:: ../../media/button_delete.png

.. note:: If you delete an asset before deleting the corresponding node on the asset tree, the node becomes invalid and you cannot attach sub-node to it.

## Deleting an Asset Tree

Select **Delete** under **Delete Asset Tree**.

Make sure no sub-nodes exist under the asset tree before deleting the asset tree.

.. image:: ../../media/deleting_asset_tree.png

## Moving a Node

You can move any node on an asset tree except the tree itself. Click and drag the |move| button to move it. If your destination is deeper than Layer 7 of a tree (the root node being layer 1), the move operation fails. By moving a node, all assets bound to it as sub-nodes will also be moved to the new location.

.. |move| image:: ../../media/button_move_asset.png

<!--end-->



