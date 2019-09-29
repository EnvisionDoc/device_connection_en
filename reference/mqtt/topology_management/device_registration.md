# Device Registration

## Register a Device

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/device/register`

- Reply TOPIC: `/sys/{productKey}/{deivceKey}/thing/device/register_reply`

### Example Request Message

```
{
 "id": "123",
 "version": "1.0",
 "params": [
 {
 "productKey": "1234556554",
 "deviceAttributes": {
 "color":"red"
 },
 "deviceKey" : "deviceKey1234",
 "deviceName": "deviceName1234",
 "deviceDesc": "deviceDesc1234"
 }
 ],
 "method": "thing.device.register"
}

```

### Example Response Message

```
{
 "id": "123",
 "code": 200,
 "data": [
 {
 "assetId": "12344",
 "productKey": "1234556554",
 "deviceKey": "deviceKey1234",
 "deviceSecret": "xxxxxx"
 }
 ]
}

```

### Parameter Description

.. list-table::
   :widths: 20 20 20 40

   * - Parameter
     - Type
     - Occurrence
     - Description
   * - Id
     - Long
     - Mandatory
     - Message ID. Reserved parameter for future use.
   * - Version
     - String
     - Mandatory
     - Version of the protocol. Current version is 1.0.
   * - Params
     - List
     - Mandatory
     - Parameters used for dynamic registration.
   * - deviceAttributes
     - String
     - Optional
     - List of the properties of the device.
   * - Color
     - String
     - Optional
     - Property of the device
   * - deviceKey
     - String
     - Optional
     - Device Key of the device.
   * - deviceName
     - String
     - Optional
     - Name of the device.
   * - deviceDesc
     - String
     - Optional
     - Description of the device.
   * - productKey
     - String
     - Mandatory
     - Product key of the device.
   * - assetId
     - String
     - Mandatory
     - Unique identifier of the device.
   * - deviceSecret
     - String
     - Mandatory
     - Device secret of the device.
   * - Method
     - String
     - Mandatory
     - The method of the request.
   * - Code
     - Integer
     - Mandatory
     - Response code. &ldquo;200&rdquo; indicates that the request operation is executed successfully.
   * - Data
     - JSON
     - Optional
     - Detailed information of the device.


<!--end-->
