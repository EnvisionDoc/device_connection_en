# Report Device Measurement Points

A device can publish a message to this topic to report the newly added measure point to the cloud.

.. note:: Configure the parameters according to the output and input parameters of the measure points.

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/measurepoint/post`

- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/measurepoint/post_reply`

## Example Request Message

```
{
	"id": "123",
	"version": "1.0",
	"params": {
		"measurepoints": {
			"Power": {
				"value": 1.0,
				"quality": 9
			},
			"temp": 1.02,
			"branchCurr": [
				"1.02", "2.02", "7.93"
			]
		},
		"time": 123456
	},
	"method": "thing.measurepoint.post"
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

   * - Parameters
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
     - Object
     - Mandatory
     - Parameters used for reporting device measure points.
   * - method
     - String
     - Mandatory
     - The method of the device.
   * - measurepoints
     - Object
     - Mandatory
     - List of the measure point-type of features of the device.
   * - power
     - --
     - Optional
     - The identifier of the measure point that you want to report, in this example, the event with the identifier of **power**.The value you set must match the data type defined for this parameter. For example, when the quality indicator is selected, the data here must be **value** and **quality**.
   * - value
     - Integer
     - Optional
     - The parameter name of the quality indicator of this measure point, in this example, the parameter named **value**. The value you set must match the data type defined for this parameter. For example, when the data type of this parameter is set to integer in the model, the value here must be an integer.
   * - quality
     - Integer
     - Optional
     - The parameter name of the quality indicator of this measure point, in this example, the **quality** parameter that indicates the data quality. The valid value is integer in the range 0 - 9.
   * - temp
     - Integer
     - Optional
     - The identifier of the measure point that you want to report, in this example, the measure point with the identifier of **temp**. Similar to above, the value you set must match the data type defined for this parameter.
   * - branchCurr
     - Array
     - Optional
     - The identifier of the measure point that you want to report, in this example, the measure point with the identifier of **branchCurr**. Similar to above, the value you set must match the data type defined for this parameter.
   * - time
     - Timestamp
     - Optional
     - The timestamp for this request topic. When not specified, the value is the server time.
   * - code
     - Integer
     - Mandatory
     - Response code. &ldquo;200&rdquo; indicates that the request operation is executed successfully.
   * - data
     - JSON
     - Optional
     - Detailed returned information in JSON format.

## Return Code

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 1204 | Model validate failed | The measurement point contains invalid format as defined by the model |

<!--end-->
