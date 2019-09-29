# Delete Attributes

A device can publish a message to this topic to delete the attributes from the cloud.

Upstream

- Request TOPIC: `/sys/{productKey}/{deviceKey}/thing/attribute/delete`

- Reply TOPIC: `/sys/{productKey}/{deviceKey}/thing/attribute/delete_reply`

## Example Request Message

```
{
 "id": "123",
 "version": "1.0",
 "params": {
   "attributes": ["attr1", "attr2", "attr3"]
 },
 "method": "thing.attribute.delete"
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
     - Parameters used for deleting attributes.
   * - attributes
     - Array
     - Optional
     - List of the identifier of the attribute-type of features. A request can carry a maximum of 200 attributes.
       When not specified, no attribute is deleted.
   * - method
     - String
     - Mandatory
     - The method of the request.
   * - code
     - Integer
     - Mandatory
     - Response code. &ldquo;200&rdquo; indicates that the requested operation is executed successfully.

<!--end-->
