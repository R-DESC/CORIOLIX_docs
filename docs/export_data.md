# Exporting Data

CORIOLIX provides flexible options for downloading oceanographic data in multiple formats and temporal resolutions. The data system uses ERDDAP to deliver binned summaries, high-resolution time series, and native sensor archives suited to different research and operational needs.

## Data Download Options

CORIOLIX offers three types of data downloads:

### 1a. Binned Data (Basic)

**Best for:** Quick time-series summaries with minimal file size

Binned data are provided in temporal bins (default: 60-second intervals) as standard CSV tables via ERDDAP `tabledap`. Basic binned exports include only the mean value and spot value (the single observation closest to the bin center) for each parameter.

**CSV Structure:**

Basic binned data files contain the following columns:

- `time` — ISO 8601 timestamp (UTC) for the bin center
- `latitude` — latitude at bin time
- `longitude` — longitude at bin time
- `point` — point identifier or index
- `<parameter_name>` — mean value for each selected parameter
- `<parameter_name>_spot` — single observation closest in time to bin center (for applicable parameters)

**Example row:**

```
2026-01-15T12:30:00Z, 57.2450, -152.4680, 1, 15.234, 15.256, ...
```

**Accompanying metadata file:**

Each binned export includes a metadata CSV listing all available parameters, their descriptions, units, and data types. Example metadata entry:

```
Variable Name,Data Type,Dimensions,Units,Long Name,Standard Name,Missing Value
sea_water_temperature,double,time latitude longitude,degree_C,Sea Water Temperature,sea_water_temperature,-999
salinity,double,time latitude longitude,PSU,Salinity,sea_water_salinity,-999
```

Use the metadata file to understand field definitions, units, and handling of missing values (typically `-999` or `NaN`).

**What's included in basic binned exports:**

- Time-averaged (mean) values for the bin interval
- Location (latitude, longitude) at bin center
- Spot values (single observation closest to bin time)
- Quality assessment and flags reflecting QC state at export time

---

### 1b. Binned Data with Statistics

**Best for:** Statistical analysis, understanding variability, and quality assurance

Binned data with statistics are provided as temporal bins (default: 1-minute / 60-second intervals) via ERDDAP `tabledap`. The export includes unweighted means plus a set of statistical summaries (std, min, max, count, median, and the spot value) and additional information about how bins are constructed.

Binning Method

- Data are binned using an unweighted mean over the configured time interval. Statistics captured per bin include standard deviation, minimum, maximum, median, and observation count.
- Circular data (e.g., heading, wind direction) use circular mean and circular standard deviation so that values like 355° and 5° average to 0°, not 180°.
- Vector data (e.g., true wind) are averaged by component: mean_u = mean(u), mean_v = mean(v), then

 mean_direction = rad2deg(atan2(mean_u, mean_v)) + 180
 mean_magnitude = sqrt(mean_u^2 + mean_v^2)

Binning Time Interval and Storage

- Bins are centered on "nice" minute timestamps (for example 12:03:00, 12:04:00, etc.).
- Default bin size is 1 minute; custom bin sizes may be configured (site default for custom bins is often 10 minutes).
- Data collected less frequently than the bin interval (e.g., a ceilometer every 6 minutes) are represented with NaNs for empty samples within a bin-centered series.
- Binned data are stored short-term; retention is configurable (typical site settings: 10–90 days).

Binned Data Quality

- Only individual observations with flags 0, 1, or 2 are used to compute bin statistics (suspect/failed points are excluded).
- The binned quality flag is a composite of the individual flags in the bin. For each flag digit/position, the composite uses the largest value observed in that position. Example:

 Individual data flags:
 111144000022222222111100000000
 121122000033222222114400000000

 Composite flag:
 121144000033222222114400000000

Binned Data Structure

- A one-day query at 1-minute resolution returns 1440 rows (60 * 24).
- Every binned-with-statistics file contains these core columns:

- `time` — center timestamp of the bin (ISO 8601, UTC)
- `spot_time` — timestamp of the spot observation used for spot value
- `spot_value` — the observation value closest in time to the bin center
- `num_values` — number of observations included in the bin (after QC filtering)
- per-parameter statistic columns (for each selected parameter):
 	- `<parameter>_mean` — unweighted mean (or circular/vector mean when applicable)
 	- `<parameter>_std` — standard deviation
 	- `<parameter>_min` — minimum value in the bin
 	- `<parameter>_max` — maximum value in the bin
 	- `<parameter>_median` — median value
 	- `<parameter>_count` — number of raw observations used to compute stats
 	- `<parameter>_spot` — spot value (single observation nearest to bin center)

**Example row:**

```
2026-01-15T12:30:00Z,2026-01-15T12:29:59Z,15.256,42,15.234,0.456,15.089,15.412,15.301,42,15.256
```

**Accompanying metadata file:**

Each binned export includes a metadata CSV listing fields, types, units, and missing-value codes. Example metadata entry:

```
Variable Name,Data Type,Dimensions,Units,Long Name,Standard Name,Missing Value
sea_water_temperature,double,time latitude longitude,degree_C,Sea Water Temperature,sea_water_temperature,-999
salinity,double,time latitude longitude,PSU,Salinity,sea_water_salinity,-999
```

Use the metadata file to interpret per-column units and missing-value indicators (commonly `-999` or `NaN`).

### 2. High Resolution (Full-Resolution) Data

**Best for:** Detailed sensor analysis, quality control investigations, and real-time data streams

High-resolution data are delivered as ERDDAP CSV exports at native sensor frequency (typically 1 Hz or higher, depending on instrument). Each row contains the raw timestamp, measured value(s), and accompanying quality flags.

**CSV Structure:**

- `time` — ISO 8601 timestamp (UTC) for each measurement
- `<parameter_name>` — raw sensor value(s) at measurement frequency
- `<parameter_name>_flag` — composite quality flag for each parameter

**File format:** Standard CSV with consistent delimiter (configurable via export interface)

**Size warning:** Can exceed 150 MB/day for a full cruise dataset without filtering. Browser-based downloads may time out for very large exports; consider using direct ERDDAP URLs and command-line tools for production pipelines.

### 3. Native Unmodified Data

**Best for:** Sensor troubleshooting, raw data archival, and complete historical records

These are the original, unmodified data messages from sensors stored exactly as received by CORIOLIX, packaged one file per sensor per day. No quality control has been applied.

**Important characteristics:**

- Raw instrument format (varies by sensor type and manufacturer protocol)
- Timestamps prepended to each message
- No standardization or alignment across parameters
- Archived to [R2R (Rolling Rock Repository)](https://www.rvdata.us/) at cruise completion for long-term preservation
- Available for direct download by all users

## How to Export Data

### One-Click Download

The quickest way to get data from the latest cruise:

1. Navigate to **Data → Export Data** in the CORIOLIX interface
2. Select either **Binned**, **High Resolution**, or **Binned+Statistics** page
3. Click **"Download [Cruise ID] Data"** to use site defaults (latest cruise, all parameters, default bin/resolution)
4. The browser downloads a ZIP file containing the data CSV and metadata CSV

### Customized Download

For precision control over exported datasets:

1. Go to **Data → Export Data** and select the desired data type (Binned, High Resolution, or Binned+Statistics)
2. In the **"Customized"** section, configure:

#### Temporal Resolution

- **Binned exports:** Select "Default Bin" (site default, usually 60 seconds) or "Custom Bin" (if configured)
- **High-resolution exports:** Select native frequency or an aggregation interval (1 second, 1 minute, 10 minutes, 1 hour, etc.)

#### Date Range

- **Preset:** Last Day, Last 7 Days, Last 30 Days, Entire Cruise
- **Custom:** Specify exact start and end datetimes (UTC, format: YYYY-MM-DDTHH:MM:SS)

#### Data Format

- CSV (Comma-Delimited) — recommended for most uses
- TSV (Tab-Delimited)
- JSON, GeoJSON, XHTML, MATLAB `.mat`, netCDF `.nc`
- OPeNDAP variants (`.asc`, `.das`, `.dds`, `.dods`)

#### Parameter Selection

- Parameters are grouped by sensor class (e.g., Temperature, Salinity, Wind)
- Use "Select/Deselect All" for each class or pick individual parameters
- Selected parameters determine which ERDDAP fields are requested

#### Show Data Access URLs

- Check this box to reveal the underlying ERDDAP URLs
- Useful for automating downloads or understanding data sources
- URLs can be directly fetched with curl, Python, or other tools

1. Click **"Download"** — the browser fetches ERDDAP data, assembles a ZIP, and saves it locally

### Download Progress and Limits

- **Progress bar:** Shows % complete as the browser fetches and assembles the ZIP
- **File size warnings:** Binned data (~30 MB/day); High-resolution (~150 MB/day without filtering)
- **Large exports:** May time out in browser; consider using direct ERDDAP URLs and server-side tools instead

## File Naming and Organization

Downloaded ZIP files follow this naming convention (generated client-side by the browser):

```
CORIOLIX_<vessel>_<cruise_id>_<sensor_name>_<start_date>_<end_date>_<bin_resolution>.zip
```

Example:

```
CORIOLIX_SKQ_SKQ202601S_CTD_2026-01-15_2026-01-20_60s.zip
CORIOLIX_SKQ_SKQ202601S__2026-01-15_2026-01-20_default.zip
```

Inside the ZIP:

- `<table_name>.<format>` — the binned or high-resolution data CSV
- `<table_name>_metadata.csv` — field definitions, units, and data type information

## Data Quality and QC State

Exported data reflect the quality control state of the underlying tables **at the time of export**. Quality is not static:

- QC flags may change as cruise technicians identify and document issues
- Subsequent exports of the same date range may contain different flags or values
- Always inspect the accompanying metadata and quality flag columns before using data for publication
- Consult the metadata file for descriptions of each field and its units
- Use the `point` identifier if you need to cross-reference data across multiple exports

**Note:** Unlike v2, data exports are not labeled as "final" or "published." All exports are working products reflecting current QC status.

## Common Questions

**Q: What format should I download?**  
A: Start with binned data for time-series analysis and statistical summaries. Use high-resolution data only if you need sub-minute detail or are investigating sensor behavior. Native data is rarely needed unless troubleshooting.

**Q: How do I read the binned CSV?**  
A: Open it in Excel, R, Python pandas, or any CSV-aware tool. The header row lists all columns (time, latitude, longitude, parameter_1, parameter_2, etc.). Refer to the metadata CSV for field definitions, units, and missing value codes.

**Q: How large are typical exports?**  
A: A day of binned data is ~30 MB; a day of high-resolution can exceed 150 MB. Check file size warnings in the UI before clicking Download.

**Q: Can I automate data downloads?**  
A: Yes. Click "Show Data Access URLs" to reveal ERDDAP `tabledap` links. Use curl, wget, or your preferred language to fetch data directly:

```bash
curl -o data.csv "https://erddap.example.org/tabledap/coriolix_binned_60s.csv?time,latitude,longitude,parameter_1&time%3E=2026-01-15T00:00:00Z&time%3C=2026-01-16T00:00:00Z&orderBy(\"time\")"
```

**Q: Why does data change between exports of the same cruise?**  
A: Quality control and processing are ongoing. If QC reviewers flag previously "good" data as suspect, those points may be excluded from bins, and statistics (mean, std, etc.) will recalculate. Always reimport if QC is finalized.

**Q: What does the metadata CSV tell me?**  
A: It lists every available field, its data type (double, string, etc.), units, long name, and missing value code. Use this to understand field meanings and how to handle `-999` or `NaN` in your analysis.

**Q: What time zone are timestamps in?**  
A: All timestamps are ISO 8601 format in UTC (Coordinated Universal Time). Convert to local time in your analysis code if needed.

**Q: How do I use the ERDDAP URLs?**  
A: They are direct HTTP GET requests to the ERDDAP server. You can paste them in a browser, use curl/wget, or build them programmatically by substituting date ranges, fields, and filtering parameters.
