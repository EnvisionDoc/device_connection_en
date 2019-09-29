# Managing Models

In **Model**, click **Edit** for the private model you choose. Go to **Feature Definition** , use the **Export Model** , **Import Model** buttons to manage your existing model. You can download and edit existing model elements (attributes, measuring points, events, services) and then import the edited model onto EnOS.

## About This Task

After creating a model, you may have to change its elements or add new elements as required. This task helps you manage existing models by using EnOS-defined JSON files or Excel files.

## Before You Start

You need to have proper authority to view and manage models. Contact your OU administrator for access. For more information, see [Policy, Role, and Access](/docs/iam/en/2.0.8/access_policy).

## Procedure

To edit your existing model, export the model first, then edit the downloaded file and import it to replace the existing model. 

### Exporting 

1. Go to **Model**, click **Edit** for the model you choose.

2. Go to **Feature Definition**, click **Export Model**. Select the format of the file you want to download: JSON or Excel.

3. Edit the downloaded file.

  The downloaded JSON file is named *model_Modelname_Timestamp.json*, where `Modelname` is the **Model Name** field from EnOS, and `Timestamp` is the time you download this JSON file in *hhmmssSS*, SS indicating millisecond.

  The downloaded Excel file is named **ThingModel_Modelname.xlsx** where `Modelname` is the **Model Name** field from EnOS.

4. You can edit the file to modify existing model elements or add new elements.

 In application development, if you need to fast locate model elements, you can use the **Priority** attribute in the JSON file or the **Ranking** attribute in the Excel sheet. Priority or Ranking 0 indicates this element is created when you create the model. Elements you add to the existing model starts from 1 and increments by 1 for every later added element. You can also give the elements random non-negative integers as their **Priority** or **Ranking** as you see fit. The **Priority** or **Ranking** numbers can be discontinuous, non-sequenced, and duplicate. 

### Importing

1. On **Feature Definition**, click **Import Model** .

2. In the pop-up, click **Upload**. Select the local JSON file.

3. Click **Import** if your uploaded file passes format verification.
 
 You may have to correct format issues before you can complete importing. Refer to the error messages prompted after you have uploaded the JSON file.

## Results

The model you selected has been changed based on the imported file you edited.

