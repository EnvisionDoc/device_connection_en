# Masking Alert

Masking alerts refers to blocking asset alerts under a node in an asset tree.

The following figure illustrates the alert masking mechanism by an example:
- Node 3 and Node 6 in the asset tree are device instances of the same model SampleModel.
- The SampleModel applies to Tree A as per the definition of the alert rule and turns on the alert masking.

![](media/alert_masking.png)

In this example, the alert masking takes effect on Node 3 and Node 6. That is:
- When Node 3 and Node 7 generate alerts at the same time, only the alerts of Node 3 are received while the alerts of Node 7 are blocked.
- When Node 6, Node 8 and Node 9 generate alerts at the same time, only the alerts of Node 6 are received while the alerts of Node 8 and Node 9 are blocked.
- Nodes 1, 2 and 4 belong to Tree A, but they are not the instances of SampleModel, so they are not subject to this alert rule.

Assume that there are multiple instances of SampleModel on the same path. In the following figure, if Node 2 is also an instance of SampleModel, the alert rule will be applied to both Node 2 and Node 6, but the application will only receive the alerts in the top of the path, i.e. the alerts of Node 2.

![](media/alert_masking_2.png)


## Scenarios

The alert masking is applicable to the following scenarios:

When a parent node generates an alert for some reason, other nodes associated with it will also generate an alert. For example, when a box-type substation trips to generate a *root alert*, its sub-devices, including inverters and junction boxes, will also generate a large number of *derivative alerts* due to state change. But the root cause of these alerts is still the trip at the parent node. In this case, the application only receives the root alert and does not receive any *derivative alert*.


## Enabling Alert Masking

The alert masking can be enabled in the **Alert Rule**. The prerequisite for enabling the masking is that: the model which the asset belongs to has an alert rule defined, and the scope of the alert rule is an selected asset tree or a node of an asset tree. Specific steps are given as follows.

1. Select **Alert Management > Alert Rules** on EnOS Console navigation menu.

2. Click the Edit button in the **Operations**.

 .. |edit| image:: ../../media/button_edit.png

3. Check the settings of **Scope**, which must be a specified asset tree or a node of an asset tree.

4. Enable **Alert Masking** and click **Confirm** to save the settings.

## Results

After the alert masking is enabled, the derivative alerts are not reported to the application by default. The application can get the information of the derivative alerts by enabling the `includeDerivative` parameter in the following alert service API:
- Search Active Alerts
- Search History Alerts

For information about how to invoke an API, see the corresponding document in **EnOS Console > EnOS API**.


