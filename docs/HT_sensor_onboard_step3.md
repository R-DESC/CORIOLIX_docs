# Step 3: Network Logger Setup

This step configures the OpenRVDAS sensor-to-network logger, which enables data transmission from your physical sensor to the CORIOLIX network infrastructure.

!!! info "Prerequisites"
    - Sensor record created in CORIOLIX (Step 1)
    - Parameters configured (Step 2)
    - Physical sensor connected to OpenRVDAS peripheral system
    - SSH access to the OpenRVDAS peripheral system
    - Administrative privileges on the logging system

## Overview

The network logger (wire2net) component:

- Reads data from the physical sensor via RS-232 serial connection
- Formats the data for network transmission
- Sends data streams to the central CORIOLIX system
- Provides local buffering and error handling

## Setup Process

### 1. Access the Peripheral System

SSH to the OpenRVDAS peripheral system where your RS-232 instrument is connected:

```bash
ssh username@peripheral-system-ip
```

Replace `username` and `peripheral-system-ip` with your actual credentials and system address.

### 2. Run the Configuration Script

Execute the cruise configuration script with the `wire2net` flag:

```bash
cd /path/to/openrvdas/scripts
./create_cruise_configuration --wire2net --sensor-id=YOUR_SENSOR_ID
```

**Parameters:**
- `--wire2net`: Enables sensor-to-network logging mode
- `--sensor-id`: Use the sensor ID created in Step 1
- Additional options may be available depending on your OpenRVDAS version

### 3. Verify Configuration

Check that the configuration was created successfully:

```bash
ls -la /path/to/openrvdas/configs/
```

Look for your sensor configuration file, typically named: `[sensor_id]_wire2net.yaml`

### 4. Restart Logging Services

Restart the logging system to activate the new configuration:

```bash
sudo systemctl restart openrvdas-logger
# or
sudo service openrvdas-logger restart
```

### 5. Verify Data Flow

Check that data is flowing from sensor to network:

```bash
# Check log files for your sensor
tail -f /var/log/openrvdas/[sensor_id]_wire2net.log

# Monitor network traffic (optional)
sudo tcpdump -i eth0 port [configured_port]
```

## Configuration Details

### Wire2Net Settings

The configuration script will create settings for:

- **Serial Port Configuration**
  - Port: `/dev/ttyUSB0` (or appropriate port)
  - Baud Rate: As specified in sensor documentation
  - Data Bits: Usually 8
  - Stop Bits: Usually 1
  - Parity: Usually None

- **Network Settings**
  - Destination IP: Central CORIOLIX system
  - Destination Port: Configured network port
  - Protocol: Usually UDP for real-time data

- **Data Processing**
  - Message parsing based on regex from Step 1
  - Timestamp addition
  - Data validation checks

### Example Configuration

```yaml
# Example wire2net configuration
sensor_id: "WIND001"
serial_config:
  port: "/dev/ttyUSB0"
  baud_rate: 9600
  data_bits: 8
  stop_bits: 1
  parity: "none"

network_config:
  destination_ip: "192.168.1.100"
  destination_port: 8001
  protocol: "udp"

data_processing:
  message_format: "[regex_from_step1]"
  timestamp_format: "%Y-%m-%d %H:%M:%S.%f"
  validation_enabled: true
```

## Monitoring and Verification

### Check System Status

```bash
# Check if the logger process is running
ps aux | grep [sensor_id]

# Check system logs
journalctl -u openrvdas-logger -f

# Monitor data throughput
netstat -an | grep [configured_port]
```

### Data Validation

Verify that data is being properly formatted and transmitted:

1. **Check raw sensor output:**
   ```bash
   screen /dev/ttyUSB0 9600
   ```

2. **Verify network transmission:**
   ```bash
   tcpdump -i eth0 -A port [configured_port]
   ```

3. **Review log files for errors:**
   ```bash
   grep -i error /var/log/openrvdas/[sensor_id]_wire2net.log
   ```

## Common Configuration Issues

### Serial Port Problems

- **Permission denied:** Add user to dialout group
  ```bash
  sudo usermod -a -G dialout $USER
  ```

- **Port not found:** Check physical connections and port assignments
  ```bash
  dmesg | grep tty
  ls -la /dev/tty*
  ```

### Network Issues

- **Connection refused:** Verify firewall settings and network connectivity
- **No data transmission:** Check IP addresses and port configurations

### Data Format Issues

- **Parsing errors:** Verify regex pattern matches actual sensor output
- **Incomplete messages:** Check for proper message termination characters

## Troubleshooting Commands

```bash
# Test serial communication
minicom -D /dev/ttyUSB0 -b 9600

# Check network connectivity
ping [destination_ip]
telnet [destination_ip] [destination_port]

# Validate configuration syntax
python -c "import yaml; print(yaml.safe_load(open('config.yaml')))"

# Monitor system resources
top
iotop
```

!!! success "Completion"
    When the network logger is successfully configured and running, data from your sensor will be transmitted to the central CORIOLIX system. You can now proceed to [Step 4: Data Logger Setup](HT_sensor_onboard_step4.md).

## Navigation

- **Previous:** [Step 2: Configure Parameters](HT_sensor_onboard_step2.md)
- **Next:** [Step 4: Data Logger Setup](HT_sensor_onboard_step4.md)
- **Overview:** [Sensor Onboarding Guide](HT_sensor_onboard.md)

## Additional Resources

- [OpenRVDAS Documentation](https://openrvdas.org/)
- [Serial Communication Troubleshooting](HT_sensor_onboard_troubleshooting.md)
- [Network Configuration Guide](HT_access.md)