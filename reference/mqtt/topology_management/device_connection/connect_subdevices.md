# Connect Sub-devices to EnOS Cloud

Upstream

- Request TOPIC: `/ext/session/{productKey}/{deviceKey}/combine/login`

- Reply TOPIC: `/ext/session/{productKey}/{deviceKey}/combine/login_reply`

## Example Request Message

```
{
 "id": "123",
 "params": {
 "productKey": "123",
 "deviceKey": "test",
 "clientId": "123",
 "timestamp": "123",
 "signMethod": "hmacmd5",
 "sign": "xxxxxx",
 "cleanSession": "true"
 }
 "method":"combine.login"
}

```

## Example Response Message

```
{
 "id":"123",
 "code":200,
 "message":""
 "data": {
  }
}

```

All parameters reported to EnOS Cloud will be signed except **sign** and **signmethod**. The parameter names and values are sorted in alphabetical order and concatenated without concatenation symbols. The concatenated string is then signed by using the algorithm specified by **signmethod**. Taking the request message above for example, sign=uppercase(hmacsha1( cleanSessiontrueclientId123deviceKeytestproductKey123timestamp123{deviceSecret})).

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
   * - params
     - List
     - Mandatory
     - Parameters used for connecting sub-device to EnOS Cloud.
   * - deviceKey
     - String
     - Mandatory
     - Device key of the sub-device.
   * - productKey
     - String
     - Mandatory
     - Product key of the sub-device
   * - sign
     - String
     - Mandatory
     - Signature of the sub-device. Sub-devices use the same signature rules as the edge.
   * - signmethod
     - String
     - Mandatory
     - Signing method. The supported methods are <em>hmacSha1</em>.
   * - timestamp
     - String
     - Mandatory
     - Timestamp
   * - clientId
     - String
     - Mandatory
     - The identifier of the device client. The value can be productKey or deviceName.
   * - cleanSession
     - String
     - Mandatory
     - Supported value: True. This indicates to clear offline information for all sub-devices, which is information that has not been received by QoS 1.
   * - message
     - String
     - Mandatory
     - Response message
   * - code
     - Integer
     - Mandatory
     - Response code. &ldquo;200&rdquo; indicates that the request operation is executed successfully.
   * - data
     - JSON
     - Optional
     - Detailed returned information in JSON format.


.. note:: A edge can accommodate a maximum of 200 online sub-devices simultaneously. When the maximum number is reached, the edge rejects any connection requests.

## Return Code

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 705 | It failed to query device, not existed this device | The sub-device does not exist |
| 723 | Device is disable | The sub-device is disabled |
| 770 | Dynamic activate is not allowed | Dynamic activation is not allowed for this product |
| 771 | Sub device cannot connect to mqtt broker directly | Sub-devices cannot be directly connected to EnOS Cloud |
| 740 | Sub device not belong the gateway | The device is not a sub-device of this gateway |
| 742 | Sign check failed | Hash signature verification failed |
| 746 | The device must login by ssl | Certificate-based authentication is required for this product |

<!--end-->
