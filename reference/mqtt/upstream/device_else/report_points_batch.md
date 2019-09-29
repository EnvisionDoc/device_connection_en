# Reporting Device Measure Points in Batch

You may use this protocol in the following scenarios:
- You want a gateway device to report the measure points of the gateway's sub-devices.
- You want one device to report measure points with different time stamps in batch at one time.
- A mix of all previous scenarios.



.. note:: Configure the parameters according to the output and input parameters of the measure points. If part of the data in the request fails to be sent, the entire request fails to be sent. The first error code to appear will be returned.

Upstream
- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/measurepoint/post/batch`

- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/measurepoint/post/batch_reply`

## Example Request Message

```json
{
	"id": "123",
	"version": "1.0",
	"params": [{
			"productKey": "product1",
			"deviceKey": "device1",
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
		{
			"productKey": "product1",
			"deviceKey": "device1",
			"measurepoints": {
				"Power": {
					"value": 2.0,
					"quality": 9
				},
				"temp": 2.02,
				"branchCurr": [
					"2.02", "3.02", "9.93"
				]
			},
			"time": 123567
		},
		{
			"productKey": "product2",
			"deviceKey": "device2",
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
		}
	],
	"method": "thing.measurepoint.post.batch"
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
   * - productKey
     - String
     - Optional
     - Product key of the device. If you need to report the data of sub-devices, the product keys and device keys of sub-devices are required. If the product keys and device keys of sub-devices are not provided, the product key and device key in the upstream request topic are used.
   * - deviceKey
     - String
     - Optional
     - Device key of the device. If you need to report the data of sub-devices, the product keys and device keys of sub-devices are required. If the product keys and device keys of sub-devices are not provided, the product key and device key in the upstream request topic are used.
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

<!--end-->


