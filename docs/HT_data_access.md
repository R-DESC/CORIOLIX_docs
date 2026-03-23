# Data Access

CORIOLIX provides access to raw, high-resolution, and binned data products via multiple modes including data download, API, and data services. Please review the details related to data structure options and access methods described here to identify the best match for different needs.

## Data States
### Native Unmodified Sensor Messages
The unmodified data messages output from the sensor are provided at the native resolution in daily text files. The time of collection and sensor ID is prepended to each data message. No quality control has been performed.

Example native unmodified data from a Seapath 380 sampled from a daily logfile.
```txt
2026-03-23T23:45:10.261108Z seapth000000 $GPHDT,346.98,T*05
2026-03-23T23:45:10.265173Z seapth000000 $GPGGA,200053.36,5711.764042,N,14657.806146,W,2,12,1.0,0.37,M,7.11,M,1.4,0001*6A
2026-03-23T23:45:10.275551Z seapth000000 $GPVTG,13.34,T,357.26,M,0.4,N,0.8,K,D*1A
2026-03-23T23:45:11.261309Z seapth000000 $GPHDT,346.90,T*0D
2026-03-23T23:45:11.273443Z seapth000000 $GPGGA,200054.36,5711.764050,N,14657.806196,W,2,12,1.0,0.42,M,7.11,M,1.2,0001*67
2026-03-23T23:45:11.284831Z seapth000000 $GPVTG,27.61,T,11.52,M,0.2,N,0.4,K,D*25
```

### Native Resolution Parameter Vales
These data are parsed from the sensor data messages and provided as individual parameters at the native resolution (e.g. 1 Hz). Processed data are also sometimes available. Timestamps and data frequencies vary between sensors. Preliminary quality flags are included. 

```txt
time_raw,time,value,flag_summary,flag_string
UTC,UTC,degrees_true,unitless,unitless
2026-03-23T00:00:00Z,2026-03-23T00:00:00Z,205.39,1,212122222222222222222222222222
2026-03-23T00:00:01Z,2026-03-23T00:00:01Z,205.37,1,212122222222222222222222222222
2026-03-23T00:00:02Z,2026-03-23T00:00:02Z,205.74,1,212122222222222222222222222222
2026-03-23T00:00:03Z,2026-03-23T00:00:03Z,206.17,1,212122222222222222222222222222
2026-03-23T00:00:04Z,2026-03-23T00:00:04Z,206.23,1,212122222222222222222222222222
2026-03-23T00:00:05Z,2026-03-23T00:00:05Z,205.85,1,212122222222222222222222222222
2026-03-23T00:00:06Z,2026-03-23T00:00:06Z,205.27,1,212122222222222222222222222222
2026-03-23T00:00:07Z,2026-03-23T00:00:07Z,204.91,1,212122222222222222222222222222
2026-03-23T00:00:08Z,2026-03-23T00:00:08Z,205.01,1,212122222222222222222222222222
2026-03-23T00:00:09Z,2026-03-23T00:00:09Z,205.45,1,212122222222222222222222222222
2026-03-23T00:00:10Z,2026-03-23T00:00:10Z,205.72,1,212122222222222222222222222222
2026-03-23T00:00:11Z,2026-03-23T00:00:11Z,205.45,1,212122222222222222222222222222
2026-03-23T00:00:12Z,2026-03-23T00:00:12Z,204.85,1,212122222222222222222222222222
2026-03-23T00:00:13Z,2026-03-23T00:00:13Z,204.54,1,212122222222222222222222222222
2026-03-23T00:00:14Z,2026-03-23T00:00:14Z,204.73,1,212122222222222222222222222222
2026-03-23T00:00:15Z,2026-03-23T00:00:15Z,204.92,1,212122222222222222222222222222
2026-03-23T00:00:16Z,2026-03-23T00:00:16Z,204.95,1,212122222222222222222222222222
2026-03-23T00:00:17Z,2026-03-23T00:00:17Z,204.99,1,212122222222222222222222222222
2026-03-23T00:00:18Z,2026-03-23T00:00:18Z,205.17,1,212122222222222222222222222222
```

### Binned Data with Statistics
These data from individual parameters are provided in temporal (e.g. one-minute) bins. The mean and accompanying statistics are provided. Preliminary quality flags are included. 

### Binned Data without Statistics
This dataset includes data from all sensors and all parameters. The data are provided in temporal (e.g. one-minute) bins. Only the mean is provided (no statistics, no quality flags). 

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
