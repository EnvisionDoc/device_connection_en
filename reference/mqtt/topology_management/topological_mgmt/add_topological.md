# Add Topological Relationships of Sub-devices

An edge can publish a message to this topic to add the topological relationship between the edge and a sub-device.

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/topo/add`

- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/topo/add_reply`

.. note:: The *productKey* and *deviceKey* in the TOPIC are the credentials of the edge device.

## Example Request Message

```
{
 "id": "123",
 "version": "1.0",
 "params": [
 {
 "deviceKey": "deviceKey1234",
 "productKey": "1234556554",
 "sign": "xxxxxx",
 "signmethod": "hmacSha1",
 "timestamp": "1524448722000",
 "clientId": "xxxxxx"
 }
 ],
 "method": "thing.topo.add"
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
     - Parameters used for adding topological relationships.
   * - deviceKey
     - String
     - Mandatory
     - Device key of the sub-device.
   * - productKey
     - String
     - Mandatory
     - Product key of the sub-device.
   * - sign
     - String
     - Mandatory
     - Signature.
   * - signmethod
     - String
     - Mandatory
     - Signing method. The supported methods are <em>hmacSha1</em>.
   * - timestamp
     - String
     - Mandatory
     - Timestamp.
   * - clientId
     - String
     - Mandatory
     - Identifier of the sub-device. The value can be productKey or deviceKey.
   * - code
     - Integer
     - Mandatory
     - Response code. &ldquo;200&rdquo; indicates that the requested operation is executed successfully.

All parameters reported to EnOS Cloud will be signed except **sign** and **signmethod**. The parameter names and values are sorted in alphabetical order and concatenated without concatenation symbols. The concatenated string is then signed by using the algorithm specified by **signmethod**. Taking the request message above for example, `sign=uppercase(hmacsha1( clientId123deviceKeytestproductKey123timestamp1524448722000{deviceSecret}))`.

<!--end-->
