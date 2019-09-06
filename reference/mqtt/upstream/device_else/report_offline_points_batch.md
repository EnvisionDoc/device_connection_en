# Reporting Offline Measurement Points in Batch

Offline measurement points are reported in batch in the following scenarios:

- A gateway device reports the measurement point data of its sub-devices.
- A device that is directly connected to EnOS reports data of different timestamps.
- A mixture of the previous two scenarios.

.. note:: Configure the following parameters according to the model definition. If part of the data failed to be reported, the entire request fails. The first return code that occurs will be returned.

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/measurepoint/resume/batch`
- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/measurepoint/resume/batch_reply`

## Sample Request

``` json
{
    "id":"123",
    "version":"1.0",
    "allowOfflineSubDevice": true,
    "skipInvalidMeasurepoints": true,
    "params":[
        {
            "productKey":"product1",
            "deviceKey":"device1",
            "measurepoints":{
                "Power":{
                    "value":1,
                    "quality":9
                },
                "temp":1.02,
                "branchCurr":[
                    "1.02",
                    "2.02",
                    "7.93"
                ]
            },
            "time":123456
        },
        {
            "productKey":"product1",
            "deviceKey":"device1",
            "measurepoints":{
                "Power":{
                    "value":2,
                    "quality":9
                },
                "temp":2.02,
                "branchCurr":[
                    "2.02",
                    "3.02",
                    "9.93"
                ]
            },
            "time":123567
        },
        {
            "productKey":"product2",
            "deviceKey":"device2",
            "measurepoints":{
                "Power":{
                    "value":1,
                    "quality":9
                },
                "temp":1.02,
                "branchCurr":[
                    "1.02",
                    "2.02",
                    "7.93"
                ]
            },
            "time":123456
        }
    ],
    "method":"thing.measurepoint.resume.batch"
}
```

## Sample Reply

``` json
{
    "id":"123",
    "code":200,
    "data":{

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
   * - version
     - String
     - Required
     - Version of the protocol. Current version is 1.0.
   * - allowOfflineSubDevice
     - boolean
     - Optional
     - Whether to allow sending measurement point data for offline devices. False by default. If sending data of offline device is not allowed and the request contains an offline device, the entire request is declined. 
   * - skipInvalidMeasurepoints
     - boolean
     - Optional
     - Whether to neglect invalid measurement point data in a request. False by default. If the value is set to false and the request contains invalid measurement point data, the entire request is declined.
   * - params
     - Object
     - Required
     - Parameters used for reporting device measurement points.
   * - productKey
     - String
     - Optional
     - Product key of a sub-device. If the data of a sub-device needs reporting, the product key and device key of the sub-device are required. If the product key and device key of the sub-device is not provided, those of the gateway device is used instead.
   * - deviceKey
     - String
     - Optional
     - Device key of a sub-device. If the data of a sub-device needs reporting, the product key and device key of the sub-device are required. If the product key and device key of the sub-device is not provided, those of the gateway device is used
   * - method
     - String
     - Required
     - Request method
   * - measurepoints
     - Object
     - Required
     - The identifier list of the measurement points to be reported.
   * - power
     - String
     - Optional
     - The identifier of the measurement point that you want to report, in this example, the event with the identifier of power.The value you set must match the data type defined for this parameter. For example, when the quality indicator is selected, the data here must be value and quality.
   * - value
     - Integer
     - Optional
     - The parameter name of the quality indicator of this measurement point, in this example, the parameter named value. The value you set must match the data type defined for this parameter. For example, when the data type of this parameter is set to integer in the model, the value here must be an integer.
   * - quality
     - Integer
     - Optional
     - The parameter name of the quality indicator of this measurement point, in this example, the parameter named quality. The value you set must match the data type defined for this parameter.
   * - temp
     - String
     - Optional
     - The identifier of the measurement point that you want to report. The value you set must match the data type defined for this parameter.
   * - branchCurr
     - String
     - Optional
     - The identifier of the measurement point that you want to report. The value you set must match the data type defined for this parameter.
   * - time
     - Timestamp
     - Optional
     - The timestamp for this measurement point. When not specified, the value is the server time.
   * - code
     - Integer
     - Required
     - Response code. `200` indicates that the request operation is executed successfully.
   * - data
     - JSON
     - Optional
     - Detailed information returned in JSON.

## Return Code

| Return Code | Error Message | Explanation |
|---------|---------|---------|
| 1204 | Model validate failed | The measurement point contains invalid format as defined by the model |
| 1206 | Measurepoint post data format error | Invalid JSON format |
| 1212 | No sub-device permission | Sub-device is offline or is not connected to EnOS through pre-configured gateway device |

<!--end-->
