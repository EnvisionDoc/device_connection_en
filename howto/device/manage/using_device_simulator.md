# Using Device Simulator

Users can use the device simulator function for the following purposes:

- Getting started with EnOS: Users can leverage this function to simulate a device to receive data to understand how to use the capabilities of EnOS.
- Testing and debugging: In application development, sometimes you need to simulate a device to send a specific value to verify device function. Real devices rarely sends that specific value. In this case you can use the device simulator to facilitate application development.

## About This Task

Users can use the device simulator to perform the following functions:

- Add, edit, query, or delete a device simulator
- Start or stop a device simulator.

## Before You Start

- You have created the device to be simulated. For information about how to create a device, see [Creating Devices](creating_device).
- You have access to device simulator. If not, contact your OU administrator to grant such permissions. See [Policies, Roles and Permissions](/docs/iam/en/latest/access_policy).

## Step 1: Add a Device Simulator

1. Select **Device Management > Simulator**.

2. Click **Add Simulator**, and select the device to be simulated in this page.
   
   The device list shows all the devices that have been created in the current OU. For simulation purpose, users can only select an inactive device. It is impossible to simulate any online or offline device.

3. Click **Confirm** to create a simulator for the selected device.
   
   In the list of simulators, you can see the simulator just created. Next, you need to define the simulation data sample for it.

## Step 2: Define the simulation data sample

1. Find out the simulator just created in the list of simulators, click **Edit** in the operation column.

2. Click **Download** in the pop-up window.

3. Input the simulation data sample in the downloaded template.
   
   The first column **timeOfDay** refers to the relative time stamp: you can enter the relative time stamp within one day (24 hours) in the format of H:MM:SS.

    The header from the second column are for the names of model points. You just need enter the name of the model point to be simulated in the header rather than that of all the model points. The content under the header are point values. If the data type is array, the format is [value1, value2, value3, ...]. Leave the cell empty if there is no value at the current time point.

4. In the pop-up window, click **Upload** to upload the simulation data.

5. Click **Confirm**.

## Step 3: Start the Device Simulator

1. Find out the target simulator and select **Start**;
   
2. Set the time when simulation ends in the pop-up window.
   
   The starting time of simulation is the system time when you click **Start** and the ending time of simulation should be within 90 days after the current system time.

## Results

The device simulator starts to simulate and send data. Users can suspend the device simulator, download the data sample or re-define data sample as needed. Only after suspending the device simulator can users re-define or download data samples.


