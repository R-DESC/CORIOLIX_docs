# API Endpoints Reference

This page provides a curated guide to the most commonly used CORIOLIX API endpoints, organized by use case and workflow.

>[!TIP]
>Interactive API Browser
>
>For the complete, interactive API documentation with all parameters and the ability to test requests directly in a browser, visit the CORIOLIX instance's API browser at:
>**`https://example-vessel.coriolix.org/api/`**
>
>The DRF (Django REST Framework) browser provides a full reference with request/response examples.

## Quick Reference

### Most Common Endpoints

| Endpoint | Purpose | Common Use |
|----------|---------|------------|
| `/api/sensor/` | List all sensors | Discover available instruments |
| `/api/chart_metadata/` | Sensor-parameter relationships | Setup plotting/visualization |
| `/api/data/binned/` | Time-averaged data | Retrieve data for analysis |
| `/api/data/fullres/{sensor}/{param}/` | Raw sensor data | High-resolution time series |
| `/api/parameter/` | Parameter details | Understand measurements |
| `/api/cruise/` | Cruise information | Expedition metadata |
| `/api/events/` | Event log | Scientific activities |
| `/api/vessel/` | Vessel information | Ship specifications |

## Endpoints by Workflow

### 1. Discovery: Finding Available Data

Start here to explore what data is available in the system.

#### List All Sensors
```
GET /api/sensor/
```

**Purpose:** Get data from all sensors installed on the vessel.

**Common Parameters:**
- `sensor_class` - Filter by category (Navigation, Atmospheric, Flowthrough, etc.)
- `sensor_type` - Filter by measurement type (Temperature, Salinity, etc.)
- `format` - Response format (`json` or `csv`)

**Example:**
```python
# Get all temperature sensors
response = requests.get(
    f"{BASE_URL}/sensor/",
    auth=auth,
    params={"sensor_type": "Temperature", "format": "json"}
)
```

#### Get Chart Metadata
```
GET /api/chart_metadata/
GET /api/chart_metadata/{sensor_id}/
```

**Purpose:** See which parameters each sensor measures (essential for plotting)

**Returns:** Sensor info with nested parameter details including units, ranges, and display settings

**Example:**
```python
# Get all sensors with their parameters
response = requests.get(
    f"{BASE_URL}/chart_metadata/",
    auth=auth,
    params={"sensor_class": "Flowthrough", "format": "json"}
)
```

#### List Parameters
```
GET /api/parameter/
```

**Purpose:** Get detailed information about measurable parameters

**Common Filters:**
- `sensor_id` - Parameters for specific sensor
- `short_name` - Search by parameter name

### 2. Data Retrieval: Getting Measurements

The core functionality for retrieving sensor observations.

#### Binned Data (Recommended for Most Uses)
```
GET /api/data/binned/
```

**Purpose:** Retrieve time-averaged data from multiple sensors

**Key Parameters:**
- `sensors` - Comma-separated sensor IDs (e.g., `"TSG001,SEAPTH000000"`)
- `parameters` - Comma-separated parameter names (e.g., `"Temperature,Salinity"`)
- `parameters_like` - Partial match (e.g., `"temp"` matches Temperature, temp_air, etc.)
- `sensor_like` - Partial sensor match
- `classes` - Sensor classes (e.g., `"Atmospheric,Flowthrough"`)
- `date_after` - Start time (ISO 8601: `"2025-12-01T00:00:00Z"`)
- `date_before` - End time
- `bin_interval` - Averaging interval in seconds (default: 60)
- `format` - `json` or `csv`
- `geo` - Set to `"true"` to include lat/lon
- `last_item` - Set to any value to get only the most recent record

**Example:**
```python
# Get 24 hours of temperature and salinity data
from datetime import datetime, timedelta

yesterday = datetime.utcnow() - timedelta(days=1)
response = requests.get(
    f"{BASE_URL}/data/binned/",
    auth=auth,
    params={
        "parameters": "Temperature,Salinity",
        "date_after": yesterday.strftime("%Y-%m-%dT%H:%M:%SZ"),
        "bin_interval": 300,  # 5-minute bins
        "format": "csv"
    }
)
```

#### Full-Resolution Data
```
GET /api/data/fullres/{sensor_id}/{parameter}/
```

**Purpose:** Get raw, unaveraged sensor data

**Use When:** Maximum time resolution is needed or to visualize individual measurements

**Parameters:**
- `date_after` - Start time
- `date_before` - End time
- `format` - `json` or `csv`

**Example:**
```python
# Get raw SST data
response = requests.get(
    f"{BASE_URL}/data/fullres/SEAPTH000000/Temperature/",
    auth=auth,
    params={
        "date_after": "2025-12-09T00:00:00Z",
        "date_before": "2025-12-09T01:00:00Z",
        "format": "csv"
    }
)
```

#### Data Statistics
```
GET /api/data/binstats/{sensor_id}/{parameter}/
```

**Purpose:** Get statistical summaries (min, max, mean, std) for a parameter

**Use When:** You want quick overview statistics without retrieving all data points

#### Count Available Data
```
GET /api/data/countdata/
```

**Purpose:** Count how many data records exist without retrieving them

**Use When:** Checking data availability before downloading large datasets

### 3. Metadata: Context and Configuration

Get information about sensors, calibrations, and system configuration.

#### Sensor Details
```
GET /api/sensor/{sensor_id}/
```

**Purpose:** Get complete information about a specific sensor

**Returns:** All sensor metadata including communication settings, operating limits, calibration info

#### Parameter Details
```
GET /api/parameter/{parameter_id}/
```

**Purpose:** Detailed parameter information

**Returns:** Units, data ranges, precision, processing settings, display configuration

#### Last Messages
```
GET /api/last_msg/
GET /api/last_msg/{sensor_id}/
```

**Purpose:** See the most recent raw message received from sensors

**Use When:** Debugging sensor communication or checking if data is flowing

#### Last Observations
```
GET /api/last_obs/
GET /api/last_obs/{sensor_id}/
```

**Purpose:** Get the most recent processed observation from each sensor

**Use When:** Building status dashboards or checking current conditions

### 4. Cruise Management: Expeditions and Events

Track scientific activities and cruise operations.

#### List Cruises
```
GET /api/cruise/
```

**Purpose:** Get information about research cruises

**Common Filters:**
- `start_date_after` - Cruises starting after date
- `end_date_before` - Cruises ending before date
- `chief_scientist` - Filter by PI

#### Cruise Details
```
GET /api/cruise/{cruise_id}/
```

**Purpose:** Complete information about a specific cruise

#### Event Log
```
GET /api/events/
```

**Purpose:** Retrieve scientific events and activities

**Common Filters:**
- `event_type` - Type of event
- `event_time_after` - Events after time
- `cruise_id` - Events for specific cruise

**Example:**
```python
# Get all CTD casts from a cruise
response = requests.get(
    f"{BASE_URL}/events/",
    auth=auth,
    params={
        "cruise_id": "TAANI-2025-001",
        "event_type": "CTD",
        "format": "json"
    }
)
```

#### Stations
```
GET /api/station/
```

**Purpose:** Sampling station locations and metadata

### 5. System Information

Get information about vessels, equipment, and configuration.

#### Vessel Information
```
GET /api/vessel/
GET /api/vessel/{vessel_uuid}/
```

**Purpose:** Ship specifications and configuration

#### Sensor Logs
```
GET /api/sensor_log/
```

**Purpose:** Maintenance and operational logs for sensors

**Common Filters:**
- `sensor_id` - Logs for specific sensor
- `log_date_after` - Recent logs

#### Calibration Records
```
GET /api/sensor_midcal/
```

**Purpose:** Sensor calibration history

#### Alerts
```
GET /api/alert_config/
GET /api/alert_current/
GET /api/alert_archive/
```

**Purpose:** System alerts and notifications about data quality, sensor status, etc.

## Common Workflows

### Workflow 1: Download Daily Temperature Data

```python
from datetime import datetime, timedelta
import requests
from requests.auth import HTTPBasicAuth
import pandas as pd
from io import StringIO

BASE_URL = "https://your-vessel.coriolix.org/api"
auth = HTTPBasicAuth("username", "password")

# Get yesterday's data
yesterday = datetime.utcnow() - timedelta(days=1)
today = datetime.utcnow()

response = requests.get(
    f"{BASE_URL}/data/binned/",
    auth=auth,
    params={
        "parameters": "Temperature",
        "date_after": yesterday.strftime("%Y-%m-%dT%H:%M:%SZ"),
        "date_before": today.strftime("%Y-%m-%dT%H:%M:%SZ"),
        "format": "csv"
    }
)

# Load into pandas
df = pd.read_csv(StringIO(response.text), parse_dates=['timestamp'])
print(df.describe())
```

### Workflow 2: Create Sensor Inventory

```python
# Step 1: Get all sensors
sensors = requests.get(
    f"{BASE_URL}/sensor/",
    auth=auth,
    params={"format": "json"}
).json()

# Step 2: Get parameters for each sensor
inventory = []
for sensor in sensors:
    metadata = requests.get(
        f"{BASE_URL}/chart_metadata/{sensor['sensor_id']}/",
        auth=auth,
        params={"format": "json"}
    ).json()
    
    inventory.append({
        "sensor_id": sensor['sensor_id'],
        "sensor_name": sensor['sensor_name'],
        "sensor_class": sensor['sensor_class'],
        "parameters": [p['short_name'] for p in metadata['parameters']]
    })

# Step 3: Display inventory
for item in inventory:
    print(f"{item['sensor_name']} ({item['sensor_class']})")
    print(f"  Measures: {', '.join(item['parameters'])}")
```

### Workflow 3: Real-Time Monitoring

```python
import time

# Get current conditions every 5 minutes
while True:
    response = requests.get(
        f"{BASE_URL}/data/binned/",
        auth=auth,
        params={
            "last_item": "true",
            "format": "json"
        }
    )
    
    latest = response.json()[0]
    print(f"Timestamp: {latest['timestamp']}")
    
    # Print all available fields
    for key, value in latest.items():
        if key != 'timestamp' and value is not None:
            print(f"  {key}: {value}")
    
    time.sleep(300)  # Wait 5 minutes
```

### Workflow 4: Export Cruise Data

```python
# Step 1: Get cruise info
cruise = requests.get(
    f"{BASE_URL}/cruise/TAANI-2025-001/",
    auth=auth,
    params={"format": "json"}
).json()

# Step 2: Get all data from cruise period
response = requests.get(
    f"{BASE_URL}/data/binned/",
    auth=auth,
    params={
        "date_after": cruise['start_date'],
        "date_before": cruise['end_date'],
        "geo": "true",
        "format": "csv"
    }
)

# Step 3: Save to file
with open(f"{cruise['cruise_name']}_data.csv", "w") as f:
    f.write(response.text)

# Step 4: Get event log
events = requests.get(
    f"{BASE_URL}/events/",
    auth=auth,
    params={
        "cruise_id": cruise['cruise_id'],
        "format": "csv"
    }
)

with open(f"{cruise['cruise_name']}_events.csv", "w") as f:
    f.write(events.text)
```

## Query Parameter Guide

### Common Parameters (Most Endpoints)

| Parameter | Values | Purpose |
|-----------|--------|---------|
| `format` | `json`, `csv` | Response format |
| `ordering` | field name | Sort results (prefix with `-` for descending) |
| `search` | text | Text search across fields |

### Time Filtering (Data Endpoints)

| Parameter | Format | Example |
|-----------|--------|---------|
| `date_after` | ISO 8601 | `2025-12-01T00:00:00Z` |
| `date_before` | ISO 8601 | `2025-12-09T23:59:59Z` |

### Data Selection (Binned Data)

| Parameter | Format | Example |
|-----------|--------|---------|
| `sensors` | Comma-separated IDs | `TSG001,SEAPTH000000` |
| `sensors_like` | Partial match | `TSG` |
| `parameters` | Comma-separated names | `Temperature,Salinity` |
| `parameters_like` | Partial match | `temp` |
| `classes` | Comma-separated classes | `Atmospheric,Flowthrough` |
| `fields` | Exact field names | `timestamp,tsg001_sst` |

## Response Formats

### JSON Response
```json
[
  {
    "sensor_id": "TSG001",
    "sensor_name": "Thermosalinograph",
    "sensor_class": "Flowthrough",
    "parameters": [
      {
        "short_name": "SST",
        "long_name": "Sea Surface Temperature",
        "units_abbrev": "degC"
      }
    ]
  }
]
```

### CSV Response
```csv
sensor_id,sensor_name,sensor_class
TSG001,Thermosalinograph,Flowthrough
SEAPTH000000,SBE 38 Thermometer,Flowthrough
```

## Authentication

All endpoints require authentication. Use HTTP Basic Authentication:

```python
from requests.auth import HTTPBasicAuth

auth = HTTPBasicAuth("your_username", "your_password")
response = requests.get(url, auth=auth)
```

## Rate Limiting

Be respectful of the API:
- Add delays between requests in loops
- Use binned data instead of full-resolution when possible
- Request only the time ranges and sensors you need
- Cache results when appropriate

## Error Responses

| Status Code | Meaning | Action |
|-------------|---------|--------|
| 200 | Success | Process the data |
| 400 | Bad Request | Check parameter syntax |
| 401 | Unauthorized | Verify credentials |
| 404 | Not Found | Check endpoint URL and resource ID |
| 500 | Server Error | Contact support if persistent |

## Interactive API Browser

For the most up-to-date endpoint documentation with:
- Complete parameter lists
- Request/response examples
- Interactive testing
- Filtering options

Visit your CORIOLIX instance's API browser:

**`https://your-vessel.coriolix.org/api/`**

The DRF interface allows you to:
1. Browse all available endpoints
2. See detailed parameter documentation
3. Test requests directly in your browser
4. View response schemas
5. Download example responses

## Additional Resources

- **[Getting Started Guide](API_getting_started.md)** - Learn API basics
- **[Data Model](API_model.md)** - Understand data relationships
- **[Use Cases](API_use_cases.md)** - More practical examples

## Support

For API questions or issues:
- Check the interactive API browser for parameter details
- Review example code in the [Getting Started](API_getting_started.md) guide
- Contact CORIOLIX support for technical assistance
- Report documentation issues on [GitHub](https://github.com/R-DESC/CORIOLIX_docs)
