# Data Access

CORIOLIX provides access to raw, high-resolution, and binned data products via multiple modes including data download, API, and data services. Please review the details related to data structure options and access methods described here to identify the best match for different needs.

## Temporal Structure Details

### Native Unmodified Data
All sensor data received by CORIOLIX are archived as ASCII flat files, one file per sensor per day. The unmodified raw message strings are stored at the native resolution collected by the sensor. The format of the strings varies by sensor. Each message string is prepended with the UTC timestamp upon receipt. No quality control has been performed on these data.

### High Resolution Data
For real-time applications, a subset of sensor data are extracted and stored within CORIOLIX. These searchable datasets are available at 1 Hz resolution or below. The collection timestamps and data values are unmodified. Preliminary quality control flags are provided as an additional field. 

### Binned Data
These data are provided in temporal (e.g. one-minute) bins. The mean and accompanying statistics are provided. Preliminary quality flags are included. 

#### Binning Method

Data are binned using an unweighted mean over a set time interval. Statistics are also captured (std, min, max, num, spotval, median) and made available to the end-user.

Circular data such as heading or wind direction are averaged using circular mean and circular standard deviation calculations. For example, the circular average of 355 and 5 is 0 not 180.

Vector data such as true winds are averaged using vector averaging. First, the components (u,v,w) are each averaged using a simple mean. These mean components are then used to derive the mean direction and mean magnitude:

mean_direction = rad2deg(atan2(mean_u, mean_v)) + 180

mean_magnitude = sqrt(mean_u^2 + mean_v^2)

#### Binning Time Interval

All parameters are binned by default to 1-minute bins, centered on minute timestamps (e.g. 12:03:00, 12:04:00). On request prior to a cruise, binned data at a custom time interval may also be generated for that cruise. Data collected less frequently than every minute are populated with NaNs between data points. The timestamp provided with each bin is the center time of the bin.

#### Date Range

Binned data are stored and available only for a user specified length of time. The number of days can be configured from 10 - 365 days (90 days is recommended). This must be discussed before a cruise and only CORIOLIX users with Support privileges and above can make this change. 

#### Binned Data Quality

Three binned products are provided: "All", "Best", and "SAMOS". The distinction is the quality of data used to calculate the bin.

#### All Bins (a)
The "all" binned product bins all data points within the time interval regardless of quality. Quality flags are ignored.

#### Best Bins (b)
Best bins are derived only from data that have flag values of 0, 1, or 2 (i.e. no suspect or failed data). For example, if there are 10 data points within the binning time interval, and one of them has a flag set to "fail", only the remaining 9 data points are used to calculate the bin value.

Special note: QA/QC happens at various stages throughout the data lifecycle. "Best" bins are regenerated (a) at various stages of the automated QA/QC process, (b) on demand by the sensor technician during the manual QA/QC process, and (c) at the end of the cruise. As a consequence, the "best" binned values may change throughout the cruise. Data earlier in the lifecycle will, by definition, have fewer opportunities for flags to be set to "suspect" or "fail".

#### SAMOS Bins (c)
The third binned product, "SAMOS", uses only a subset of flags to determine whether data should be included in the bin or not. Data must pass only the following four quality tests to be included in the bin:

(1) Gap Test

(2) Syntax Test

(3) Gross Range Test

(4) Global Range Test

SAMOS performs further quality assessment on these binned data.

#### Binned Quality Flags

Quality flags representing the binned data are a composite of all of the flags from that bin. For each flag position, the largest value is used in the composite binned flag.

For example:

Individual Data Flags:

111144000022222222111100000000

121122000033222222114400000000

Composite Flag:

121144000033222222114400000000

Special note: QA/QC happens at various stages throughout the data lifecycle. As a consequence, the composite quality flags may change throughout the cruise.

#### Binned Data Structure

A file with a single day of one-minute binned data will contain 1440 rows (60 minutes * 24 hours). Each downloaded binned data file contains the following columns:

| Title | Description |
| ----- | ----- |
| Datetime | The center timestamp of the bin |
| Latitude | The latitude closest in time to the bin center timestamp (a spot value) |
| Longitude | The longitude closest in time to the bin center timestamp (a spot value) |
| Parameter Data | A JSON dictionary containing the binned data and statistics |

Multiple parameters may be included in a single file (one column per parameter).

Binned data and statistics for a given parameter and time bin are stored as a JSON dictionary. An example follows below:

{ "a": [ 359.017, 5.259, 0.1, 359.9, 120, 359.5, -999 ], "b": [ 358.982, 5.321, 1, 359.9, 117, 359.5, -999 ], "c": [ 358.982, 5.321, 1, 359.9, 117, 359.5, -999 ], "fa": "224122222222222222222222222222", "fb": "221122222222222222222222222222", "fc": "221122222222222222222222222222", "sa": "2021-05-06 22:12:59Z", "sb": "2021-05-06 22:12:59Z", "sc": "2021-05-06 22:12:59Z" }

Where:

"a": all array (mean, std, min, max, num, spot, median)

"b": best array (mean, std, min, max, num, spot, median)

"c": samos array (mean, std, min, max, num, spot, median)

"fa": all flag (combination of flags from the data used to derive the "all" bins)

"fb": best flags (combination of flags from the data used to derive the "best" bin)

"fc": samos flag (combination of flags from the data used to derive the "samos" bin)

"sa": spot datetime (all)

"sb": spot datetime (best)

"sc": spot datetime (samos)

Spot values are unmodified data values that were collected closest in time to the center time of the bin.

# Modes of Data Access
CORIOLIX supports varied modes of data access, described briefly here. 

## File (download/file share)
|data structure:|native only|
|----------------------|--------------------|
| **temporal resolution** | **exactly once per day** |
| **data formats:** | **.csv** |
| **exchange protocols:** | **smb & nfs (read only) file share** | 

## CORIOLIX REST API (polling)
|data structures:|native, high resolution, binned|
|----------------------|-------------------------------|
| **temporal resolution** | **any window of time** |
| **data formats:** | **.json, .geojson**|
| **exchange protocol:**| **https**|

## ERDDAP API (polling)
|data structures:|native, high resolution, binned|
|----------------------|-------------------------------|
| **temporal resolution** | **any window of time** |
| **data formats:** | **.csv, .tsv, .mat, .nc** |
| **exchange protocol:**| **https**|

## Data Services (pushing)
|data structures:|real-time|
|----------------------|---------|
| **temporal resolution** | **near real-time only** |
| **data format:** | **native sensor messages** |
| **exchange protocols:** | **udp, mqtt** |
