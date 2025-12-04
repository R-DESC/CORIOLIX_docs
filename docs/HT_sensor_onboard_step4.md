# Step 4: Data Logger Setup

This final step establishes the central data logger that receives network data from Step 3, processes it according to your sensor configuration, and stores it in the CORIOLIX database.

!!! info "Prerequisites"
    - Sensor record created (Step 1)
    - Parameters configured (Step 2)
    - Network logger running (Step 3)
    - SSH access to central OpenRVDAS system
    - Administrative privileges on the central system

## Overview

The data logger (net2db) component:

- Receives data streams from network loggers
- Applies data processing and transformations
- Validates data quality according to configured parameters
- Stores processed data in the CORIOLIX database
- Generates real-time displays and alerts

## Setup Process

### 1. Access the Central System

SSH to the central OpenRVDAS system that manages the CORIOLIX database:

```bash
ssh username@central-system-ip
```

Replace `username` and `central-system-ip` with your actual credentials and system address.

### 2. Run the Configuration Script

Execute the cruise configuration script with the `net2db` flag:

```bash
cd /path/to/openrvdas/scripts
./create_cruise_configuration --net2db --sensor-id=YOUR_SENSOR_ID
```

**Parameters:**
- `--net2db`: Enables network-to-database logging mode
- `--sensor-id`: Use the sensor ID created in Step 1
- `--database`: Specify CORIOLIX database connection (if required)

### 3. Verify Configuration

Check that the data logger configuration was created:

```bash
ls -la /path/to/openrvdas/configs/
```

Look for your configuration file: `[sensor_id]_net2db.yaml`

### 4. Restart Central Logging

Restart the central logging system to activate the new configuration:

```bash
sudo systemctl restart openrvdas-central
# or
sudo service openrvdas-central restart
```

### 5. Verify Data Pipeline

Confirm that data is flowing through the complete pipeline:

```bash
# Check central logger logs
tail -f /var/log/openrvdas/[sensor_id]_net2db.log

# Verify database writes
mysql -u coriolix -p -e "SELECT COUNT(*) FROM sensor_data WHERE sensor_id='YOUR_SENSOR_ID'"
```

## Configuration Details

### Net2DB Settings

The configuration includes:

- **Network Input**
  - Source IP: Peripheral system IP
  - Source Port: Configured in Step 3
  - Protocol: UDP or TCP
  - Buffer settings

- **Data Processing**
  - Parameter mapping from Step 2
  - Transformation modules
  - Quality control rules
  - Unit conversions

- **Database Output**
  - Connection parameters
  - Table structures
  - Write frequency
  - Error handling

### Example Configuration

```yaml
# Example net2db configuration
sensor_id: "WIND001"

network_input:
  source_ip: "192.168.1.50"
  source_port: 8001
  protocol: "udp"
  buffer_size: 1024

data_processing:
  transformation_module: "NoTransformation"
  parameters:
    - name: "wind_speed"
      type: "float"
      units: "m/s"
      range: [0, 50]
    - name: "wind_direction"
      type: "float"
      units: "degrees"
      range: [0, 360]

database_output:
  connection_string: "mysql://coriolix:password@localhost/coriolix_db"
  table: "sensor_data"
  write_interval: 1
  batch_size: 100
```

## Data Processing Pipeline

### 1. Network Data Reception

- Listens for incoming data packets
- Validates packet integrity
- Buffers data for processing

### 2. Message Parsing

- Applies regex patterns from Step 1
- Extracts parameter values
- Handles parsing errors gracefully

### 3. Data Transformation

- Applies calibration equations
- Converts units if necessary
- Executes custom processing scripts

### 4. Quality Control

- Range validation
- Rate-of-change checks
- Spike detection
- Flag questionable data

### 5. Database Storage

- Formats data for database insertion
- Manages transactions and rollbacks
- Handles connection failures

## Monitoring and Verification

### Check System Status

```bash
# Verify central logger is running
systemctl status openrvdas-central

# Check process details
ps aux | grep [sensor_id]_net2db

# Monitor resource usage
htop
```

### Database Verification

```bash
# Connect to database
mysql -u coriolix -p coriolix_db

# Check recent data
SELECT * FROM sensor_data 
WHERE sensor_id = 'YOUR_SENSOR_ID' 
ORDER BY timestamp DESC 
LIMIT 10;

# Verify data rates
SELECT COUNT(*) as records, 
       DATE(timestamp) as date
FROM sensor_data 
WHERE sensor_id = 'YOUR_SENSOR_ID' 
GROUP BY DATE(timestamp);
```

### Real-time Data Monitoring

```bash
# Monitor incoming data
tail -f /var/log/openrvdas/[sensor_id]_net2db.log

# Watch database activity
watch -n 5 "mysql -u coriolix -p[password] -e 'SELECT COUNT(*) FROM coriolix_db.sensor_data WHERE sensor_id=\"YOUR_SENSOR_ID\"'"

# Check for errors
grep -i error /var/log/openrvdas/[sensor_id]_net2db.log
```

## Validation Steps

### 1. End-to-End Test

1. **Generate test data** from the sensor
2. **Monitor network transmission** from peripheral system
3. **Verify data reception** on central system
4. **Check database insertion** for completeness
5. **Validate data values** against expected ranges

### 2. CORIOLIX Web Interface

1. Log into the CORIOLIX web interface
2. Navigate to the **Sensors** page
3. Find your newly configured sensor
4. Verify that:
   - Sensor status shows "Active"
   - Real-time data is updating
   - Parameter values are reasonable
   - Plots are displaying correctly

### 3. Data Quality Checks

```sql
-- Check for missing data
SELECT 
  DATE(timestamp) as date,
  COUNT(*) as records,
  COUNT(*) / (24 * 60 * 60) as records_per_second
FROM sensor_data 
WHERE sensor_id = 'YOUR_SENSOR_ID'
GROUP BY DATE(timestamp);

-- Check for out-of-range values
SELECT parameter_name, MIN(value), MAX(value), AVG(value)
FROM sensor_data 
WHERE sensor_id = 'YOUR_SENSOR_ID'
  AND timestamp > NOW() - INTERVAL 1 DAY
GROUP BY parameter_name;
```

## Common Issues and Solutions

### Data Not Appearing in Database

1. **Check network connectivity:**
   ```bash
   netstat -an | grep [configured_port]
   ```

2. **Verify configuration:**
   ```bash
   cat /path/to/configs/[sensor_id]_net2db.yaml
   ```

3. **Check database permissions:**
   ```bash
   mysql -u coriolix -p -e "SHOW GRANTS;"
   ```

### Performance Issues

1. **Monitor system resources:**
   ```bash
   iostat -x 1
   free -m
   ```

2. **Optimize database settings:**
   - Increase buffer pool size
   - Optimize table indices
   - Consider data partitioning

### Data Quality Problems

1. **Review transformation modules**
2. **Check parameter configurations**
3. **Validate regex parsing patterns**
4. **Verify sensor calibration**

!!! success "Completion"
    Congratulations! Your sensor is now fully integrated into CORIOLIX. Data should be flowing from the physical sensor through the network logger to the central database and appearing in the web interface.

## Post-Setup Tasks

### 1. Documentation

- Update vessel sensor inventory
- Document any custom configurations
- Record calibration information
- Create operator procedures

### 2. Monitoring Setup

- Configure alerts for data gaps
- Set up automated quality control reports
- Establish maintenance schedules
- Create backup procedures

### 3. User Training

- Train operators on sensor monitoring
- Document troubleshooting procedures
- Establish contact procedures for issues
- Create user guides for data access

## Navigation

- **Previous:** [Step 3: Network Logger Setup](HT_sensor_onboard_step3.md)
- **Overview:** [Sensor Onboarding Guide](HT_sensor_onboard.md)
- **Next Steps:** [Data Management](PAGES_data.md) | [System Monitoring](PAGES_status.md)

## Support Resources

- [CORIOLIX User Interface Guide](PAGES_sensors.md)
- [Data Access Documentation](HT_data_access.md)
- [Troubleshooting Guide](HT_sensor_onboard_troubleshooting.md)
- [API Documentation](API_endpoints.md)