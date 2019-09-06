# Reporting Offline Measurepoint Information

Configure the following parameters based on the input and output parameters in the model. If some data fails to be sent in the request, the whole request will fail and the first error code that occurs will be returned.

Upstream
- Request TOPIC: `sys/${productKey}/integration/measurepoint/post`

- Response TOPIC: `sys/${productKey}/integration/measurepoint/post_reply`

## Request Format

```JSON
{
    "id":"123",
    "version":"1.0",
    "params":[
        {
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
    "method":"integration.measurepoint.post"
}
```

## Response Format

```json
{
    "id": "123",
    "code": 200,
    "data": {}
}
```

## Parameter descriptions

.. list-table::
   :widths: auto

   * - Parameters
     - Type
     - Necessary or not
     - Description
   * - id
     - Long
     - Optional
     - Message ID (retention value)
   * - version
     - String
     - Required
     - Protocol version, which is V1.0 now
   * - params
     - Object
     - Required
     - Parameters required for reporting measure points
   * - deviceKey
     - String
     - Optional
     - device key 
   * - measurepoints
     - Object
     - Required
     - List of the identifiers for the measurepoints to be reported.
   * - power
     - String
     - Optional
     - Identifier for the measurepoint to be reported, in this example, the **power** attribute. The format here must match the data type in the model. For example, when the data type of this parameter is set as struct, the format here must be consistent with that in the model, i.e. **value** and **quality** in this example.
   * - value
     - Integer
     - Optional
     - Name of the member of this measure point. In this example, the **value** key. The format here must match the data type in the model.
   * - quality
     - Integer
     - Optional
     - Name of the member of this measure point. In this example, the **quality** key. The format here must match the data type in the model.
   * - temp
     - String
     - Optional
     - Identifier for the measurepoints to be reported, the **temp** parameter in this example. The format here must match match the data type in the model.
   * - branchCurr
     - String
     - Optional
     - Identifier for the measure point to be reported, the **branchCurr** parameter in this example. The format here must match match the data type in the model.
   * - time
     - Timestamp
     - Optional
     - Timestamp of the measurepoint. If not specified, the time stamp will be set as the server time.
   * - method
     - String
     - Required
     - Request method
   * - code
     - Integer
     - Required
     - Return code. 200 indicates success.
   * - data
     - JSON
     - Optional
     - Returned detailed information. JSON format

## Return Code

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 1204 | Model validate failed | Invalid data format against model definition |
| 1257 | Device not found | Device does not exist |

<!--end-->
