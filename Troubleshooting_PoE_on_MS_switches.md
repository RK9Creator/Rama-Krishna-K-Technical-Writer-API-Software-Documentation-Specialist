# Troubleshooting PoE on MS Switches

## Overview

Power over Ethernet (PoE) allows Cisco Meraki MS switches to provide power to connected Powered Devices (PDs), such as access points, IP phones, and cameras.

This article provides troubleshooting procedures for identifying PoE issues on MS switches. It covers the following topics:

* PoE provisioning
* PoE underload and overload alerts
* Physical-layer troubleshooting
* PoE negative current errors

## Troubleshooting PoE Provisioning

PoE provisioning occurs during the initial link negotiation between the Powered Device (PD) and the Power Sourcing Equipment (PSE), such as an MS switch.

During negotiation, the PD communicates its power requirements to the switch. The switch uses the negotiated information to determine the amount of power to allocate to the PD.

The PD can use Link Layer Discovery Protocol (LLDP) and Cisco Discovery Protocol (CDP) to communicate PoE information.

The LLDP packet sent by the PD includes the requested power value. The LLDP packet sent by the switch includes the power allocated to the PD.

The CDP packet sent by the PD reports **Power Consumption** and **Power Request** values. The CDP packet sent by the switch reports the **Power Available** value.

The **Power Available** value can be higher than the **Power Request** value because the switch reserves an upper power limit based on the PoE class reported by the PD.

### Troubleshooting Steps

1. Start a dashboard packet capture on the switch port connected to the PoE device.

   * If the PD supports CDP or LLDP, continue with the following steps.
   * If the PD does not support CDP or LLDP, see [PoE Support on MS Switches](https://documentation.meraki.com/Switching/MS_-_Switches/Operate_and_Maintain/How-Tos/PoE_Support_on_MS_Switches) for information about how the switch provisions PoE for the device.
   * For more information about packet captures, see [Packet Capture Overview](https://documentation.meraki.com/Platform_Management/Dashboard_Administration/Troubleshooting_and_Support/Troubleshooting/Packet_Capture_Overview).

2. Review the LLDP packet sent by the PD.

   Verify the following field:

   * **Link Discovery Protocol > IEEE 802.3 > Power Via MDI > MDI Power Support > PD Requested Power Value**

   This field shows the amount of power requested by the PD during link negotiation.

3. Review the CDP packet sent by the PD.

   Verify the following fields:

   * **Cisco Discovery Protocol > Power Consumption**
   * **Cisco Discovery Protocol > Power Request**

   The **Power Consumption** value shows the power currently reported by the PD. The **Power Request** value shows the amount of power requested by the PD.

4. Review the LLDP packet sent by the switch.

   Verify the following field:

   * **Link Discovery Protocol > IEEE 802.3 > Power Via MDI > MDI Power Support > PSE Allocated Power Value**

   This field shows the amount of power that the switch allocates to the PD.

5. Review the CDP packet sent by the switch.

   Verify the following field:

   * **Cisco Discovery Protocol > Power Available**

   The **Power Available** value represents the upper power limit that the switch makes available to the connected PD based on the negotiated PoE class.

### Example

The following example uses a packet capture collected on port 5 of an MS switch during link negotiation with a Meraki MR access point.

The same packet analysis can help troubleshoot other PoE-powered devices that use CDP or LLDP.

> **Note:** The Intelligent Packet Capture tool requires the switch port to be active. If the switch port is down, the packet capture fails with a **422: Capture Failed** error. Verify that the switch port is active before starting the packet capture.

In this example, the MR access point requests **20 W** during link negotiation and operates as **Class 4**.

The LLDP packet sent by the access point shows the **Power Class** and **Power Requested** values reported by the PD.

The CDP packet sent by the access point reports the **Power Consumption** and **Power Request** values.

The LLDP packet sent by the switch shows that the switch allocates the requested power to the access point.

The CDP packet sent by the switch shows that the switch budgets **30 W** based on the negotiated PoE class, even though the access point requests **20 W**.

The **30 W** value represents the upper power limit available to the PD. It does not represent the PD's actual power consumption.

> **Note:** The switch budgets power based on the PoE classification reported by the connected device. The PoE budget can exceed the switch's current power consumption because the budget represents the potential power allocation rather than actual consumption. The switch continues to provide power while the total actual power consumption remains within the available power budget. If the switch exceeds its available power budget, lower-numbered ports take precedence over higher-numbered ports, and the switch can remove power from higher-numbered ports.

## Troubleshooting PoE Underload and Overload Alerts

### PoE Underload Alerts

A PoE underload event occurs when a PoE-powered device consumes less power than expected for an extended period.

An underload condition can indicate that the connected device is not operating correctly or that the device does not transition to a low-power state as expected.

### PoE Overload Alerts

A PoE overload event occurs when a PoE-powered device draws more power than the negotiated allocation or exceeds the supported power level.

An overload condition can occur even when the switch has sufficient total PoE power available for other ports.

When an overload occurs, verify the device's power requirements and compare them with the power that the switch provides to the port.

## Troubleshooting the Physical Layer

Physical-layer problems can prevent an MS switch from providing stable PoE to a connected device.

### Troubleshooting Steps

1. Verify that the MS switch supports PoE.

   Cisco Meraki PoE-capable switches typically include a **P** at the end of the model number, such as **MS220-24P**.

   For more information, see the [MS Family Datasheet](https://meraki.cisco.com/lib/pdf/meraki_datasheet_ms_family.pdf).

2. Verify that PoE is enabled on the switch port connected to the PD.

   1. Navigate to **Switching > Monitor > Switch ports**.
   2. Select the affected switch port.
   3. Verify that **PoE** is enabled.
   4. Review the current PoE status and power information for the port.
   5. Select **Details** to view additional port information.

   For more information, see [Configuring PoE on MS Switches](https://documentation.meraki.com/Switching/MS_-_Switches/Operate_and_Maintain/How-Tos/Configuring_PoE_on_MS_switches).

3. If a UPS powers the switch, connect the switch directly to a wall outlet and check whether the PoE issue persists.

4. Connect the PD to another PoE source, such as:

   * Another PoE-capable switch port
   * A PoE injector
   * Another PoE-capable switch

   If the PD receives power from another source, the original switch port or switch might be causing the issue.

5. Connect a non-PoE device, such as a laptop, to the affected switch port.

   Verify whether the port establishes a network connection.

6. Check whether another connected device is supplying power to the switch.

   If a connected device supplies PoE to the switch instead of drawing power from it, the switch can disable PoE to protect the equipment.

   Disconnect connected devices one at a time and check whether PoE resumes after disconnecting a specific device.

7. If the issue persists after completing the preceding troubleshooting steps, perform a [factory reset](https://documentation.meraki.com/Platform_Management/Dashboard_Administration/Troubleshooting_and_Support/Troubleshooting/Resetting_Cisco_Meraki_Devices_to_Factory_Defaults) on the switch.

> **Caution:** A factory reset removes the device's local configuration and returns the device to its factory-default state. Verify the impact of a factory reset before performing this step.

## Additional Resources

* [Configuring PoE on MS Switches](https://documentation.meraki.com/Switching/MS_-_Switches/Operate_and_Maintain/How-Tos/Configuring_PoE_on_MS_switches)
* [PoE Support on MS Switches](https://documentation.meraki.com/Switching/MS_-_Switches/Operate_and_Maintain/How-Tos/PoE_Support_on_MS_Switches)
* [Combined Power on MS Devices](https://documentation.meraki.com/Switching/MS_-_Switches/Operate_and_Maintain/How_Tos/Combined_Power_on_MS_Devices)
* [Packet Capture Overview](https://documentation.meraki.com/Platform_Management/Dashboard_Administration/Troubleshooting_and_Support/Troubleshooting/Packet_Capture_Overview)

## Requesting a Return Merchandise Authorization (RMA)

If the troubleshooting steps in this article indicate that the switch requires replacement, contact [Cisco Meraki Support](https://documentation.meraki.com/Platform_Management/Dashboard_Administration/Troubleshooting_and_Support/Support/Ways_to_Contact_Meraki_Support) to begin the RMA process.

Be prepared to provide the following information:

* Troubleshooting steps that you completed
* Results of the troubleshooting steps
* Shipping address for the replacement device

For more information about device replacement, see the [RMA documentation](https://documentation.meraki.com/Platform_Management/Dashboard_Administration/Troubleshooting_and_Support/Support/Returns_%28RMAs%29%2C_Warranties_and_End-of-Life_Information).

> **Note:** Cisco Meraki Support might recommend additional troubleshooting steps depending on the nature of the issue.
