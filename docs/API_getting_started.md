# Getting Started with the CORIOLIX API

This guide introduces scientists and researchers to the CORIOLIX REST API, enabling programmatic access to vessel sensor data for custom analysis, visualization, and integration workflows.

!!! info "Who This Guide Is For"
    This documentation is designed for researchers with programming experience in Python, R, MATLAB, or similar scientific computing environments who want to access CORIOLIX data programmatically.

## What is a REST API?

A REST API (Representational State Transfer Application Programming Interface) is a web-based interface that allows programs to request and receive data over HTTP/HTTPS, the same protocol used by web browsers.

**Key Concepts:**

- **Endpoints**: URLs that represent specific data resources (e.g., `/api/sensor/` for sensor information)
- **HTTP Methods**: Actions you can perform (GET to retrieve data, POST to create, PUT to update, DELETE to remove)
- **Parameters**: Options you include in requests to filter or specify what data you want
- **Response Formats**: Data returned in JSON (structured text) or CSV (tabular) format

## Why Use the API?

The CORIOLIX web interface is great for interactive work, but the API enables:

- **Automated Data Retrieval**: Scheduled scripts to download data regularly
- **Custom Analysis**: Process data with your preferred scientific libraries (pandas, numpy, scipy, etc.)
- **Integration**: Incorporate CORIOLIX data into your existing workflows and tools
- **Bulk Operations**: Efficiently retrieve large datasets
- **Reproducible Research**: Script your data access for repeatable analyses

## Prerequisites

### Required Knowledge

- Basic programming skills in Python, R, or similar language
- Understanding of HTTP requests (or willingness to learn)
- Familiarity with command-line interfaces (optional but helpful)

### Software Requirements

**For Python users:**
```bash
pip install requests pandas
```

**For R users:**
```R
install.packages(c("httr", "jsonlite", "dplyr"))
```

### Authentication

Most CORIOLIX API endpoints require authentication. You'll need:

- A valid CORIOLIX account
- Your username and password for basic authentication
- OR an API token (in development and coming soon)

!!! warning "Security Note"
    Never hard-code passwords in your scripts. Use environment variables or configuration files that are not committed to version control.

## Your First API Request

### Understanding the Base URL

All CORIOLIX API endpoints start with:
```
https://your-vessel.coriolix.org/api/
```

Replace `your-vessel.coriolix.org` with your actual CORIOLIX instance URL.

### Example 1: Listing Available Sensors (Python)

```python
import requests
from requests.auth import HTTPBasicAuth

# Configuration
BASE_URL = "https://your-vessel.coriolix.org/api"
USERNAME = "your_username"
PASSWORD = "your_password"

# Make the request
response = requests.get(
    f"{BASE_URL}/sensor/",
    auth=HTTPBasicAuth(USERNAME, PASSWORD),
    params={"format": "json"}
)

# Check if successful
if response.status_code == 200:
    sensors = response.json()
    print(f"Found {len(sensors)} sensors")
    
    # Print first sensor details
    if sensors:
        sensor = sensors[0]
        print(f"Sensor ID: {sensor['sensor_id']}")
        print(f"Name: {sensor['sensor_name']}")
        print(f"Type: {sensor['sensor_type']}")
else:
    print(f"Error: {response.status_code}")
    print(response.text)
```

### Example 2: Listing Available Sensors (R)

```R
library(httr)
library(jsonlite)

# Configuration
BASE_URL <- "https://your-vessel.coriolix.org/api"
USERNAME <- "your_username"
PASSWORD <- "your_password"

# Make the request
response <- GET(
  paste0(BASE_URL, "/sensor/"),
  authenticate(USERNAME, PASSWORD),
  query = list(format = "json")
)

# Check if successful
if (status_code(response) == 200) {
  sensors <- content(response, as = "parsed")
  cat(sprintf("Found %d sensors\n", length(sensors)))
  
  # Print first sensor details
  if (length(sensors) > 0) {
    sensor <- sensors[[1]]
    cat(sprintf("Sensor ID: %s\n", sensor$sensor_id))
    cat(sprintf("Name: %s\n", sensor$sensor_name))
    cat(sprintf("Type: %s\n", sensor$sensor_type))
  }
} else {
  cat(sprintf("Error: %d\n", status_code(response)))
  print(content(response, as = "text"))
}
```

### Example 3: Using curl (Command Line)

```bash
curl -X GET "https://your-vessel.coriolix.org/api/sensor/?format=json" \
  -u "your_username:your_password" \
  | jq '.[0]'  # Use jq to pretty-print the first sensor
```

## Understanding Response Formats

### JSON Format

JSON (JavaScript Object Notation) is ideal for programmatic access:

```json
{
  "sensor_id": "seatem381110",
  "sensor_name": "Forward Intake Thermometer",
  "sensor_type": "Thermometer, Water",
  "sensor_class": "Flowthrough",
  "parameters": [
    {
      "short_name": "Temperature",
      "long_name": "Temperature",
      "units_abbrev": "degrees_C"
    }
  ]
}
```

**Benefits:**
- Hierarchical structure
- Easy to parse in all programming languages
- Preserves data types (numbers, strings, booleans)

### CSV Format

CSV (Comma-Separated Values) is ideal for tabular data:

```csv
timestamp,sensor_id,temperature,salinity
2025-12-03T10:00:00Z,seatsg450712,15.2,34.5
2025-12-03T10:01:00Z,seatsg450712,15.3,34.6
```

**Benefits:**
- Simple tabular format
- Direct import into spreadsheets
- Easy to use with pandas/R dataframes

**Requesting CSV:**
```python
response = requests.get(
    f"{BASE_URL}/data/binned/",
    auth=HTTPBasicAuth(USERNAME, PASSWORD),
    params={"format": "csv", "sensors": "TSG001"}
)

# Save to file
with open("data.csv", "w") as f:
    f.write(response.text)
```

## Common Query Parameters

Most API endpoints support these parameters:

| Parameter | Purpose | Example |
|-----------|---------|---------|
| `format` | Response format | `?format=json` or `?format=csv` |
| `search` | Text search | `?search=temperature` |
| `ordering` | Sort results | `?ordering=sensor_name` |
| `date_after` | Filter by start time | `?date_after=2025-12-01T00:00:00Z` |
| `date_before` | Filter by end time | `?date_before=2025-12-03T23:59:59Z` |

## Working with Timestamps

CORIOLIX uses ISO 8601 format for timestamps:

```
2025-12-03T14:30:00Z
```

Where:
- `2025-12-03` is the date (YYYY-MM-DD)
- `T` separates date and time
- `14:30:00` is the time (HH:MM:SS)
- `Z` indicates UTC timezone

**Python Example:**
```python
from datetime import datetime, timedelta

# Create timestamp for 24 hours ago
yesterday = datetime.utcnow() - timedelta(days=1)
timestamp = yesterday.strftime("%Y-%m-%dT%H:%M:%SZ")

# Use in API request
params = {
    "format": "json",
    "date_after": timestamp
}
```

**R Example:**
```R
library(lubridate)

# Create timestamp for 24 hours ago
yesterday <- now(tzone = "UTC") - days(1)
timestamp <- format(yesterday, "%Y-%m-%dT%H:%M:%SZ")

# Use in API request
params <- list(
  format = "json",
  date_after = timestamp
)
```

## Error Handling

Always check response status codes:

| Code | Meaning | Action |
|------|---------|--------|
| 200 | Success | Process the data |
| 400 | Bad Request | Check your parameters |
| 401 | Unauthorized | Verify your credentials |
| 404 | Not Found | Check the endpoint URL |
| 500 | Server Error | Contact support if persistent |

**Python Example:**
```python
response = requests.get(url, auth=auth, params=params)

if response.status_code == 200:
    data = response.json()
    # Process data
elif response.status_code == 401:
    print("Authentication failed. Check your credentials.")
elif response.status_code == 404:
    print("Resource not found. Check the endpoint URL.")
else:
    print(f"Error {response.status_code}: {response.text}")
```

## Best Practices

### 1. Use Environment Variables for Credentials

**Python:**
```python
import os

USERNAME = os.environ.get("CORIOLIX_USERNAME")
PASSWORD = os.environ.get("CORIOLIX_PASSWORD")
```

Set in your terminal:
```bash
export CORIOLIX_USERNAME="your_username"
export CORIOLIX_PASSWORD="your_password"
```

**R:**
```R
USERNAME <- Sys.getenv("CORIOLIX_USERNAME")
PASSWORD <- Sys.getenv("CORIOLIX_PASSWORD")
```

### 2. Request Only What You Need

Use query parameters to filter data at the source:

```python
# Good: Request specific sensor and time range
params = {
    "sensors": "TSG001",
    "date_after": "2025-12-01T00:00:00Z",
    "date_before": "2025-12-02T00:00:00Z",
    "format": "csv"
}

# Avoid: Downloading everything and filtering locally
```

### 3. Handle Rate Limits Gracefully

If you're making many requests, add delays:

```python
import time

for sensor_id in sensor_list:
    response = requests.get(f"{BASE_URL}/sensor/{sensor_id}/", auth=auth)
    # Process response
    time.sleep(0.5)  # Half-second delay between requests
```

### 4. Cache Results When Appropriate

```python
import pickle
from pathlib import Path

def get_sensor_list(use_cache=True):
    cache_file = Path("sensor_cache.pkl")
    
    # Use cached data if available and recent
    if use_cache and cache_file.exists():
        cache_age = time.time() - cache_file.stat().st_mtime
        if cache_age < 3600:  # Cache valid for 1 hour
            with open(cache_file, "rb") as f:
                return pickle.load(f)
    
    # Fetch fresh data
    response = requests.get(f"{BASE_URL}/sensor/", auth=auth, params={"format": "json"})
    sensors = response.json()
    
    # Update cache
    with open(cache_file, "wb") as f:
        pickle.dump(sensors, f)
    
    return sensors
```

## Next Steps

Now that you understand the basics, explore specific use cases:

- **[API Use Cases](API_use_cases.md)** - Common workflows and examples
- **[Data Model](API_model.md)** - Understanding CORIOLIX data structures
- **[Endpoint Reference](API_endpoints.md)** - Complete API documentation

## Quick Reference

### Python Template

```python
import requests
from requests.auth import HTTPBasicAuth
import pandas as pd
from datetime import datetime, timedelta

# Configuration
BASE_URL = "https://your-vessel.coriolix.org/api"
USERNAME = "your_username"
PASSWORD = "your_password"
auth = HTTPBasicAuth(USERNAME, PASSWORD)

# Get data from the last 24 hours
yesterday = datetime.utcnow() - timedelta(days=1)
params = {
    "format": "csv",
    "date_after": yesterday.strftime("%Y-%m-%dT%H:%M:%SZ"),
    "sensors": "YOUR_SENSOR_ID"
}

response = requests.get(f"{BASE_URL}/data/binned/", auth=auth, params=params)

if response.status_code == 200:
    # Load into pandas DataFrame
    from io import StringIO
    df = pd.read_csv(StringIO(response.text))
    print(df.head())
else:
    print(f"Error: {response.status_code}")
```

### R Template

```R
library(httr)
library(dplyr)
library(lubridate)
library(readr)

# Configuration
BASE_URL <- "https://your-vessel.coriolix.org/api"
USERNAME <- "your_username"
PASSWORD <- "your_password"

# Get data from the last 24 hours
yesterday <- now(tzone = "UTC") - days(1)
params <- list(
  format = "csv",
  date_after = format(yesterday, "%Y-%m-%dT%H:%M:%SZ"),
  sensors = "YOUR_SENSOR_ID"
)

response <- GET(
  paste0(BASE_URL, "/data/binned/"),
  authenticate(USERNAME, PASSWORD),
  query = params
)

if (status_code(response) == 200) {
  # Load into data frame
  df <- read_csv(content(response, as = "text"))
  print(head(df))
} else {
  cat(sprintf("Error: %d\n", status_code(response)))
}
```

## Getting Help

- **API Issues**: Check the [troubleshooting section](HT_sensor_onboard_troubleshooting.md)
- **Code Examples**: See [API Use Cases](API_use_cases.md)
- **Technical Questions**: Contact CORIOLIX support
- **Documentation Feedback**: Open an issue on [GitHub](https://github.com/R-DESC/CORIOLIX_docs)