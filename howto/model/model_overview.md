# Device Modeling

The thing model is the abstraction and digitization of the physical objects in the physical world. A thing model is the summary of the features of the objects that are connected to the EnOS Cloud, including attributes, measure points, services and events of the device.

In EnOS, defining thing model means defining the features of the product. The thing model describes what the product is, what it can do, and what services it can provide. On the EnOS platform, the description language of the thing model is in JSON format, and you can assemble and report the device data according to the syntax of the thing model.

## Thing Model Elements
You can define the following elements to define a thing model according to the actual needs of the product.

.. list-table::
   :widths: 20 40 40

   * - Model elements
     - Description
     - Instance
   * - Attribute
     - Describes the static attribute of the device. You can define the name and identifier of the static attribute. The attribute name is a descriptive string that allows Chinese input.
     - Name, model, location, design parameters, longitude, etc.
   * - Measure point
     - Describes the runtime state of the device. You can define the name of the measure points as well as its identifier. The measure point name is a descriptive string that allows Chinese input.
     - Temperature, pressure, current, voltage, various states, etc.
   * - Service
     - A capability or method that can be called to achieve remote operation or control. You can define its input and output parameters. Compared with an attribute, you can achieve more complex business logic through a command.
     - Command to issue, job to perform, etc.
   * - Event
     - The event that can occur when the device is running. An event generally contains notification information that needs to be externally perceived and processed, and may include multiple output parameters.
     - State changes, information about the completion of a job, or the temperature of a device when a failure or alert occurs, etc. 

<!--The end -->

## Data Type

The attributes, measurement points, services, and events are described by data, which is categorized into different types. The following types of data can be used to describe a model.

- int32, 32-bit signed integers
- float
- double
- enum
- string, 1 to 1024 bytes in length
- timestamp, in UTC with millisecond timing accuracy
- date
- struct, whose members can be int32, float, double, enum, bool, string, date
- array, whose members can be int32, float, double, string. You must declare the types of the members when using array. The length of an array can be indefinite, with a maximum of 128 members allowed.
- file

You can use the built-in units for the data when the type is not enum or struct, such as kilometer, decimeter, and percentage.

## Model Relationship

A model can be created from a source model through: **Clone** and **Inherit**. The difference of the two creating modes are mainly reflected in the model relationship.

### Clone

For models created from the **Clone** mode, the new model has exactly the same four elements as the source model. The two models are independent from each other, and changes to one model will not affect the other.

### Inherit

For models created from the **Inherit** mode, we define the newly created models as the **Sub Model** and the source model is the **Parent Model**. The sub model has the following main characteristics:

- The sub model inherits all the features of the parent model, and the inherited elements cannot be modified.
- The sub model can be inherited again, and supports multi-level inheritance relationships.
- The sub model can have independent elements, but the newly added elements in the sub model shall not have the same name as the elements of all parent models.
- When the four elements of the parent model are changed, the sub model's four elements inherited from the parent model are changed synchronously to remain consistent with the parent model.

## Model Permission

EnOS provides **Public Model** and **Private Model**, each having their own access policy.

### Public Model

The public models are the standard models applicable to different industries that are accessible to all organization units (OUs) on EnOS. All OUs on EnOS have read access and no OU has write access to public models.

### Private Model

A private model is created by an OU. Private models are accessible only within the the creating OU. All users of this OU have read access, and only authorized users of this OU have write access to private models.

## Related Information

- [Creating a Model](creating_model)
