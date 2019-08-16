# Unit 2: Configuring Storage Policy for the Device Data

EnOS Time Series Database (TSDB) enables you to store important and frequently-accessed business data with a variety of data storage options. Data is stored in TSDB by categories (data types and storage time), thus reducing data storage costs and enhancing data reading efficiency.

Before the CPU and memory usage data of the PC is uploaded to EnOS Cloud, you need to configure data storage policy for the uploaded data. Otherwise, the uploaded data will not be stored in EnOS TSDB by default.

In this unit, configure storage policy for the following measuring points that have been defined when creating the computer model.

| Measuring Point     | Storage Type       | Description                                                  |
| ----------------- | ------------------ | ------------------------------------------------------------ |
| cpu_used      | AI Raw Data        | When the CPU usage data of the PC is uploaded, store the raw data of the *cpu_used* measuring point in TSDB. |
| mem_used        | AI Raw Data | When the memory usage data of the PC is uploaded, store the raw data of the *mem_used* measuring point in TSDB. |
| cpu_percent   | AI Raw Data | When the CPU usage percentage data of the PC is calculated by the stream processing job, store the calculated data in TSDB. |
| mem_percent | AI Raw Data | When the memory usage percentage data of the PC is calculated by the stream processing job, store the calculated data in TSDB. |

For detailed description of the supported storage types, see [Configuring TSDB Storage](https://www.envisioniot.com/docs/data-asset/en/latest/configuring_tsdb_storage.html).

## Creating a storage policy group

If there is no storage policy created for your organization, take the following steps to create one:

1. Log in EnOS Console and select **Time Series Data > Storage Policy**.
2. Click **New Group** to create a storage group. Enter the group name, and select a group model (select the *Computer* model for this tutorial).
3. Click **OK** to save the storage policy group configuration.


## Configuring storage policy

After the storage group is created, you can see all the TSDB storage policy options listed under the storage group tab. Configure **AI Raw Data** storage policy for the *cpu_used*, *mem_used*, *cpu_percent*, and *mem_percent* measuring points by taking the following steps:

1. Move the cursor on the **AI Raw Data** storage type and click the **Edit** icon to open the **Edit Storage Policy** page.

2. From the **Storage Time** drop down list, select the storage time for the data (for example, 3 months).

3. Select the *Computer* model and the measuring points.

4. Click **OK** to save the storage policy. See the following example.

   .. image:: media/storage_policy_config.png

With the storage policy configured, ingested data and calculated data of the PC will be stored as **AI Raw Data** storage type.

## Next Unit

[Connecting the PC into EnOS and Ingesting Data](connecting_device)

<!--end-->
