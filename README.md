**Introduction**

This project is about predicting the 1 hour ahead emission of nitrogen dioxide (NO2) in the city of Zurich at the Stampfenbachstrasse, where the air quality and meteorological measurement station is located.

Nitrogen dioxide (NO2) and nitrogen monoxide (NO) contribute to the formation of smog and acid rain, and are affecting tropospheric ozone. These gases are usually produced from the reaction between nitrogen and oxygen during combustion of fuels at high temperatures in car engines.

The wider context of the problem is the air pollution in the cities. It can cause diseases and allergies to humans, harm to other living organisms and may damage the natural environment (climate change, ozone depletion) or built environment (acid rain).

<br/>

**Data preparation**

The raw dataset obtained in this notebook corresponds to 5 years of data, from January 1, 2015 to December 31, 2019. It contains air quality and meteorological hourly measurements from the Stampfenbachstrasse station in Zurich, public holidays and school holidays in Zurich and the day name feature.

2020, 2021, 2022 and 2023 were not taken into account because Covid-19 pandemic makes prediction difficult.

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

<br/>

**Exploratory data analysis**

The raw dataset from the data preparation notebook was loaded in the 1st section.

In the 2nd section data was prepared for the analysis by dealing with missing values, inconsistencies and outliers, relationships (NO, NO2 and NOx), distribution of features, integer encoding (day name, rain duration) and by adding time features (hour, month).

In the 3rd section, cycles, temporal dependencies, autocorrelation, stationarity and relationships were investigated.

In the end, dataset was prepared for the modeling by applying cyclical encoding, adding lagged features, applying log transformation and sample split.

<br/>

**K-nearest neighbors model**

K-nearest neighbors model was trained and tuned on the train set (11 months) and predictions were made on the test set (1 month).

First, one-hot encoding was applied to 3 features, rain duration (RainDur), public holiday (PH) and school holiday (SH). Then, using a pipeline, standard scaler and k-nearest neighbors regression were applied. k-NN regression was tuned with 24 values of nearest neighbors, from 1 to 24. TranformedTargetRegressor was used for the log transformation (log1p) of the target (NO2). Cross-validation object (cv) was created with the TimeSeriesSplit. The number of splits was 5 and the validation set was 1 month (24 hours multiplied by 30 days).

The optimal number of neighbors is 14. The smallest mean validation error is 7.60 µg/m3 which is 37% higher than the mean training error of 5.52 µg/m3. Standard deviation of the validation error is 1.03 which is 14x higher that the standard deviation of the training error (0.073). The cross-validation showed that the model is not robust when exposed to unseen data.

Tuned model with the optimal number of neighbors was fitted on the train set and evaluated on the test set. The MAE score of the tuned k-NN model is 7.41 µg/m3, which is 2x lower than the baseline of 15.93 µg/m3.

The first day of the test set, Friday, December 1, 2017, showed significant underpredictions during the daytime and small overpredictions in the night.

The whole test set (December 2017) showed mainly underpredictions throughout the day.

Training the model on a larger dataset, instead of 11 months, should improve the predictions.

<br/>

**Random forest model**

Random forest model was trained and tuned on the train set (11 months) and predictions were made on the test set (1 month).

First, one-hot encoding was applied to 3 features, rain duration (RainDur), public holiday (PH) and school holiday (SH). There was no feature scaling. Two hyperparameters were tuned with 3 values each, number of trees (n_estimators) and the maximum depth of the tree (max_depth). TranformedTargetRegressor was used for the log transformation (log1p) of the target (NO2). Cross-validation object (cv) was created with the TimeSeriesSplit. The number of splits was 5 and the validation set was 1 month (24 hours multiplied by 30 days).

The optimal number of trees is 100, while the optimal maximum depth of the tree is 15. The smallest mean validation error is 5.00 µg/m3 which is 44% higher than the mean training error of 3.47 µg/m3. The model is overfitting, as seen on 2 graphs with training and validation errors against hyperparameters.

Tuned model with the optimal hyperparameters was then fitted on the train set and evaluated on the test set. The MAE score of the tuned random model is 4.93 µg/m3 which is 3x lower than the baseline of 15.93 µg/m3.

The first day of the test set, Friday, December 1, 2017, showed underpredictions in the morning and afternoon hours, and overpredictions around noon and in the evening.

The whole test set (December 2017) showed mainly underpredictions on working days in the morning hours.

Training the model on a larger dataset, instead of 11 months, should improve predictions.

<br/>

**Ridge regression model**

Ridge regression model was trained and tuned on the train set (11 months) and predictions were made on the test set (1 month).

First, one-hot encoding was applied to 3 features, rain duration (RainDur), public holiday (PH) and school holiday (SH). Then, using a pipeline, standard scaler and ridge regression were applied. Ridge regression was tuned with 20 values of hyperparameter alpha, in the range from 0.0001 to 10'000. TranformedTargetRegressor was used for the log transformation (log1p) of the target (NO2). Cross-validation object (cv) was created with the TimeSeriesSplit. The number of splits was 5 and the validation set was 1 month (24 hours multiplied by 30 days).

The optimal alpha value is 4.28. The smallest mean validation error, 5.33 µg/m3, is 3% lower than the mean training error of 5.47 µg/m3, which is usually not the case. Standard deviation of the validation error is 8x higher than the standard deviation of the training error. The cross-validation showed that the model is not robust when exposed to unseen data.

Tuned model with the optimal alpha value was fitted on the train set and evaluated on the test set. The MAE score of the tuned ridge regression model is 4.94 µg/m3, which is 3x lower than the baseline of 15.93 µg/m3.

The first day of the test set, Friday, December 1, 2017, showed underpredictions in the morning and afternoon hours, and overpredictions around noon and in the evening.

The whole test set (December 2017) showed mainly underpredictions on working days in the morning hours.

Training the model on a larger dataset, instead of 11 months, should improve predictions.
