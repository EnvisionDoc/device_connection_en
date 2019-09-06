# Connecting Sub-devices to EnOS Cloud in Batch

## Before You Start

1. Create instances on EnOS Cloud for the sub-devices.

2. Add the instances as sub-devices to a gateway device instance. 

## Topics

UpStream:

- Request TOPIC: `/ext/session/{productKey}/{deviceKey}/combine/login/batch`
- Reply TOPIC: `/ext/session/{productKey}/{deviceKey}/combine/login/batch_reply`

## Example Request Message

``` json
{
    "method":"combine.login.batch",
    "id":"1",
    "params":{
        "clientId":"Lx5Q1X6M",
        "subDevices":[
            {
                "sign":"c14fc21231e6c44849683ccfb7a2089895a278b37a30c33ccb58d3b8690d16e1",
                "deviceKey":"xGKFwfkEXz",
                "productKey":"x4jwTsoz"
            },
            {
                "sign":"1fbf0cd0903a9405d567fe5909a1aae75012df5465211db367c7666c0ae33bf2",
                "deviceKey":"cS9MdVJwO9",
                "productKey":"x4jwTsoz"
            },
            {
                "sign":"480869163c85777c9fcb9fe53340fb13d38ef19e644df82927fcd2393b3819c4",
                "deviceKey":"viWaBURIDm",
                "productKey":"x4jwTsoz"
            }
        ],
        "signMethod":"sha256",
        "timestamp":"1564988853275"
    },
    "version":"1.1"
}
```

## Example Reply Message 

##### a) All sub-devices logged in successfully 
``` json
{
    "id":"1",
    "code":200,
    "data":{
        "loginedSubDevices":[
            {
                "assetId":"LhBLfxpG",
                "deviceKey":"cS9MdVJwO9",
                "productKey":"x4jwTsoz"
            },
            {
                "assetId":"kAHwVbyl",
                "deviceKey":"viWaBURIDm",
                "productKey":"x4jwTsoz"
            },
            {
                "assetId":"AkHJvoqN",
                "deviceKey":"xGKFwfkEXz",
                "productKey":"x4jwTsoz"
            }
        ],
        "failedSubDevices":[]
    },
    "message":""
}
```

##### b) Some of the sub-devices failed to log in
``` json
{
    "id":"1",
    "code":723,
    "data":{
        "loginedSubDevices":[
            {
                "assetId":"LhBLfxpG",
                "deviceKey":"cS9MdVJwO9",
                "productKey":"x4jwTsoz"
            },
            {
                "assetId":"kAHwVbyl",
                "deviceKey":"viWaBURIDm",
                "productKey":"x4jwTsoz"
            }
        ],
        "failedSubDevices":[
            {
                "deviceKey":"xGKFwfkEXz",
                "productKey":"x4jwTsoz"
            }
        ]
    },
    "message":"x4jwTsoz&xGKFwfkEXz has error: Device is disable;"
}
```

##### c) Invalid request format
``` json
{
    "id":"1",
    "code":1220,
    "data":{
        "loginedSubDevices":[],
        "failedSubDevices":[]
    },
    "message":"payload format error, ..."
}
```

## Dealing with Reply

When the return code `code` is `723`, `loggedinSubDevices` indicates the sub-devices that successfully logged in and `failedSubDevices` indicates those that failed to log in.

When `code` is `1220` or there was an internal server error, `loggedinSubDevices` and `failedSubDevices` remain blank. 

The following is a sample logic on how to deal with a reply.

``` java
if (code == 200) {
  // All sub-devices logged in successfully.
} else {
  if (loginedSubDevices.isEmpty() && failedSubDevices.isEmpty()) {
    // Invalid format or internal server error
  } else {
    if (loginedSubDevices.isNotEmpty()) {
      // Deal with sub-devices that logged in
    }
    if (failedSubDevices.isNotEmpty()) {
      // Deal with sub-devices that failed to log in
    }
  }
}
```

## Parameter Description

.. list-table::
   :widths: auto

   * - Parameter
     - Type
     - Required or Optional
     - Description
   * - id
     - Long
     - Optional
     - Message ID. Reserved parameter for future use.
   * - params
     - List
     - Required
     - Parameters used for connecting sub-device to EnOS Cloud.
   * - deviceKey
     - String
     - Required
     - Device key of the sub-device.
   * - productKey
     - String
     - Required
     - Product key of the sub-device.
   * - sign
     - String
     - Required
     - Signature of the sub-device. Sub-devices use the same signature rules as the gateway device.
   * - signmethod
     - String
     - Required
     - Signing method. The supported methods are **hmacSha1**.
   * - timestamp
     - String
     - Required
     - Timestamp
   * - clientId
     - String
     - Required
     - The identifier of the device. The value can be `productKey` or `deviceName`.
   * - message
     - String
     - Required
     - Response message
   * - code
     - Integer
     - Required
     - Response code. `200` indicates that the request operation is executed successfully.
   * - data
     - JSON
     - Required
     - Detailed returned information in JSON format.
   * - loginedSubDevices
     - Array
     - Required
     - Devices that successfully logged in.
   * - failedSubDevices
     - Array
     - Required
     - Devices that failed to log in.

.. note:: A gateway device can have a maximum of 200 online sub-devices at the same time. Subsequent new sub-devices will be declined to go online.

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
