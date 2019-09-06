# Historical Event Integration

In case of non-passthrough mode, the 3rd-party system needs generate data as per the standard format (currently JSON) defined in EnOS Cloud and then report these data.

Uplink
- Request TOPIC: `sys/${productKey}/integration/event/post`

- Response TOPIC: `sys/${productKey}/integration/event/post_reply`

## Request Format
```json
{
    "id":"123",
    "version":"1.0",
    "params":[
        {
            "deviceKey":"device1",
            "events":{
                "eventId1":{
                    "alarm":9,
                    "level":3
                }
            },
            "time":123456
        },
        {
            "deviceKey":"device2",
            "events":{
                "eventId2":{
                    "result":"ok"
                }
            },
            "time":123456
        }
    ],
    "method":"integration.event.post"
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

## Parameter Description

.. list-table::
   :widths: auto

   * - Parameters
     - Type
     - Necessary or not
     - Description
   * - id
     - Long
     - Optional
     - Message ID (reserved value)
   * - version
     - String
     - Required
     - Protocol version, which is V1.0 now
   * - params
     - Object
     - Required
     - Parameters for the reported events
   * - deviceKey
     - String
     - Required
     - device key
   * - events
     - Object
     - Required
     - List of the identifiers for the events to be reported.
   * - eventId
     - String
     - Required
     - Identifier for the event to be reported. In this example, the **eventId1** and **eventId2** parameters. The format here must match the data type of the model. For example, when the data type of this parameter is set as struct, the formats here must be consistent with those in the model, i.e. **alarm** and **level** in this example.
   * - alarm
     - Integer
     - Optional
     - Identifier for the member of the event to be reported, in this example the **alarm** parameter. Its format must match the data type in the model. For example, when the data type of this parameter is set as int in the model, its value formats must be consistent with, i.e. **9** in this example.
   * - level
     - Integer
     - Optional
     - Identifier for the member of the event to be reported, in this example, the **level** parameter. Its format must match the data type in the model. For example, when the data type of this parameter is set as int, its value formats must be consistent with that in the model, i.e. **3** in this case.
   * - result
     - String
     - Optional
     - Identifier for the member of the event to be reported, in this example, the **result** parameter. Its format must match the data type in the model. For example, when the data type of this parameter is set as string, its formats must be consistent with that in the model, i.e. **OK** in this example.
   * - time
     - String
     - Optional
     - Time stamp of event. If not specified, the timestamp will be set as the server time.
   * - method
     - String
     - Optional
     - Request method
   * - code
     - Integer
     - Required
     - Return code. 200 indicates success.
   * - data
     - String
     - Optional
     - Detailed information returned. JSON format


## Return Code

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 1204 | Model validate failed | Invalid data format against model definition |
| 1257 | Device not found | Device does not exist |


<!--end-->
