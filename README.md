# **NYC311 Data Challenge**

## **Table of Contents**
- [Project Overview](#project-overview)
- [Directory Structure](#directory-structure)
- [Setup Instructions](#setup-instructions)
- [How to Run the Project](#how-to-run-the-project)
- [Outputs Generated](#outputs-generated)
- [Script Details](#script-details)
  - [main.py](#mainpy)
  - [etl.py](#etlpy)
  - [utils.py](#utilspy)
  - [data_preprocessing.py](#data_preprocessingpy)
  - [aggregation.py](#aggregationpy)
  - [visualization.py](#visualizationpy)
  - [spatialdata_processing.py](#spatialdata_processingpy)
- [Assumptions](#assumptions)

---

## **Project Overview**
This project processes and analyzes NYC 311 service request data to generate insights and visualizations. The workflow includes data fetching, preprocessing, aggregation, visualization, and spatial analysis, culminating in interactive maps and plots.

---

## **Directory Structure**
```
NYC311_DataChallenge/
├── data/                   # Stores fetched, preprocessed, aggregated data, and outputs (plots, maps)
├── main.py                 # Main script to run the project
├── README.md               # Project documentation
├── requirements.txt        # Python dependencies
├── scripts/                # Contains modular scripts for each task
│   ├── utils.py            # Utility functions (setup data directory)
│   ├── etl.py              # Fetches data from NYC 311 API
│   ├── data_preprocessing.py # Preprocesses raw data and handles missing values
│   ├── aggregation.py      # Aggregates data by date, hour, and complaint type
│   ├── visualization.py    # Creates interactive multi-line plots
│   ├── spatialdata_processing.py # Generates interactive choropleth maps
├── NYC_data_challenge/     # Virtual environment folder
```
---

## **Setup Instructions**
### Step 1: Clone the Repository
- git clone https://github.com/UshashiP/NYC_DataChallenge.git
- cd NYC311_DataChallenge
### Step 2: Create a Virtual Environment
- python -m venv NYC_data_challenge
- source NYC_data_challenge/bin/activate   # On Mac/Linux
- NYC_data_challenge\Scripts\activate      # On Windows
### Step 3: Install Dependencies
pip install -r requirements.txt
### Step 4: Place Shapefile for NTAs
Place the shapefile folder in the 'data' directory (data/nta_shapefile/)
### Step 5: Run the Project
To execute the pipeline, simply run: python main.py from your teminal or bash

---

## Outputs Generated
- Raw Data: data/raw.csv (Task 1)
- Preprocessed Data: data/preprocessed.csv
- Aggregated Data: data/aggregated.csv (Task 2)
- Interactive Plot: data/interactive_visualization.html (Task 3)
- Interactive Choropleth Map: data/interactive_choropleth_plotly.html (Task 4)
  
---

## Script Details
### main.py
The entry point of the project orchestrates all steps. It creates a structured data directory, dynamically retrieves raw NYC 311 data, preprocesses data to resolve missing values and validate key fields, aggregates the data, and generates an interactive plot of complaint trends and a choropleth map.

### etl.py
A NYC311ETL class is used to save the last seven days' NYC 311 service request data as data/raw.csv. Initiation (__init__): Ensures that the data directory exists by taking a directory path (data_dir) as input. The fetch_data() method dynamically retrieves service request data from the NYC Open Data API and saves it in a structured CSV file. Output - data/raw.csv

### utils.py
Contains utility function. Identifies the root directory of the project.
setup_data_directory(): Ensures the 'data' directory is created.

### data_preprocessing.py
Preprocesses raw data, drops rows with missing created_date (the data did not showcase any missing created_date for all the runs),fills missing complaint_type with "Unknown", handles missing latitude and longitude: drops rows with missing coordinates (<1%)geocoding, geocodes addresses if specified (geocode_missing=True), fills missing coordinates with borough averages if geocoding fails and validation. Outputs to data/preprocessed.csv.
The rest of the columns with missing or null values were ignored as they were not specific to this data challenge.
Detailed picture of the functions:
Functions:
- #### def preprocess_data(input_file, output_file, geocode_missing=False):
The main preprocessing function handles missing values, validates key columns, and saves the cleaned dataset. It loads raw data, validates columns with defined actions (e.g., drop, fill, or validate_geo), and prints dataset stats before and after processing. Finally, it saves the cleaned data and returns a pandas dataframe.
- #### def handle_missing_values(df, column, action, *args, geocode_missing=False):
Handles missing values in a column based on the specified action: drop rows, fill with a value, or validate latitude/longitude (drop, impute, or geocode). Modifies the DataFrame in place.
- #### def validate_geospatial(df, geocode_missing):
Validates latitude and longitude by handling missing values: drops rows (<1% missing), geocodes if enabled, or imputes with borough medians. Modifies the DataFrame in place.
- #### def geocode_missing_coordinates(df):
Uses the geocoder library to fill missing latitude and longitude using the incident_address. Counts missing values, attempts geocoding for each row, updates coordinates if successful, and prints geocoding results. Modifies the DataFrame in place.

### aggregation.py
Aggregates data by created_date_hour and complaint_type. Output - data/aggregated.csv.
The task explicitly suggests creating a column named created_date_hour, implying aggregation at the hour level. Following this guideline ensures consistency with the expectations of the challenge. Including minutes, seconds, and milliseconds makes the visualization extremely messy, especially for time-series plots. By grouping data into hourly intervals, patterns and trends became more apparent and easier to interpret.

### visualization.py
Generates an interactive multi-line time-series plot of complaint trends using Plotly. Reads aggregated data, validates columns, and creates an HTML file with hoverable lines for each complaint type. Output - data/interactive_visualization.html.

### spatialdata_processing.py
The spatial_processing function generates an interactive choropleth map to visualize complaint counts by Neighborhood Tabulation Area (NTA). It reads the preprocessed data, verifying latitude and longitude columns, and filters rows based on the specified complaint type. It converts the filtered data into a GeoDataFrame for spatial operations and reprojects it to match the CRS of the NTA shapefile, ensuring spatial consistency. A spatial join associates complaints with their respective NTAs, and complaint counts are aggregated for each area. The aggregated counts are merged back into the NTA shapefile for visualization. The data is then reprojected to WGS84 for compatibility with Folium. Finally, an interactive map is created with a choropleth layer that visualizes complaint counts using color gradients. Output - data/interactive_choropleth_plotly.html.

---

## Assumptions
- Dynamic Dataset: Fetches data for the most recent 7 days dynamically, ensuring up-to-date insights.
- Missing Values: Handles missing coordinates by dropping rows (<1%) or imputing using geocoding or borough averages.
- Complaint Type: The choropleth map focuses on specific complaint types, e.g., "HEAT/HOT WATER."

