# Report Device Events​ (Non-Passthrough)


A device can publish a message to this topic to report the newly added event to the cloud.

If non-passthrough mode is used, the device data sent to the cloud is in JSON.

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/event/{tsl.event.identifier}/post`

- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/event/{tsl.event.identifier}/post_reply`

.. note:: `tsl.service.identifier` is the identifier of the event that has been defined in the thing model.

## Example Request Message

```
{
  "id": "123",
  "version": "1.0",
 "params": {
	"events": {
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
  "method": "thing.event.{tsl.event.identifier}.post"
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
     - Object
     - Mandatory
     - Parameters used for reporting device events.
   * - events
     - Object
     - Mandatory
     - List of the event-type of features of the device.
   * - power
     - String
     - Optional
     - The identifier of the event that you want to report, in this example, the event with the identifier of **power**.
   * - value
     - Integer
     - Optional
     - The name of the output parameter of this event, in this example, the parameter named **value**. The value you set must match the data type defined for this parameter. For example, when the data type of this parameter is set to integer in the thing model, the value here must be an integer.
   * - quality
     - Integer
     - Optional
     - The name of the output parameter of this event, in this example, the **quality** parameter that indicates the data quality. The valid value is integer in the range 0 - 9.
   * - temp
     - Integer
     - Optional
     - The identifier of the event that you want to report, in this example, the event with the identifier of **temp**. Similar to above, the value you set must match the data type defined for this parameter.
   * - branchCurr
     - Array
     - Optional
     - The identifier of the event that you want to report,  in this example, the event with the identifier of **branchCurr**. Similar to above, the value you set must match the data type defined for this parameter.
   * - time
     - String
     - Optional
     - The timestamp of this request. When not specified, the value is the server time.
   * - method
     - String
     - Optional
     - The method of the request.
   * - code
     - Integer
     - Mandatory
     - Response code. &ldquo;200&rdquo; indicates that the requested operation is executed successfully.
   * - data
     - JSON
     - Optional
     - Detailed returned information in JSON format.

## Return Code

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 1204 | Model validate failed | The event contains invalid format as defined by the model |

<!--end-->
