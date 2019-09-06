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
     - Response code. `200` indicates that the requested operation is executed successfully.

## Return Code

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 1256 | Remove topo failure, \[details\] | Part or all of the sub-devices failed to be removed as sub-device. To troubleshoot the problem, you must read the details of this return code. [#f1]_ |

.. [#f1] Details of this return code can be categorized into the following:
   - The device you remove sub-devices from is not a gateway device.
   - The sub-device does not exist.
   - The sub-device is online.
   - The device to be removed is not a sub-device of the gateway device.



<!--end-->
