# Device modelling overview

## What is a device model?

A device model is the abstract of a specific device. The device model allows devices of thousands of models from different manufacturers to be unified into a small number of common models and thus facilitates application processing.

Taking smart meter as an example, there are many manufacturers and models. But various electric meters might have similar attributes, data points, and common processing logics (such as the electric anti-jump logic). Therefore, we can use an abstract smart meter model to standardize their attributes, measuring points, and common processing logics. Device model removes the complexities caused by device variety and enables you to construct applications based on common data model.

A device model can also be an abstract of a topological structure. A wind farm, for example, is such a model. Because all wind farms basically have similar attributes, some common calculation points, and relevant processing logics.

A hierarchical structure also exists among device models. The hierarchical
structure defined in EnOS™ platform is shown in the figure below:

.. image:: media/model_hierarchical_structure.png

## What is included in a device model ?

A device model consists the following elements:

- **Attribute:** Attributes are static description about a model. Taking the smart meter model as an example, the typical attributes are _name_, _manufacturer_, _model_, and _logic address_ of the meter.

- **Measuring point:** This is a dynamic measurement point in a model, and can be an aquisition point or a calculation point. An aquisition point is typically a data point or control point collected directly via communication. A calculation point is a data point added to the model depending on business needs. The data of a calculation point is derived from a certain business calculation logic rather than direct aquisition. However, calculation points and aquisition points are just a differentiation of model measurement points based on data source. They are the same in nature. Again, taking electric meter model as an example, voltage, current, and power are data points, while positive power per hour is calculation point.

- **Calculation script:** For measurement points, groovy script can be used to define some light-weight calculation logics and attach them to a model. This
calculation is triggered when the data obtained meet certain conditions. Here
also taking electric meter model as an example, for certain collection points,
the final value can only be obtained by performing some calculations on the
collected data. For instance, an electric meter of a certain manufacturer has a
voltage of ``UA=UA_tmp×10\^(P-4)``, where both UA_tmp and P are original values
collected. The final voltage UA can be only be obtained after calculation. In
this case, this special type of sub-model can be realized by creating a
sub-model for an electric meter basic model and then adding a calculation script
to the sub-model. Every time new data are received, this calculation condition
will be triggered to solve the final value. In this case, setting an expression
in the script will accomplish the target, as shown below:

```
output("UA", input("UA_tmp") \* (10 \*\* (input("P") - 4))).
```

## Why is a device model required?

The purpose of modeling or standardization of a device model is mainly for
application reuse.

For example, in an electric meter monitoring and analysis application, the
application needs to obtain the data at different measurement points and show,
process, and analyze the data. Although the electric meters adopted in different
projects differ, all electric meters have some common important measurement
points and some identical processing logics. Therefore, if certain electric
meter models can be abstracted from various actual electric meters, development
for common electric meter models will be possible in the application.

In actual projects, a matching between different electric meters and common
electric meter model will enable processing of various electric meter data with
the same application. In this way, the combination of a suitable model and a
model adapter (called **device template** on EnOS™ platform) will help
effectively address complexities in reality and greatly reduce repeated
application development work.

## What device models does EnOS™ provide out-of-the-box?

EnOS™ Cloud has accumulated a large set of device models in its model library. For the modesl that are supported out-of-the-box, see [Supported device models](deviceconnection_models).

## How to create a custom device model?

Before defining a device model, you must create a domain. You can then create site models under the domain, and create device basic models under the site models. Device sub-models can be created under each device basic model.

A high-level procedure of managing device models is as follows:

1.  Create a domain.

2.  Create site models under the domain.

3.  Create basic device models for a site model.

4.  Create device sub-models for a basic device model.

.. note:: A sub-model inherits attributes and measurement points of a basic model, which can’t be modified. However, new measurement points and calculation logics can be added to a sub-model.

<!--end-->
