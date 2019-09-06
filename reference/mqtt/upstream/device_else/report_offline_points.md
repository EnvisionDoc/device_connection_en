# Reporting Offline Measurement Points

.. note:: Configure the input and output parameters according to your model definition.

Upstream
- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/measurepoint/resume`
- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/measurepoint/resume_reply`

## Sample Request

``` json
{
    "id":"123",
    "version":"1.0",
    "params":{
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
    "method":"thing.measurepoint.resume"
}
```

## Sample Reply

``` json
{
    "id":"123",
    "code":200,
    "data":{}
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
   * - params
     - Object
     - Required
     - Parameter required for reporting measurement points.
   * - method
     - String
     - Required
     - Request method.
   * - measurepoints
     - Object
     - Required
     - Identifier list of measurement points to be reported.
   * - power
     - String
     - Optional
     - The identifier of the measurement point that you want to report, in this example, the point with the identifier of **power**.The value you set for the point must match the data type defined for this parameter. For example, when the quality indicator is selected, the value here must be **value** and **quality**.
   * - value
     - Integer
     - Optional
     - Identifier of this measurement point. The value of the point must match the data type.
   * - quality
     - Integer
     - Optional
     - Identifier of this measurement point. The value of the point must match the data type.
   * - temp
     - String
     - Optional
     - Identifier of this measurement point. The value of the point must match the data type.
   * - branchCurr
     - String
     - Optional
     - Identifier of this measurement point. The value of the point must match the data type.
   * - time
     - Timestamp
     - Optional
     - Timestamp of the measurement point. If you don't set a value to it, the system timestamp of EnOS will be used
   * - code
     - Integer
     - Required
     - Return code. 200 indicates success.
   * - data
     - JSON
     - Optional
     - Detailed information returned in JSON.

## Return Code

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 1204 | Model validate failed | The measurement point contains invalid format as defined by the model |


<!--end-->
