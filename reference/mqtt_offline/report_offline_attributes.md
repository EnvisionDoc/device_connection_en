# Reporting Attribute Information

The 3rd-party system reports the historical attribute data. EnOS updates the corresponding attribute information as per the reported data.

Upstream
- Request TOPIC：`sys/${productKey}/integration/attributes/post`

- Response OPIC：`sys/${productKey}/integration/attributes/post_reply`

## Request Format

```JSON
{
    "id":"123",
    "version":"1.0",
    "params":[
        {
            "deviceKey":"device1",
            "attributes":{
                "attr1":{
                    "key1":"val1",
                    "key2":"val2",
                }
            }
        },
        {
            "deviceKey":"device2",
            "attributes":{
                "attr2":2.3
            }
        }
    ],
    "method":"integration.attribute.post"
}
```

## Response Format

```JSON
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
     - Message ID (reserved value)
   * - version
     - String
     - Required
     - Protocol version, which is V1.0 now
   * - params
     - Object
     - Required
     - Parameters required for reporting attributes
   * - attributes
     - Array
     - Optional
     - List of the identifiers for the attributes to be reported. At most 200 attributes are allowed to be reported at a time. If this parameter is not specified, no attribute will be reported. 
   * - attr1
     - struct
     - Required
     - Identifiers for the attributes to be reported, i.e. the **attr1** attribute in this case. The format here must match the data type defined in the model. For example, when the data type of this parameter is set as struct, the formats here must be consistent with those in the model, i.e. **key1** and **key1** in this case.
   * - key1
     - String
     - Required
     - Parameter name in this attribute.
   * - key2
     - String
     - Required
     - Parameter name in this attribute.
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
