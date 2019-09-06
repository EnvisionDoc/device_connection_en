# Get Attributes

A device can publish a message to this topic to retrieve attributes from the cloud.

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/attribute/query`

- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/attribute/query_reply`

## Example Request Message

```
{
 "id": "123",
 "version": "1.0",
 "params": {
   "attributes": ["attr1", "attr2", "attr3"]
 },
 "method": "thing.attribute.query"
}

```

## Example Response Message

```
{
 "id": "123",
 "code": 200,
 "data": {
   "attr1": {
       "value": 1.0,
       "value2": "9"
     },
   "attr2": 1.02,
   "attr3": [1.02, 2.02, 7.93]
 }
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
     - Parameters used for getting attributes.
   * - attributes
     - Array
     - Mandatory
     - List of the identifier of the attribute-type of features. A request can carry maximum 200 items.
       When not specified, system will retrieve all the attributes. 
   * - attr1
     - String
     - Mandatory
     - The unique identifier of the attribute.
   * - method
     - String
     - Mandatory
     - The method of the request.
   * - value
     - Struct
     - Mandatory
     - The detailed returned information of this attributes. In this example, the data type of this attribute is struct. Therefore, the parameter **value** is returned with its value.
   * - code
     - Integer
     - Mandatory
     - Response code. `200` indicates that the request operation is executed successfully.

## Return Code

| Return Code | Error Message | Explanation|
|---------|---------|---------|
| 1208 | Attribute query data format error | Invalid request format |


<!--end-->
