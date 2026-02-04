# Exporting Data

CORIOLIX provides flexible options for downloading oceanographic data in multiple formats and resolutions. Whether you need high-frequency sensor data or quality-controlled averages, you can customize your data export to match your research needs.

## Data Download Options

CORIOLIX offers three types of data downloads, each suited for different analytical and operational needs:

### 1. Binned Data (Recommended for most users)

**Best for:** Time-series analysis, quality control, and general research use

Binned data are provided in temporal bins (default: 1-minute intervals) with statistical summaries. This is the most commonly used format for research applications.

**What's included:**

- **Mean value** for the time bin
- **Statistics:** standard deviation, minimum, maximum, count of observations
- **Spot value:** the actual data point closest in time to the bin center
- **Quality flags:** composite flags from all data in the bin
- **Location data:** latitude and longitude spot values closest to the bin time

**Data quality levels:**

| Level     | Description                                                      | Use Case                              |
| --------- | ---------------------------------------------------------------- | ------------------------------------- |
| **All**   | All data points regardless of quality flags                      | Data exploration, completeness        |
| **Best**  | Only flag values 0-2 (no suspect/failed data)                    | Publication-quality analysis          |
| **SAMOS** | Subset of quality tests (gap, syntax, gross range, global range) | Interoperability with SAMOS standards |

The "Best" product is dynamically regenerated throughout the cruise as quality control flags are updated. Published data will have the highest quality.

**File structure:**
Each line contains:

```
Datetime, Latitude, Longitude, Parameter_1_JSON, Parameter_2_JSON, ...
```

Example parameter data (JSON format):

```json
{
  "a": [359.017, 5.259, 0.1, 359.9, 120, 359.5, -999], // all: [mean, std, min, max, count, spot, median]
  "b": [358.982, 5.321, 1, 359.9, 117, 359.5, -999], // best: [mean, std, min, max, count, spot, median]
  "c": [358.982, 5.321, 1, 359.9, 117, 359.5, -999], // samos: [mean, std, min, max, count, spot, median]
  "fa": "224122222222222222222222222222", // all flags
  "fb": "221122222222222222222222222222", // best flags
  "fc": "221122222222222222222222222222", // samos flags
  "sa": "2021-05-06 22:12:59Z", // all spot datetime
  "sb": "2021-05-06 22:12:59Z", // best spot datetime
  "sc": "2021-05-06 22:12:59Z" // samos spot datetime
}
```

!!! note "Special Data Handling" - **Circular data** (heading, wind direction): calculated using circular mean and circular standard deviation - **Vector data** (true winds, currents): components (u,v,w) are averaged separately, then recalculated as direction and magnitude - **Low-frequency sensors** (e.g., ceilomet every 6 minutes): populated with NaN values between measurements

### 2. High Resolution Data

**Best for:** Real-time applications, detailed sensor behavior, quality control investigations

High-resolution data are extracted and stored as searchable datasets at 1 Hz resolution (or native frequency if lower). Timestamps and values are unmodified from the original sensor data.

**What's included:**

- Raw timestamps and unmodified data values
- Preliminary quality control flags

**File format:** CSV with columns for timestamp, parameter values, and flags

**Warning:** Can exceed 150 MB/day for a full cruise dataset

### 3. Native Unmodified Data

**Best for:** Sensor troubleshooting, raw data archival, complete historical record

These are the original, unmodified data messages from sensors stored exactly as received by CORIOLIX, one file per sensor per day.

**Important:**

- No quality control has been applied
- Format varies by sensor type
- Timestamps prepended to each message
- Archived to R2R (Rolling Rock Repository) after each cruise
- Available for direct download by end users

### How to Export Data

#### One-Click Download

The quickest way to get data from the latest cruise:

1. Navigate to **Data Access → Export Data** in the CORIOLIX interface
2. Click **"Download [Cruise ID] Data"**
3. Data for the entire cruise downloads as CSV file(s)
4. One-minute binned data is the default format

#### Customized Download

For more control over what you export:

1. Go to **Data Access → Export Data**
2. In the **"Download Customized Data"** section, configure:

##### Temporal Resolution

- **Default Bin (One Minute):** Standard 1-minute bins
- **Custom Interval:** Alternative bin sizes available on request (must be configured before cruise starts on the [Data/Archive Settings](https://coriolix.sikuliaq.alaska.edu/settings/data/) page)

##### Date Range

- Select a start date and time
- Select an end date and time
- Default shows the last day of data

##### Data Format

- **Comma Delimited (.csv):** Standard CSV format, compatible with all analysis software
- _Other formats may be available depending on system configuration_

##### Data Access URLs

- Check "Show Data Access URLs" to see direct URLs for programmatic access
- Useful for automation, API integration, and scripting workflows

#### Download Progress

Large datasets may take time to process. The system will:

- Display file size warnings (can exceed 30 MB/day for binned data)
- Generate compressed archives if files are large
- Provide download links via email or direct browser download

## File Naming and Organization

Downloaded files follow this naming convention:

```
[cruise_id]_binned_[startdate]_[enddate].csv
[cruise_id]_fullres_[startdate]_[enddate].csv
```

Example:

```
2956_SKQ2601S_binned_2026-01-11_2026-02-14.csv
```

## Data Archival

### Retention Policy

- **Real-time binned data:** Available for X days (configurable 10-365 days; 90 days recommended)
- **Full resolution data:** Available during and shortly after cruise
- **Native data:** Archived to [R2R (Rolling Rock Repository)](https://www.rvdata.us/) at cruise conclusion for long-term preservation

### Accessing Archived Data

- Historical cruise data: Contact the data manager or check the [R2R archive](https://www.rvdata.us/)
- Specific datasets: Use the R2R query interface with cruise ID and parameters

## Quality Flags

Quality flags are composite values indicating data reliability:

- **0:** Good data
- **1:** Probably good data
- **2:** Possibly suspect data
- **3:** Likely bad/failed data

For publication-quality analysis, use the "Best" binned dataset which includes only flags 0-2.

## Common Questions

**Q: Which format should I use for my research?**  
A: Start with binned data (Best quality). It provides statistical summaries and is quality-controlled. Use high-resolution data only if you need sensor-by-sensor analysis or real-time investigation.

**Q: Can I automate data downloads?**  
A: Yes! Use the "Show Data Access URLs" option to get direct links suitable for scripting and programmatic access.

**Q: Why does "Best" data change during a cruise?**  
A: The "Best" product is regenerated as technicians perform quality control. More flags may be set to "suspect" or "fail" as issues are discovered, changing which data points are included in bins.

**Q: How do I handle the JSON format in binned data?**  
A: Most analysis software can parse the JSON directly. Alternatively, expand the JSON to separate columns or use a parsing library in your preferred language (Python's `json` module, R's `jsonlite`, etc.).

**Q: What's the difference between "spot value" and "mean"?**  
A: The mean is calculated from all observations in the time bin. The spot value is the single actual observation closest in time to the bin center—useful for discrete events or quality verification.

## Additional Resources

- [Data Structure & Format Details](https://coriolix.sikuliaq.alaska.edu/data/format/)
- [Quality Flags Documentation](https://coriolix.sikuliaq.alaska.edu/data/flags/)
- [R2R Long-term Archive](https://www.rvdata.us/)
- [Data Download Interface](https://coriolix.sikuliaq.alaska.edu/data/download/binned/)
