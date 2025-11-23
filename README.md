# Data preparation

The raw dataset obtained in this notebook corresponds to 5 years of data, from January 1, 2015 to December 31, 2019. It contains air quality and meteorological hourly measurements from the Stampfenbachstrasse station in Zurich, public holidays and school holidays in Zurich and the day name feature.

Air quality and meteorological measurements were taken from the open data portal of canton Zurich. Public holidays were taken from the officeholidays.com website. School holidays were taken from the schulferien.org website. The day name was obtained from the timestamp.

Air quality and meteorological measurements were merged by creating a dictionary of 16 dataframes (for each parameter). 16 dataframes where then merged on the basis of date. Public holidays, school holidays and the day name were added as separate columns.

The raw dataset has 43'824 rows and 20 features. There are 16 float (air quality and meteorological features), 2 integer (public holiday, school holiday), 1 datetime (timestamp) and 1 object (day name) feature.

Feature information:

- Timestamp
- Nitrogen dioxide (NO2), µg/m3
- Nitrogen monoxide (NO), µg/m3
- Nitrogen oxides (NOx), ppb
- Ozone (O3), µg/m3
- Fine dust (PM10), µg/m3
- Carbon monoxide (CO), mg/m3
- Sulfur dioxide (SO2), µg/m3
- Fine dust (PM2.5), µg/m3
- Relative humidity (Hr), %
- Duration of precipitation (RainDur), min
- Temperature (T), °C
- Wind direction (WD), °
- Vector wind speed (WVv), m/s
- Air pressure (p), hPa
- Scalar wind speed (WVs), m/s
- Global radiation (StrGlo), W/m2
- Public holiday (0 or 1)
- School holiday(0 or 1)
- Day (Monday - Sunday)
