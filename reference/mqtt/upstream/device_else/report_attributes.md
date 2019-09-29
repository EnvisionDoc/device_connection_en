# Report Attributes

A device can publish a message to this topic to report the newly added attributes to the cloud.

Upstream

- Request TOPIC：`/sys/{productKey}/{deviceKey}/thing/attribute/update`

- Reply TOPIC：`/sys/{productKey}/{deviceKey}/thing/attribute/update_reply`

## Example Request Message

```
{
 "id": "123",
 "version": "1.0",
 "params": {
 "attributes": {
 "attr1": {
     "value": 1.0,
     "value2": 9
   },
 "attr2": 1.02,
 "attr3": [1.02, 2.02, 7.93]
 }
 },
 "method": "thing.attribute.update"
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
     - List
     - Mandatory
     - Parameters used for reporting attributes.
   * - attribute
     - Array
     - Optional
     - List of the attributes-type of features of the device. A request can carry a maximum of 200 attributes.
       When not specified, no attribute will be reported. 
   * - method
     - String
     - Mandatory
     - The method of the request.
   * - attr1
     - Struct
     - Mandatory
     - The identifier of the attribute that you want to report, in this example, the attribute with the identifier of **attr1**. The format here must match the data type defined for this parameter. For example, when the data type of this parameter is struct. the data here must be match the defined struct which is **value** and **value2**.
   * - value
     - Integer
     - Mandatory
     - The parameter name of the struct data of this attribute, in this example, the parameter named **value**. The value you set must match the data type defined for this parameter. For example, when the data type of this parameter is set to integer in the thing model, the value here must be an integer.
   * - code
     - Integer
     - Mandatory
     - Response code. &ldquo;200&rdquo; indicates that the requested operation is executed successfully.

<!--end-->
