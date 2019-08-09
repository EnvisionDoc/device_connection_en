# Creating Alert Rules

This topic instructs how to create the triggering rules of an alert.

You can define alert triggering rules for a data measuring point of a domain or the communication model of a device point.

In wind turbine example, you can define an alert triggering rule when the wind speed is over 30m/s, the severity level, and the content to report when the alert occurs.

The alert rules are for specified model, and can be applied to the following ranges depending on the settings:

- All assets instantiated based on the certain model

- The assets in the specified asset tree which are instantiated based on the certain model

## Before You Start

Ensure that the alert content to be used by the triggering rule is created. For more information, see [Creating Alert Content](create_alert_content).

## Procedure

1. Click **Alert > Alert Rule** on EnOS Console navigation menu.

2. Click the **New Rule** button to define a new alert triggering rule.

   .. image:: media/create_alert_rule.png

   - **Rule ID**

     Required. User-defined identifier for the rule. Supports letters, numbers and period (.), underscore(_), and dash (-). No more than 50 characters.

   - **Select Model**

     Select the asset model and measurement point that will be applied to the alert rule. For more information about module, see [Model Management](../model/model_overview).

   - **Condition**

     Select a triggering condition for the alert rule and enter the corresponding value or value scope for the condition.

   - **Scope**

     Select the scope of the asset to which the alert applies to according to the selected asset model. The triggering rule can apply to a whole station or specific device of the station.

     - If **All Devices** is selected, the rule applies to all asset instances based on the previous selected model.

     - If a certain asset tree is selected, the rule applies to all asset instances based on the previous model in this asset tree. You can also select a node in an asset tree. Then the rule only applies to the specified nodes. When the asset tree is updated, the rule is automatically inherited to the relevant node. For example, when a child node is added under a node, the child node automatically inherits the rule defined on the parent node.



   - **Alert Content**

     Select alert content for the rule from the list of defined alert content. An alert content can be assigned to multiple triggering rules.

   - **Alert Severity**

      Select an alert severity level from the list of defined severity levels according to your business needs.

   - **Tag**

     Optional. You can attach custom tags to the rule for easy management.

   - **Alert Masking**

     Only available when **Scope** is an asset tree. When it is enabled, all the alerts associated with the child nodes of the selected asset tree will be blocked. It helps reduce irrelevant alerts. See [Masking Alert](masking_alert).

   - **Enable**

     Whether to enable this alert rule.
