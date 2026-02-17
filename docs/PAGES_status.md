![](img/banner_status_list.png)

# Sensor Status Page
This page provides a quick table style view of every active (enabled) scientific data parameter in the CORIOLIX system.  Parameters are organized alphabetically as cells and grouped by sensor class (flowthrough, Navigation, Atmospheric, and Other).  Each cell in the status tableprovides the parameter name, current value with units, sensor location, and sensor ID.  Cells are hyperlinked to the parent sensor page, click the cell to be redirected.

## Sensor Status
The status of a parameter is indicated by the color of the cell.  Black cells are normal (ok).  Cell color other than black indicates that the parameter is experiencing an abnormal condition and a warning has been issued.  

These status indicators are customizable per user. Instructions on how to configure them to your specific needs can be found in the "Setting Up Your User Specific Alerts" section on the [Accounts](https://github.com/R-DESC/CORIOLIX_docs/blob/1daa3f77a5631dbe512847a0fb8363ce4f7a0ad5/docs/PAGES_account.md?plain=1#L54) page. 

![](img/status_table.png)

## System Status
The system status page reports the CORIOLIX deployment state of health.  Because CORIOLIX is part of a suite of monitored equipment, the system status page pulls system hardware state of health from the external monitoring system rather than duplicate that monitoring locally.

![](img/status_system.png)
