# Data Access

CORIOLIX provides access to raw, high-resolution, and binned data products via multiple modes including data download, API, and data services. Please review the details related to data structure options and access methods described here to identify the best match for different needs.

## Data States
### Native Unmodified Sensor Messages
The unmodified data messages output from the sensor are provided at the native resolution in daily text files. The time of collection and sensor ID is prepended to each data message. No quality control has been performed.

```txt title="Example native unmodified data from a Seapath 380 sampled from a daily logfile."
2026-03-23T23:45:10.261108Z seapth000000 $GPHDT,346.98,T*05
2026-03-23T23:45:10.265173Z seapth000000 $GPGGA,200053.36,5711.764042,N,14657.806146,W,2,12,1.0,0.37,M,7.11,M,1.4,0001*6A
2026-03-23T23:45:10.275551Z seapth000000 $GPVTG,13.34,T,357.26,M,0.4,N,0.8,K,D*1A
2026-03-23T23:45:11.261309Z seapth000000 $GPHDT,346.90,T*0D
2026-03-23T23:45:11.273443Z seapth000000 $GPGGA,200054.36,5711.764050,N,14657.806196,W,2,12,1.0,0.42,M,7.11,M,1.2,0001*67
2026-03-23T23:45:11.284831Z seapth000000 $GPVTG,27.61,T,11.52,M,0.2,N,0.4,K,D*25
```

### Native Resolution Parameter Vales
These data are parsed from the sensor data messages and provided as individual parameters at the native resolution (e.g. 1 Hz). Processed data are also sometimes available. Timestamps and data frequencies vary between sensors. Preliminary quality flags are included. 

```txt title="Example of native resolution parameter data from the same Seapath 380.""
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

```txt title="foobarbin"
time,spot_time,spot_value,mean,stddev,minimum,maximum,median,num_values
UTC,UTC,degrees_C,degrees_C,degrees_C,degrees_C,degrees_C,degrees_C,unitless
2026-03-23T00:00:00Z,2026-03-22T23:59:58Z,4.6,4.6,0.0,4.6,4.6,4.6,20.0
2026-03-23T00:01:00Z,2026-03-23T00:01:00Z,4.6,4.611,0.031,4.6,4.7,4.6,19.0
2026-03-23T00:02:00Z,2026-03-23T00:02:01Z,4.6,4.605,0.022,4.6,4.7,4.6,20.0
2026-03-23T00:03:00Z,2026-03-23T00:02:59Z,4.6,4.6,0.0,4.6,4.6,4.6,19.0
2026-03-23T00:04:00Z,2026-03-23T00:04:01Z,4.6,4.605,0.022,4.6,4.7,4.6,20.0
2026-03-23T00:05:00Z,2026-03-23T00:04:59Z,4.7,4.616,0.036,4.6,4.7,4.6,19.0
2026-03-23T00:06:00Z,2026-03-23T00:06:00Z,4.6,4.605,0.022,4.6,4.7,4.6,20.0
2026-03-23T00:07:00Z,2026-03-23T00:06:59Z,4.6,4.605,0.022,4.6,4.7,4.6,20.0
2026-03-23T00:08:00Z,2026-03-23T00:08:00Z,4.6,4.6,0.0,4.6,4.6,4.6,19.0
2026-03-23T00:09:00Z,2026-03-23T00:09:01Z,4.6,4.605,0.022,4.6,4.7,4.6,20.0
2026-03-23T00:10:00Z,2026-03-23T00:10:00Z,4.6,4.6,0.0,4.6,4.6,4.6,19.0
2026-03-23T00:11:00Z,2026-03-23T00:11:01Z,4.6,4.6,0.0,4.6,4.6,4.6,20.0
2026-03-23T00:12:00Z,2026-03-23T00:11:59Z,4.6,4.6,0.0,4.6,4.6,4.6,19.0
2026-03-23T00:13:00Z,2026-03-23T00:12:59Z,4.6,4.6,0.0,4.6,4.6,4.6,20.0
2026-03-23T00:14:00Z,2026-03-23T00:13:59Z,4.6,4.605,0.022,4.6,4.7,4.6,20.0
2026-03-23T00:15:00Z,2026-03-23T00:15:00Z,4.6,4.6,0.0,4.6,4.6,4.6,19.0
2026-03-23T00:16:00Z,2026-03-23T00:16:01Z,4.6,4.61,0.03,4.6,4.7,4.6,20.0
2026-03-23T00:17:00Z,2026-03-23T00:16:59Z,4.6,4.6,0.0,4.6,4.6,4.6,20.0
2026-03-23T00:18:00Z,2026-03-23T00:18:01Z,4.6,4.6,0.0,4.6,4.6,4.6,19.0
```

### Binned Data without Statistics
This dataset includes data from all sensors and all parameters. The data are provided in temporal (e.g. one-minute) bins. Only the mean is provided (no statistics, no quality flags). 
```txt
time,latitude,longitude,point,relativewinddirection_degrees_anem2d000000,relativewinddirection_degrees_anem3d193204,relativewinddirectionmax_degrees_metwxt350085,relativewinddirectionmean_degrees_metwxt350085,relativewinddirectionmin_degrees_metwxt350085,relativewindspeedmax_mpers_metwxt350085,relativewindspeedmean_mpers_metwxt350085,relativewindspeedmin_mpers_metwxt350085,relativewindspeed_mpers_anem2d000000,relativewindspeed_mpers_anem3d193204,relativewindvelocityu_mpers_anem3d193204,relativewindvelocityv_mpers_anem3d193204,relativewindvelocityw_mpers_anem3d193204,truewinddirection_degreestrue_anem2d000000,truewinddirection_degreestrue_anem3d193204,truewindspeed_mpers_anem2d000000,truewindspeed_mpers_anem3d193204,truewindvelocityu_mpers_anem2d000000,truewindvelocityv_mpers_anem2d000000
UTC,degrees_north,degrees_east,degrees,degrees,degrees,degrees,degrees,degrees,m/s,m/s,m/s,m/s,m/s,m/s,m/s,m/s,degrees_true,degrees_true,m/s,m/s,m/s,m/s
2026-03-23T00:00:00Z,NaN,NaN,,239.744,235.219509,291,256,173,0.355,0.2,0.09,8.8183,1.453113,0.81362,-999.0,0.11704,85.635285,80,8.79809,1.434113,8.723357,1.060219
2026-03-23T00:01:00Z,NaN,NaN,,239.555,239.492856,285,316,166,0.32,0.215,0.115,9.7943,1.154713,0.5747,-999.0,0.12364,84.852381,84,9.792642,1.157485,9.647752,1.507495
2026-03-23T00:02:00Z,NaN,NaN,,233.537,234.089361,330,330,341,0.379,0.263,0.153,9.7858,1.617318,0.94013,-999.0,0.08136,78.52949,78,9.799105,1.63182,9.562227,1.940125
2026-03-23T00:03:00Z,NaN,NaN,,235.639,217.968507,261,215,209,0.64,0.48,0.35,9.1712,1.69898,1.33013,-999.0,0.0925,81.55234,63,9.168493,1.6928,8.983905,1.546151
2026-03-23T00:04:00Z,NaN,NaN,,232.185,220.110965,254,239,222,0.679,0.579,0.474,9.475,1.657805,1.24587,-999.0,0.08581,76.340683,63,9.475738,1.659165,9.141693,2.304002
2026-03-23T00:05:00Z,NaN,NaN,,240.365,207.11248,242,171,182,0.355,0.26,0.21,9.2528,1.247052,1.07652,-999.0,0.11923,86.314213,52,9.260593,1.261812,9.200008,0.876914
2026-03-23T00:06:00Z,NaN,NaN,,240.382,199.967065,217,206,195,0.553,0.453,0.326,10.153,1.212362,1.1057,0.05175,0.15695,86.458616,45,10.142813,1.192842,10.101832,0.885085
2026-03-23T00:07:00Z,NaN,NaN,,235.347,209.150236,185,168,153,0.56,0.44,0.355,9.4835,1.254382,1.05653,0.0115,0.17804,81.538487,53,9.494567,1.273838,9.350885,1.578138
2026-03-23T00:08:00Z,NaN,NaN,,234.622,209.247283,172,161,150,0.832,0.705,0.595,9.4062,0.780882,0.61903,0.06633,0.18329,80.285243,58,9.412487,0.78364,9.213353,1.937058
2026-03-23T00:09:00Z,NaN,NaN,,233.307,209.422302,146,139,133,0.86,0.785,0.68,8.524,1.551672,1.33188,-999.0,0.08284,79.741355,54,8.504187,1.53872,8.306588,1.749778
2026-03-23T00:10:00Z,NaN,NaN,,241.186,211.006905,147,140,133,0.785,0.675,0.6,8.7073,1.373747,1.16863,-999.0,0.10054,87.383788,56,8.712582,1.393178,8.661932,0.887353
2026-03-23T00:11:00Z,NaN,NaN,,240.723,210.56039,146,138,130,0.879,0.774,0.711,8.7375,1.499175,1.22813,0.0545,0.13267,86.055063,55,8.727128,1.498057,8.67076,0.835487
2026-03-23T00:12:00Z,NaN,NaN,,237.819,213.910321,132,125,118,0.95,0.835,0.735,8.1242,1.156533,0.94715,-999.0,0.13423,83.027337,58,8.142302,1.176115,8.058083,1.01937
2026-03-23T00:13:00Z,NaN,NaN,,236.519,206.766221,146,135,126,0.974,0.905,0.826,7.8905,1.369493,1.21433,-999.0,0.14574,81.006198,50,7.890825,1.373892,7.7285,1.348742
2026-03-23T00:14:00Z,NaN,NaN,,233.705,210.211034,147,139,131,0.97,0.85,0.775,8.3355,1.472377,1.22397,0.047,0.1124,80.20437,55,8.328478,1.472033,8.174455,1.416676
2026-03-23T00:15:00Z,NaN,NaN,,233.903,218.547781,144,137,129,1.168,1.053,0.974,8.2577,1.431048,1.11635,-999.0,0.1152,78.480766,63,8.266568,1.443818,8.031243,1.718151
2026-03-23T00:16:00Z,NaN,NaN,,238.632,217.239145,139,134,129,1.205,1.125,1.035,9.4057,1.436327,1.11787,-999.0,0.10337,83.963383,61,9.40865,1.438868,9.196505,1.954024
2026-03-23T00:17:00Z,NaN,NaN,,238.035,221.239543,138,131,125,1.147,1.053,0.963,9.553,1.782397,1.336,-999.0,0.08575,84.034479,66,9.53857,1.771125,9.458168,1.039365
2026-03-23T00:18:00Z,NaN,NaN,,230.769,226.117192,138,130,124,1.265,1.16,1.03,8.1397,1.691442,1.1702,-999.0,0.1223,76.371825,71,8.138162,1.692478,7.835317,1.919418
2026-03-23T00:19:00Z,NaN,NaN,,237.15,228.602268,149,135,119,1.43,1.255,1.105,8.3663,1.62714,1.07093,-999.0,0.10907,82.264859,73,8.377907,1.634823,8.266413,1.197751
2026-03-23T00:20:00Z,NaN,NaN,,248.035,229.869106,143,130,120,1.268,1.147,0.968,8.9498,1.59846,1.04013,-999.0,0.05386,93.754835,74,8.945685,1.597083,8.9047,0.522567
2026-03-23T00:21:00Z,NaN,NaN,,246.732,241.568208,139,131,121,1.39,1.27,1.175,9.3628,1.412438,0.66122,-999.0,0.11637,93.681083,87,9.35791,1.405297,9.316797,0.2957
2026-03-23T00:22:00Z,NaN,NaN,,241.852,238.547326,133,126,119,1.189,1.126,1.047,8.2858,1.33989,0.67538,-999.0,0.07867,88.412064,84,8.294962,1.351355,8.247783,0.888647
2026-03-23T00:23:00Z,NaN,NaN,,233.088,239.597605,142,136,128,1.22,1.12,1.01,8.4175,1.160283,0.57083,-999.0,0.123,79.97634,85,8.40359,1.151565,8.23374,1.530067
2026-03-23T00:24:00Z,NaN,NaN,,240.808,249.885568,143,138,130,1.39,1.285,1.18,7.6,1.322742,0.46197,-999.0,0.10704,88.633396,97,7.592947,1.315507,7.395085,2.018441
```

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
