# Delete Topological Relationships of Sub-devices

An edge can publish a message to this topic to delete the topological relationship between the edge and a sub-device.

After you delete the topological relationship of the sub-device, the sub-device can no longer connect to the EnOS Cloud through the edge.

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/topo/delete`

- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/topo/delete_reply`

.. note:: The *productKey* and *deviceKey* in the TOPIC are the credentials of the edge.

## Example Request Message

```
"id": "123",
 "version": "1.0",
 "params": [
 {
 "deviceKey": "deviceKey1234",
 "productKey": "1234556554"
 }
 ],
 "method": "thing.topo.delete"
}

```

## Example Response Message

```
{
 "id": "123",
 "code": 200,
 "data": {}
}

```

## Parameter Description

.. list-table::
   :widths: 20 20 20 40

   * - Parameter
     - Type
     - Occurrence
     - Description
   * - id
     - Long
     - Optional
     - Message ID. Reserved parameter for future use.
   * - version
     - String
     - Mandatory
     - Version of the protocol. Current version is 1.0.
   * - params
     - List
     - Mandatory
     - Parameters used for deleting topological relationships.
   * - deviceKey
     - String
     - Mandatory
     - Device Key of the sub-device.
   * - productKey
     - String
     - Mandatory
     - Product key of the sub-device.
   * - method
     - String
     - Mandatory
     - Signing method.
   * - code
     - Integer
     - Mandatory
     - Response code. &ldquo;200&rdquo; indicates that the requested operation is executed successfully.

<!--end-->
