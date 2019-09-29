# Get Topological Relationships of Sub-devices

An edge can publish a message to this topic to retrieve the topological relationship between the edge and a sub-device.

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/topo/get`

- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/topo/get_reply`

.. note:: The *productKey* and *deviceKey* in the TOPIC are the credentials of the edge.

## Example Request Message

```
{
 "id": "123",
 "version": "1.0",
 "params": {},
 "method": "thing.topo.get"
}

```

## Example Response Message

```
{
 "id": "123",
 "code": 200,
 "data": [
 {
 "deviceKey": "deviceKey1234",
 "productKey": "1234556554"
 }
 ]
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
     - Mandatory
     - Message ID. Reserved parameter for future use.
   * - version
     - String
     - Mandatory
     - Version of the protocol. Current version is 1.0.
   * - params
     - Object
     - Optional
     - Parameters used for getting topological relationships.
   * - method
     - String
     - Mandatory
     - The method of the request.
   * - deviceKey
     - String
     - Mandatory
     - Device key of the sub-device.
   * - productKey
     - String
     - Mandatory
     - Product key of the sub-device.
   * - code
     - Integer
     - Mandatory
     - Response code. &ldquo;200&rdquo; indicates that the requested operation is executed successfully.
   * - data
     - JSON
     - Optional
     - Detailed returned information in JSON format.


<!--end-->
