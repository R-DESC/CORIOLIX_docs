# Sensor Onboarding Troubleshooting Guide

This guide provides solutions for common issues encountered during the sensor onboarding process in CORIOLIX.

## General Troubleshooting Approach

When troubleshooting sensor onboarding issues:

1. **Identify the affected step** - Determine which stage is failing
2. **Check system logs** - Review relevant log files for error messages
3. **Verify prerequisites** - Ensure previous steps completed successfully
4. **Test components individually** - Isolate the problem area
5. **Document solutions** - Record successful fixes for future reference

## Step 1 Issues: Sensor Creation

### Problem: Sensor ID Already Exists

**Symptoms:**
- Error message: "Sensor ID already in use"
- Cannot save new sensor record

**Solutions:**
1. Check existing sensors for duplicates:
   ```sql
   SELECT sensor_id, sensor_name FROM sensors WHERE sensor_id = 'SENSOR_ID';
   ```
2. Modify the sensor ID to be unique
3. Follow the naming convention: 6 alphanumeric + last 6 digits of serial

### Problem: Regex Parsing Failures

**Symptoms:**
- "Verify Format" button shows errors
- Parsed values don't match expected results
- Sample data doesn't generate expected parameters

**Solutions:**

1. **Common Regex Issues:**

   | Issue | Problem | Solution |
   |-------|---------|----------|
   | Missing escapes | Special chars not escaped | Use `\\.` for literal dots |
   | Wrong quantifiers | `+` vs `*` vs `?` | Use `+` for one-or-more |
   | Case sensitivity | Uppercase/lowercase mismatch | Use `[Bb]` for both cases |
   | Whitespace | Spaces in data | Use `\\s*` for optional spaces |

2. **Test with simple patterns first:**
   ```regex
   # Start simple
   ['^(?P<first_value>\\w+)']
   
   # Add complexity gradually
   ['^(?P<first_value>\\w+),\\s*(?P<second_value>\\d+\\.\\d+)']
   ```

3. **Use online regex testers:**
   - Test patterns with actual sensor data
   - Verify named capture groups work correctly

### Problem: Required Field Validation

**Symptoms:**
- Form won't submit
- Missing field error messages

**Solutions:**
- Review all required fields (marked with *)
- Ensure dropdown selections are made
- Verify numeric fields have valid numbers
- Check that text fields aren't empty

## Step 2 Issues: Parameter Configuration

### Problem: Processing Symbol Errors

**Symptoms:**
- Data transformation failures
- Error: "Unknown processing symbol"
- Parameters not appearing in processed data

**Solutions:**

1. **For NoTransformation sensors:**
   - Leave Processing Symbol field blank
   - Ensure transformation module is set to "NoTransformation"

2. **For custom transformations:**
   - Consult transformation module documentation
   - Verify symbol names match exactly (case-sensitive)
   - Check for typos in symbol names

### Problem: Invalid Data Ranges

**Symptoms:**
- Quality control flags all data as invalid
- Warning: "Value outside expected range"

**Solutions:**
- Review sensor specifications for realistic ranges
- Consider environmental conditions at deployment location
- Set ranges 10-20% beyond normal operating limits
- Use conservative ranges initially, then refine based on real data

## Step 3 Issues: Network Logger

### Problem: Serial Port Access Denied

**Symptoms:**
- Error: "Permission denied /dev/ttyUSB0"
- Cannot read from serial port

**Solutions:**
1. **Add user to dialout group:**
   ```bash
   sudo usermod -a -G dialout $USER
   logout  # Log out and back in
   ```

2. **Check port ownership:**
   ```bash
   ls -la /dev/ttyUSB*
   ```

3. **Set proper permissions:**
   ```bash
   sudo chmod 666 /dev/ttyUSB0
   ```

### Problem: No Serial Data Received

**Symptoms:**
- Empty log files
- No data in network transmission
- Timeout errors

**Solutions:**

1. **Verify physical connections:**
   - Check RS-232 cable connections
   - Verify correct pin wiring (TX/RX, ground)
   - Test with different cable if available

2. **Check serial settings:**
   ```bash
   # Test with screen or minicom
   screen /dev/ttyUSB0 9600
   
   # Check for data flow
   cat /dev/ttyUSB0
   ```

3. **Verify sensor configuration:**
   - Confirm sensor is powered on
   - Check sensor output is enabled
   - Verify baud rate, data bits, stop bits, parity

### Problem: Network Transmission Failures

**Symptoms:**
- Local data visible but not transmitted
- Connection refused errors
- Network timeout errors

**Solutions:**

1. **Check network connectivity:**
   ```bash
   ping [destination_ip]
   telnet [destination_ip] [port]
   ```

2. **Verify firewall settings:**
   ```bash
   sudo iptables -L
   sudo ufw status
   ```

3. **Check port availability:**
   ```bash
   netstat -an | grep [port]
   lsof -i :[port]
   ```

## Step 4 Issues: Data Logger

### Problem: Database Connection Failures

**Symptoms:**
- Error: "Can't connect to MySQL server"
- Authentication failures
- Connection timeout errors

**Solutions:**

1. **Check database service:**
   ```bash
   sudo systemctl status mysql
   sudo service mysql status
   ```

2. **Verify credentials:**
   ```bash
   mysql -u coriolix -p -h [host] [database]
   ```

3. **Check database permissions:**
   ```sql
   SHOW GRANTS FOR 'coriolix'@'%';
   ```

### Problem: No Data in Database

**Symptoms:**
- Network logger shows data transmission
- Database queries return no results
- No errors in logs

**Solutions:**

1. **Check table structure:**
   ```sql
   DESCRIBE sensor_data;
   SHOW TABLES LIKE '%sensor%';
   ```

2. **Verify data format:**
   - Check timestamp formats
   - Verify sensor_id matches exactly
   - Confirm parameter names are correct

3. **Monitor real-time insertion:**
   ```bash
   tail -f /var/log/mysql/general.log
   ```

### Problem: Data Quality Issues

**Symptoms:**
- Data values seem incorrect
- All parameters showing same value
- Inconsistent parameter parsing

**Solutions:**

1. **Verify regex parsing:**
   - Test message format string with actual data
   - Check for parameter order mismatches
   - Validate named capture groups

2. **Check transformation modules:**
   - Review calibration equations
   - Verify unit conversions
   - Test with known input values

## System-Wide Issues

### Problem: High System Load

**Symptoms:**
- Slow response times
- Process timeouts
- System becoming unresponsive

**Solutions:**

1. **Monitor system resources:**
   ```bash
   top
   iotop
   free -h
   df -h
   ```

2. **Check log file sizes:**
   ```bash
   du -sh /var/log/*
   # Rotate or clean old logs if needed
   ```

3. **Optimize configurations:**
   - Reduce logging verbosity
   - Increase buffer sizes
   - Consider data sampling rates

### Problem: Time Synchronization Issues

**Symptoms:**
- Timestamps don't match reality
- Data appears in wrong time slots
- Synchronization errors

**Solutions:**

1. **Check system time:**
   ```bash
   date
   ntpstat
   chrony sources -v
   ```

2. **Synchronize clocks:**
   ```bash
   sudo ntpdate -s time.nist.gov
   sudo systemctl restart ntp
   ```

3. **Configure automatic sync:**
   ```bash
   sudo timedatectl set-ntp true
   ```

## Diagnostic Commands

### Quick System Check

```bash
#!/bin/bash
# Quick CORIOLIX diagnostic script

echo "=== System Status ==="
uptime
free -h
df -h

echo "=== OpenRVDAS Services ==="
systemctl status openrvdas-logger
systemctl status openrvdas-central

echo "=== Database Status ==="
systemctl status mysql
mysql -u coriolix -p[password] -e "SELECT COUNT(*) FROM coriolix_db.sensors;"

echo "=== Network Connectivity ==="
ping -c 3 [central_system_ip]

echo "=== Recent Logs ==="
tail -20 /var/log/openrvdas/*.log
```

### Data Flow Verification

```bash
# Check complete data pipeline
echo "1. Serial data:"
timeout 5 cat /dev/ttyUSB0

echo "2. Network transmission:"
sudo tcpdump -i eth0 -c 5 port [configured_port]

echo "3. Database records:"
mysql -u coriolix -p -e "SELECT COUNT(*) FROM sensor_data WHERE timestamp > NOW() - INTERVAL 5 MINUTE;"
```

## Getting Additional Help

### Log File Locations

- **OpenRVDAS logs:** `/var/log/openrvdas/`
- **System logs:** `/var/log/syslog`
- **MySQL logs:** `/var/log/mysql/`
- **Application logs:** `/var/log/coriolix/`

### Support Channels

1. **Check documentation:**
   - [CORIOLIX User Guide](index.md)
   - [API Documentation](API_endpoints.md)
   - [System Administration](INSTALL_coriolix.md)

2. **Contact support:**
   - Create issue in GitHub repository
   - Email CORIOLIX support team
   - Check community forums

### Escalation Process

When issues persist:

1. **Gather diagnostic information:**
   - System specifications
   - Error messages
   - Log file excerpts
   - Configuration files
   - Timeline of events

2. **Document troubleshooting steps:**
   - What was tried
   - Results observed
   - Current system state

3. **Prepare for support contact:**
   - Sensor specifications
   - Network topology
   - Custom configurations
   - Business impact

## Prevention

### Best Practices

- **Regular backups** of configurations
- **Monitor system resources** proactively
- **Test changes** in development environment first
- **Document** all customizations
- **Keep systems updated** with security patches
- **Regular maintenance** schedules

### Monitoring Setup

```bash
# Set up automated monitoring
crontab -e

# Add monitoring jobs
*/5 * * * * /scripts/check_sensor_data.sh
0 */6 * * * /scripts/system_health_check.sh
0 2 * * * /scripts/log_rotation.sh
```

This comprehensive troubleshooting guide should help resolve most issues encountered during sensor onboarding. Remember to document any new solutions for future reference.
