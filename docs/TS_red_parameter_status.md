# Troubleshooting Red Parameter Status

| Field | Value |
|---|---|
| Version | 1.0 |
| Status | Draft |


## 1. Purpose

Provides step-by-step instructions for diagnosing and fixing red status alerts for CORIOLIX parameters and sensors.

## 2. Diagnostic Questions

### Are most (or all) of the sensors red on the sensor status page?

If yes, this would indicate that either the source data feed (e.g. SCS output) is unavailable, or the OpenRVDAS loggers have failed. Go to Solution A.

If the issue seems limited to one sensor, it may be a result of bad/missing metdata or a bad regex. Go to Solution B.

If the issue involves a few but not all sensors, it may be related to post-processing. Go to Solution C.

### Are raw data files for the affected sensor being created in real-time on the ship-side OpenRVDAS VM?

If YES, this would indicate that the sensor's openrvdas logger is running but the data are either failing to parse, are unable to make it into the CORIOLIX database, or the data post-processing is failing. Go to Solution B.

## Solution A: Fixing multiple sensor data feeds

### Is the vessel's data aqcuisition service (e.g. SCS) outputting data feeds?
If the data source is a data acquisition system (e.g. SCS) rather than individual sensors, confirm with a tech onboard that the system is on and configured to output data feeds to the OpenRVDAS VM.  UDP traffic can be confirmed using tshark.

### Are the loggers running?
Confirm the loggers are running on the OpenRVDAS VM.  If they are not running, restart them all. A common cause for this issue is a reboot of the OpenRVDAS or CORIOLIX VM (OpenRVDAS writes to CORIOLIX).

### Are the loggers crashing?
If the loggers fail to start or crash immediately, check the logs. Common issues include (a) unable to access the CORIOLIX database (network/firewall issues), (b) unable to write to the CORIOLIX VM (ran out of disk on the CORIOLIX VM), (c) unable to write locally (ran out of disk on the OpenRVDAS VM), (d) a bad cruise.yaml file (manually altered, outdated, or corrupt).  If disk space is an issue, space will need to be cleared/added before the loggers can be restarted.

### Is it only shore-side that is affected (not ship-side)?
If only shore-side is affected, and the loggers are running on shore-side but no data files are being created, the issue may be the transfer of data from ship to shore.  Check the status of the MQTT data feed for one of more sensors using an MQTT client. Confirm that the ship-side and shore-side OpenRVDAS VMs can both access (ping) the MQTT broker. Confirm that the MQTT broker is running.

## Solution B: Fixing a single sensor data feed

### Is this a recently enabled/modified sensor?

If YES, the most common issues are (a) forgetting to refresh CORIOLIX on both ship and shore, and (b) incorrect or missing sensor/parameter metadata.  If the sensor (or parameter) was copied from another sensor, check that all necessary metadata changes have been made.  Make sure there that each enabled sensor is listening to a different UDP port (unless the sensor if part of a sensor group).

If NO, the problem could be a crashed OpenRVDAS logger, a change in the sensor data message output (e.g. a missing field), or missing ancillary data needed for post-processing.

### Are raw data files being created in real-time on ship-side?

If NO, is the logger running for that sensor?  If the logger is not running, try restarting it using the OpenRVDAS GUI.  If it crashes immediately, check the logs. The most common issue is bad sensor metadata affecting the cruise.yaml file. If no logger exists at all, it may be missing from the cruise.yaml file due to missing sensor metadata such as the UDP port.

If YES, does the message format look similar to the last working message displayed in CORIOLIX? (Are there any missing fields, new fields, weird contents, etc.)?  If they differ, the regex may need to be updated to allow the new message format to parse successfully. Update the regex and hit the "refresh" button. The refresh will need to be done on both ship and shore separately.


## Solution C: Fixing post-processed parameters

If parameters from multiple sensors are affected (red) all at once, it may be due to missing ancillary data required for post-processing.  For example, if the SOG data feed fails, true winds can no longer be calculated for all anemometers.

### Are the affected parameters all post-processed parameters (e.g. true winds)?

If YES, check to see if they have an ancillary data feed in common that may be missing (e.g. SOG). If the ancillary data are missing and a default value for the ancillary data feed isn't provided, or if the ancillary data is outdated, the post-processed parameter cannot be calculated.  To resolve this issue, the ancillary data feed will need to be restored.  Confirm that the ancillary data are flowing into CORIOLIX. Also confirm the settings on the Post-Processing Data Sources list match an available ancillary sensor.  Changes to this list require a refresh on ship and shore.

If NO, got to Solution A.
