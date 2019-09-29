# Tutorial: Setting Alert Triggering Delay Timer

In some scenarios, alerts need to be delayed until a specified time later instead of being immediately triggered when there is an anomaly.

For instance, you use EnOS to monitor the load factor of an electricity producing installation: when the load factor is over 120% for, say, 120 minutes, EnOS triggers the "Overload" alert. Alert triggering delay timer can be used in this scenario.

## Concepts

- _Anomaly_ : When the measurement point data sent from a device satisfies the condition set in alert rule, an anomaly is generated.
- _Alert triggering delay_ : The duration of time between an anomaly is generated and the related alert is triggered a specified time later.

## Usage Notes

### Start and End of Alert Triggering Delay

- **Timer starts**: The delay timer starts working the moment an anomly is generated. As the following illustrates, timer kicks off at 1min, as the measurement point value satisfies the alert rule. At 2min, the measurement point satisfies rule, timer count increases 1.
- **Timer resets**: If the anomaly is recovered before the timer times out, the timer is reset. As the following illustrates, the timer is reset at 3min when the anomaly disappears.
- **Timer restarts**: At 4min, anomaly re-appears, timer re-starting from 0 minutes.
- **Alert triggered**: At 7min, the 3-minute timer times out. An alert is triggered.
- **Alert dismissed**: The alert is dismissed immediately the underlying anomaly is recovered. At 8min, the alert is dismissed as the underlying anomaly disappears.

.. image:: ../../media/alert_triggering_delay_timer.png

## About This Task

This topic describes how to use alert triggering delay in a smart-building scenario. For details of this scenario, see [Tutorial: Setting Different Alert Thresholds for Devices of the Same Model](/docs/device-connection/en/latest/howto/alert/set_alert_rule_for_device.html#id2). For the sake of convenience, we will use only the code snippet of the fridge ammeter.

## Before You Start

- You need to have necessary access to model, device management, and alert. If not, ask your OU administrator to grant you the access. For more information, see [Policy, Role, and Access](/docs/iam/en/latest/access_policy).
- You have finished [Tutorial: Setting Different Alert Thresholds for Devices of the Same Model](set_alert_rule_for_device).

## Procedure

1. Select **Alert > Alert Content** . Create a content named "1 minute current alert". For how to create an alert content, see [Creating Alert Content](create_alert_content).

  .. image:: ../../media/alert_triggering_delay_new_content.png

2. Select **Alert > Alert Rule** . Create a new rule as follows. For how to create an alert rule, see [Creating Alert Rule](create_alert_rule).

  .. image:: ../../media/alert_triggering_delay_new_rule.png

  Set **Alert Triggering Delay** to **60** seconds. Click **Confirm**.

3. Copy sample code **fridgeMQTT** from [Tutorial: Setting Different Alert Thresholds for Devices of the Same Model](/docs/device-connection/en/latest/howto/alert/set_alert_rule_for_device.html#enos-java-sdk-for-mqtt) to your IDE and make the following changes:

  - Change `MeasurepointPostRequest request = MeasurepointPostRequest.builder().addMeasurePoint("rt_current", random.nextDouble() * 1500).build()` to `MeasurepointPostRequest request = MeasurepointPostRequest.builder().addMeasurePoint("rt_current", random.nextDouble() + 1500 ).build()` , so that the telemetric data **Real-time current** sent from the simulated ammeter all exceed **Max Current Allowed**, generating anomaly.
  - Change `Thread.sleep(10000L)` to `Thread.sleep(30000L)` so that the simulated ammeter sends telemetry every 30 seconds. In this way, if the ammeter sends 3 consecutive telemetry data **Real-time current**, which is above **Max Current Allowed**, to EnOS, **1 minute current alert** will be triggered.

4. Run **fridgeMQTT** .

## Results

Select **Alert > Alert Record** . Set **Model** to **Current Meter** and click **Search** .

Once the ammeter sends 3 consecutive data to EnOS, which are all above the threshold, across 60 seconds, you can see the triggered alert here as follows:

.. image:: ../../media/alert_triggering_delay_result.png

You can also call an API to query triggered alerts, the measurement point data value included in the respond is the telemetric value when the timer starts timing.
