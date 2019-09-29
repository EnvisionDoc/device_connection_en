# Unit 6: Calculating Memory Usage Percentage

With the total memory and used memory data ingested, we can now develop a stream data processing job to calculate the real-time memory usage percentage of the PC.

EnOS Stream Analytics provides a Multi-Point Merging template, which supports calculation among multiple points and attributes.


## Step 1. Creating a stream data processing job

1. Log in EnOS Console and click **Stream Data Processing** > **Stream Development**.

2. Click the **+** icon above the stream processing job list to open the **New Stream** window and complete the following basic settings:

   - **Method**: Select **New**.
   - **Message Channel**: Select **Real-Time**.
   - **Name**: Enter a name for the stream data processing job.
   - **Template**: Select **Multi-Point Merging**.
   - **Version**: Select **V1.0**.

3. Click **OK** to save the basic information of the stream processing job. See the following example:

   .. image:: media/create_stream.png

## Step 2. Configuring data processing policy

In this step, configure the *Multi-Point Merging* template for calculating the memory usage percentage with the following parameters:

| Field                | Value                                          | Description                                                  |
| -------------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| Triggering Mode      | Point                                          | The data processing job is triggered by the arrived input data. |
| Timing Interpolation | lastValue                                      | The interpolation policy.                                    |
| Output Point         | Computer::mem_percent                          | The measuring point receiving the processed data.            |
| Triggering Point     | Computer::mem_used                             | The measuring point triggering the data processing job.      |
| Output Logic         | `${Computer::mem_used}/${Computer::mem_total}` | The expression for getting the memory usage percentage.      |

See the following example:

.. image:: media/stream_config.png

For more information about the *Multi-Point Merging* template, see [Multi-Point Merging Template](/docs/data-asset/en/latest/learn/multi_point_overview.html).

## Step 3. Running the data processing job

After the data processing job configuration is completed, you can publish it online:

1. Click **Save** to save the configuration of the data processing job.

2. Click **Release** to release the job online.

3. On the **Stream Operation** page, find the data processing job that is online, and then click the  the **Start** icon to start the job.


The data processing job will start running if there is no error.

## Step 4. Viewing the calculated data

On the **Time Series Data > Data Insights** page, view the calculated memory usage percentage data:

1. In the **Select Time Range** section, select or specify the time range for querying data (for example, `1D` for the current day). 

2. Click the **Select Devices** input box, and search for and select the `PC_Win10` device. 

3. In the **Select Data Type** section, select **ALL**.

4. In the **Selected Measuring Points** column, click on the selected device name, expand the list of measuring points, and select the `mem_percent` measuring point.

5. View the memory usage percentage data in the chart. See the following example:

   .. image:: media/memory_data.png



<!--end-->
