# Thing Model 

## What is a thing model?

A thing model is the abstraction of a specific device or non-device asset that you want to manage on EnOS. The thing model describes what the thing is, what it can do, what services it can provide, and what events can happen to it.

The thing model allows devices of similar characteristics to be unified into a common model to facilitate application processing. Take smart meter as an example, various electric meters might have similar attributes and common processing logics, and collects the same types of data. We can use an abstract model to standardize the description of a smart meter. Device model removes the complexities caused by device variety so that you can focus on the common characteristics of your assets in application programming.

A thing model can also represent a physical location or container where a collection of homogeneous or heterogeneous devices are deployed. In this sense, a thing model is the abstraction of a _non-device asset_. A wind farm, for example, is a non-device asset where wind turbines are deployed. All wind farms have common categories of attributes, runtime statistic, and states, such as average wind speed and average power production.

## Why is a thing model required?

The product of modeling can be utilized by applications.

For instance, an application that monitors electric meters can record, process, analyze, and display data it collects from actual electric meters. To describe an electric meter to the application, the common measurement points and processing logics of an electric meter needs to be abstracted. This is where a model comes in. A model contains common characteristics and capabilities of electric meters in quantified parameters, which can then be used in application processing. Modeling converts real-world complexities into standardized structure oriented for automation and programming.

## What out-of-the-box models does EnOS provide?

EnOS has a large set of device models in store in its model library. To check these models, go to **Model > Public Models**. 

## How to create a custom model?

In EnOS, defining a model means defining the following features of a device:

- Attribute: Static descriptive parameter of a device
- Measurement point: Runtime parameters of a device
- Services: A capability or method that can be called for remote operation or control 
- Events: Occurrences that can happen when the device is running. 

For more information, see [Device Modeling](model_overview).