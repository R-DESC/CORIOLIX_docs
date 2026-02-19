# Sensor Onboarding Guide

This guide provides a comprehensive overview of onboarding a new instrument to CORIOLIX. The process involves incorporating sensors into the system to enable data collection, processing, and access through the CORIOLIX interface.

>[!NOTE]
 This guide assumes that:
> * The new sensor has been physically deployed on the vessel
> * The sensor is operational and properly configured
> * The sensor is configured to output RS-232 serial data
> * Users have appropriate access privileges in CORIOLIX

## Overview

"Incorporate into CORIOLIX" encompasses all actions needed to:

- Connect and communicate with the sensor
- Configure a data logger for the sensor
- Access the sensor and its data through CORIOLIX
- Ensure proper data processing and quality control

## Onboarding Process

The sensor onboarding process consists of four main steps:

### [Step 1: Create New Sensor](HT_sensor_onboard_step1.md)

Create a comprehensive sensor record in the CORIOLIX database with all required metadata and configuration details.

**Key Activities:**

- Define sensor overview and identification
- Configure native data feed settings
- Set up data format and parsing rules
- Specify processing requirements
- Document operating limits and ownership

### [Step 2: Configure Parameters](HT_sensor_onboard_step2.md)

Define and configure individual sensor parameters for data processing and display.

**Key Activities:**

- Set parameter names and descriptions
- Define units and data specifications
- Configure database storage options
- Set up display preferences

### [Step 3: Network Logger Setup](HT_sensor_onboard_step3.md)

Create and configure the OpenRVDAS sensor-to-network logger for data transmission.

**Key Activities:**

- SSH to peripheral OpenRVDAS system
- Configure cruise configuration with wire2net settings
- Restart logging services

### [Step 4: Data Logger Setup](HT_sensor_onboard_step4.md)

Establish the central data logger for processing and storing sensor data.

**Key Activities:**

- SSH to central OpenRVDAS system
- Configure cruise configuration with net2db settings
- Restart logging services
- Verify data flow

## Quick Navigation

| Step | Description | Estimated Time |
|------|-------------|----------------|
| [Step 1](HT_sensor_onboard_step1.md) | Create Sensor Record | 15-30 minutes |
| [Step 2](HT_sensor_onboard_step2.md) | Configure Parameters | 10-20 minutes |
| [Step 3](HT_sensor_onboard_step3.md) | Network Logger | 5-10 minutes |
| [Step 4](HT_sensor_onboard_step4.md) | Data Logger | 5-10 minutes |

>[!TIP]
>Best Practices
>- Complete all metadata fields for better data management
>- Test regex parsing expressions thoroughly
>- Document any custom processing requirements
>- Verify data flow at each step before proceeding

## Special Topics

### [Regular Expression (RegEx) Guide](RegExGuide.md)

Learn how to build regular expressions for parsing sensor messages. This comprehensive guide walks through creating regex patterns for NMEA-style messages and other sensor data formats.

**Topics Covered:**
- Understanding message structure
- Building regex patterns step-by-step
- Named capture groups for data extraction
- Handling optional fields and variable formats
- CORIOLIX-specific regex implementation

>[!IMPORTANT]
>Understanding regex patterns is crucial for completing the Message Format String in Step 1. Review this guide before attempting to parse complex sensor messages.

### Importing and Exporting Sensors
If a sensor already exists in one CORIOLIX installation, there is a quick way to add it to another. Navigate to the Sensor List page that has the desired sensor. Logging in is not necessary. From the dropdown menu, select "Export Sensor". It will download a copy of the sensor's metadata as a .csv. To add it to a different ship/istallation, log in and navigate to the Sensor List page. Click "Import" at the top of the page and upload the .csv file. Check that all the metadata filled in properly and be sure to assign it to the correct ship. 

## Getting Help

If there are issues during the onboarding process:

- Check the [troubleshooting section](HT_sensor_onboard_troubleshooting.md) for common problems
- Review the [RegEx Guide](RegExGuide.md) for help with message parsing
- Review the [API documentation](API_endpoints.md) for technical details
- Contact the CORIOLIX support team for assistance

---

**Next:** Begin with [Step 1: Create New Sensor](HT_sensor_onboard_step1.md)
