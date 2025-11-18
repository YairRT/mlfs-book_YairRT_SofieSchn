## Predict Air Quality

This project builds an Air Quality Forecasting Service for an Air Quality sensor available at https://waqi.info/.


The output is a forecast for air quality, like this one:

![Air quality Prediction](https://featurestorebook.github.io/mlfs-book/air-quality/assets/img/pm25_forecast.png)


## Personalized Air Quality Predictions with a LLM

This air quality forecasting service has been augmented with LLM capabilities. You can ask it both future (forecasting) and historical questions about air quality at your location via a microphone or text input dialog.

We augment the prompt with:
 * your location,
 * today’s date,
 * predicted air quality (from a ML model),
 * historical air quality (from the feature store),
 * are you in a sensitive group (coming soon).


![Personalized Air Quality with LLMs Architecture](personalized-air-quality-with-llms.png)


## Application Architecture

![Application Architecture Air Quality with LLMs Architecture](app-air-quality-with-llms.png)


## Tutorial Instructions

You can find [instructions for running this tutorial in this Google Doc](https://docs.google.com/document/d/1YXfM1_rpo1-jM-lYyb1HpbV9EJPN6i1u6h2rhdPduNE/edit?usp=sharing).


# Air Quality Forecasting Notebooks
This project implements an end-to-end Air Quality Forecasting ML system using data from waqi.info and weather data from Open-Meteo. The pipeline is organized into modular Jupyter notebooks, each responsible for a specific stage:

# Notebooks Overview
1_air_quality_feature_backfill.ipynb
Loads historical air quality data (PM2.5) from a chosen sensor, cleans and engineers features (including lag features), and backfills the feature store.

# 2_air_quality_feature_pipeline.ipynb
Runs daily to fetch the latest air quality and weather data, computes lag features, and inserts new records into the feature store.

# 3_air_quality_training_pipeline.ipynb
Reads features from the feature store, creates a feature view, splits data into train/test, trains an XGBoost regression model, evaluates it, and registers the model in the Hopsworks Model Registry.

# 4_air_quality_batch_inference.ipynb
Loads the latest model and feature data, computes lag features for the forecast date(s), performs batch inference, and saves predictions for monitoring and dashboarding.

Parameterization for Multiple Scenarios
All notebooks are parameterized using papermill, allowing you to run the same pipeline for different air quality sensors or locations by simply changing the input parameters. This is achieved by defining a cell with the parameters tag at the top of each notebook

# Feature Engineering: Lag and Rolling Window Features
To improve the predictive power of the air quality forecasting models, the notebooks implement lag features and a rolling window feature for the PM2.5 measurements:

Lag Features
Lag features capture the value of PM2.5 from previous days, allowing the model to learn temporal dependencies and trends. For each record, the following lag features are computed:

pm25_lag_1: PM2.5 value from 1 day before
pm25_lag_2: PM2.5 value from 2 days before
pm25_lag_3: PM2.5 value from 3 days before
These are calculated using the Pandas shift()

# Rolling Window Feature
The rolling window feature provides a smoothed view of recent air quality by computing the mean PM2.5 value over the last 3 days:

pm25_roll_3: Rolling mean of PM2.5 over the previous 3 days
This is implemented using Pandas’ rolling() function