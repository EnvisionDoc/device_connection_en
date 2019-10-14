# Tutorial: Using Device Simulator

Using device simulator, you can simulate a device that connects to and sends data to EnOS without using the EnOS Java SDK for getting started or troubleshooting. For connecting device by using EnOS Java SDK, see [Connecting a Simulated Device into EnOS](../../../tutorial/connecting_device_simulated/index).

In this tutorial, we simulate a device that sends data to EnOS. We insert some abnormal data into the data sample to trigger an alert. Meanwhile, we configure a storage policy to store the data sample in the TSDB and view the data in line chart in **Time Series Data > Data Insights** .

## About This Task

This tutorial simulates a scenario where an ammeter reports its reading once every minute. If the current is above 60mA， alert "Current is above the threshold" is triggered.

.. image:: ../../../media/simulated_ammeter.png

We will view the data reported in **Time Series Data > Data Insights** .

The steps we will follow is as follows, whose numbers indicates the order of the steps as described in [Procedure](#Procedure).

.. image:: ../../../media/simulated_ammeter_flowchart.png

## Before You Start

- Obtain access to the following function modules. For more information, see [Policy, role, and access](/docs/iam/en/2.0.9/access_policy):

    - Model
    - Device Management
    - Alert
    - Times Series Data

## Procedure

### Step 1: Create Model, Product, and Registering Device

1. Create a model for the simulated ammeter, whose parameters are as follows. For information on how to create a model, see [Creating a model](../../model/creating_model)：

  .. csv-table:: Model for Simulated Ammeter
     :widths: auto
     :header: "Field", "Value"

     "Model ID", "test.ammeter"
     "Model Name", "Simulated Ammeter Model"
     "Category", "None"
     "Created From", "No"
     "Source Model", "No"
     "Description", "No"

2. Define the following elements for the model we just created:

  .. csv-table:: Elements of Simulated Ammeter
     :widths: auto
     :header: "Field", "Value"

     "Feature Type", "Measurement Points"
     "Name", "Real-time Current"
     "Identifier", "current"
     "Data Type", "AI"
     "Quality Indicator", "No"
     "Data Type", "double"
     "Unit", "Electric Current: milliampere | mA"
     "Tags", "No"
     "Description", "No"

3. Create a product based on **Simulated Ammeter Model** , whose parameters are as follows. For information on how to create a product, see [Creating a Device Collection (Product)](creating_product):

  .. csv-table:: Simulated Ammeter Product
     :widths: auto
     :header: "Field", "Value"

     "Product Name", "Simulated Ammeter Product"
     "Asset Type", "Device"
     "Model", "Simulated Ammeter Model"
     "Data Type", "Json"
     "Certificate-based Authentication", "Disabled"
     "Description", "None"

4. Create a device based on **Simulated Ammeter Product** , whose parameters are as follows. For information on how to create a device, see [Creating device](creating_device):

  .. csv-table:: Simulated Ammeter Device
     :widths: auto
     :header: "Field", "Value"

     "Product", "Simulated Ammeter Product"
     "Device Key", "simulatedAmmeter"
     "Device Name", "Simulated Ammeter Device"
     "Time Zone/City", "UTC+08:00"

### Step 2: Configure TSDB Stroage Policy

1. In **Time Series Data > Storage Policy** , configure storage policy for the simulated ammeter.

  1. Select an existing group or create a new group. Click **Edit Group** . Select **test.ammeter** and click **OK** to include the ammeter model into the policy group.

  2. Select |edit| besides **AI Raw Data** . Select **test.ammeter** to include all its measurement points into this policy.

    As we defined **Real-time Current** as AI data when creating a model and this tutorial doesn't require normalized data, so we put **Real-time Current** into **AI Raw Data** .

    .. |edit| image:: ../../../media/button_edit.png

### Step 3: Configure Alert Rule

1. In **Alert** , create a rule as follows. TO create a rule, corresponding severity, type, and content are required. For information on how to create these, see [Managing Device Alerts](../../alert/index.rst):

  .. csv-table:: Alert Rule Configuration
     :widths: auto
     :header: "Field", "Value"

     "Rule ID", "ammeter_high_alert"
     "Select Model", "Simulated Ammeter Model"
     "Measuring Point", "Real-time Current"
     "Condition", ">"
     "Value", "60"
     "Alert Content", "Current is above threshold"
     "Alert Severity", "Warning"
     "Alert Triggering Delay", "0"
     "Alert Masking", "Disabled"
     "Enabled", "No"

  This rule stipulates that, an **Current is above threshold** alert of **Warning** severity will be triggered immediately if **Real-time Current** reported by devices derived from **Simulated Ammeter Model** exceeds 60 (milliampere). 

### Step 4: Launch Device Simulator

1. In **Device Management > Simulator** , create a simulator for **Simulated Ammeter Device** . For information on how to create a simulator, see [Using Device Simulator](using_device_simulator).

  .. image:: ../../../media/added_simulated_ammeter.png

2. Upload data sample for **Simulated Ammeter Device** . For information on how to upload data sample, see Using Device Simulator](using_device_simulator).

  In this sample, there are two columns of data. Column **timeOfDay** indicates the relative time in a day from 0:00:00 to 23:59:00 in minute. Column **current** , the identifier for **Real-time Current**, indicates the measurement point value reported by the device.

  When we created the alert rule, we set the alert threshold to 60 milliampere. So in this sample, we need to modify value in column **current** so that at least one piece of value is above 60 to trigger the alert **Current is above threshold** .

  The best practice is setting some values above 60 at earlier moments so that you don't have to wait long to see an alert triggered. In this tutorial, we set abnormal values at Minute 2, 7 and 10 as follows:

  .. image:: ../../../media/simulator_data_sample.png

3. Start the simulator, setting the end time at 24 hours later.

  You can actually set the end time whenever you'd like to, but setting it much later leaves enough time for TSDB to absorb enough data to generate a report.

## Results

### Data Insights

Wait until enough time later and go to **Time Series Data > Data Insights** . Select device **Simulated Ammeter Device** . View the **Real-time Current** data report in minute.

  .. image:: ../../../media/simulated_ammeter_tsdb_data.png

### Alert Record

In **Alert > Alert Record**, select model **Simulated Ammeter Model** and view the historical alerts. You can see that several alerts have been triggered by the anormal data.

  .. image:: ../../../media/simulated_ammeter_historical_alerts.png