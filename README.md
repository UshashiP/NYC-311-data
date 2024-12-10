## Table of Contents
- [Project Overview](#project-overview)
- [Directory Structure](#directory-structure)
- [Setup Instructions](#setup-instructions)
- [How to Run the Project](#how-to-run-the-project)
- [Project Details](#project-details)



NYC311 Data Challenge
Project Overview
This project processes and analyzes NYC 311 service request data to generate insights and visualizations. The workflow includes data fetching, preprocessing, aggregation, visualization, and spatial analysis, culminating in interactive maps and plots.

The code is designed to be modular, reusable, and easy to run, with a clear structure and DRY-compliant scripts.

Directory Structure:

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

Setup Instructions
Step 1: Clone the Repository
git clone https://github.com/UshashiP/NYC_DataChallenge.git
cd NYC311_DataChallenge

Step 2: Create a Virtual Environment
python -m venv NYC_data_challenge
source NYC_data_challenge/bin/activate   # On Mac/Linux
NYC_data_challenge\Scripts\activate      # On Windows

Step 3: Install Dependencies
pip install -r requirements.txt

Step 4: Place Shapefile for NTA
data/nta_shapefile/


How to Run the Project
The main.py script orchestrates the entire pipeline. Simply run from the root/terminal:
python main.py

Outputs Generated
Raw Data: data/raw.csv
Preprocessed Data: data/preprocessed.csv
Aggregated Data: data/aggregated.csv
Interactive Plot: data/interactive_visualization.html
Interactive Choropleth Map: data/interactive_choropleth_plotly.html

Script Details
1. main.py
The entry point for running the entire project.
Executes each step in sequence:
Sets up the data/ directory.
Fetches raw data from the NYC 311 API.
Preprocesses the raw data.
Aggregates data for analysis.
Creates an interactive plot showing complaint trends over time.
Generates an interactive choropleth map for a specific complaint type.

2. scripts/etl.py
Fetches NYC 311 service request data for the last 7 days from the NYC Open Data API. It is the first step in the ETL (Extract, Transform, Load) pipeline.
Saves the raw data as data/raw.csv. 
Components
Imports
requests:
Used to make HTTP requests to the NYC 311 API.
pandas:
Converts the JSON response from the API into a tabular format and saves it as a CSV.
datetime and timedelta:
Constructs dynamic date ranges to fetch data for the last n days.
pathlib.Path:
Manages file paths in an OS-agnostic way.
Class: NYC311ETL
This class encapsulates the logic for extracting and saving NYC 311 service request data.
Initialization (__init__):Input: data_dir (Path or string)
Specifies where the fetched data will be stored. Creates the directory (data_dir) if it doesn’t already exist. Ensures the script can save output files in the specified location.
Method: fetch_data(): Fetches raw service request data from the API for the last n days and saves it as a CSV file.


3. scripts/utils.py
Contains utility functions.
setup_data_directory(): Ensures the 'data' directory is created.

4. scripts/utils.py
Contains utility functions. setup_data_directory(): Ensures the 'data' directory is created. Identify the root directory of the project. Create a data folder within the root directory if it does not already exist.
Return the absolute path of the data folder for consistent file handling across the project.


5. scripts/data_preprocessing.py
Handles missing values and validates the dataset:
Drops rows with missing created_date.
Fills missing complaint_type with "Unknown".
Handles missing latitude and longitude:
Drops rows with missing coordinates (<1%).
Geocodes addresses if specified (geocode_missing=True).
Fills missing coordinates with borough averages if Geocoding fails.
The rest of the columns with missing or null values were ignored as they were not specific to this data challenge.

Functions:
# preprocess_data(input_file, output_file, geocode_missing=False)
Purpose
This is the main function that orchestrates the preprocessing of raw 311 data. It handles missing values, validates essential columns, and saves the cleaned dataset.
Load raw data from the specified CSV file and print the dataset's initial shape and missing values.
Define a list of columns to validate and their respective actions (drop, fill, or validate_geo).
Iterate through the columns and handle missing values based on the specified action.
Print the final shape and missing values after preprocessing.
Save the cleaned data to the specified output path.
Returns a cleaned pandas.DataFrame.
# handle_missing_values(df, column, action, *args, geocode_missing=False)
Purpose
Handles missing values for a specific column based on the provided action.
Count the number of missing values in the column.
Based on the action:
drop: Drop rows with missing values in the column.
fill: Fill missing values with the specified fill value.
validate_geo: Validate latitude and longitude, either dropping, imputing, or geocoding missing values.
Returns none (modifies the DataFrame in place).
# validate_geospatial(df, geocode_missing)
Purpose
Validates the latitude and longitude columns, handling missing values either by dropping rows, imputing with borough averages, or geocoding.
Calculate the percentage of missing coordinates.
If missing coordinates are less than 1% of the dataset:
Drop rows with missing coordinates.
If geocode_missing is True:
Attempt geocoding using the incident_address column.
Otherwise:
Impute missing values using the median latitude and longitude of the respective borough.
Returns none (modifies the DataFrame in place).
# geocode_missing_coordinates(df)
Purpose
Uses the geocoder library to fill missing latitude and longitude values based on the incident_address column.
Count and print the number of missing values for latitude and longitude.
Iterate through rows with missing coordinates:
For each row, use the incident_address to fetch latitude and longitude.
If geocoding is successful, update the row with the fetched coordinates.
Print a message indicating success or failure for each row.
Print the final count of missing values after geocoding.
Returns none (modifies the DataFrame in place).

6. scripts/aggregation.py
Aggregates data by:
Date and hour (created_date_hour).
Complaint type.
Outputs the result as data/aggregated.csv.

The task explicitly suggests creating a column named created_date_hour, implying aggregation at the hour level. Following this guideline ensures consistency with the expectations of the challenge. Including minutes, seconds, and milliseconds makes the visualization extremely messy, especially for time-series plots. By grouping data into hourly intervals, patterns and trends become more apparent and easier to interpret.

7. scripts/visualization.py
Generates an interactive multi-line plot of complaint trends over time.
The visualize_data() function generates an interactive multi-line time-series plot using aggregated data. It reads the input CSV file, validates necessary columns, converts the time column to a datetime format, and creates a Plotly line chart where each line represents a different complaint_type. The plot is saved as an HTML file, allowing users to interact with it, hover over data points, and analyze complaint trends over time.

8. scripts/spatialdata_processing.py
The spatial_processing function generates an interactive choropleth map to visualize complaint counts by Neighborhood Tabulation Area (NTA). Using preprocessed 311 data and NTA shapefiles, it processes the data spatially, aggregates complaints by neighborhood, and outputs the map in an interactive HTML format using Folium. 
Key steps:
Load Preprocessed Data: Reads the cleaned data and verifies the presence of latitude and longitude columns.
Filter by Complaint Type: Extracts only the rows corresponding to the specified complaint type.
Create GeoDataFrame: Converts filtered data into a GeoDataFrame for spatial operations.
Reproject Data: Ensures spatial consistency by reprojecting the data to match the CRS of the NTA shapefile.
Spatial Join: Associates each complaint with its corresponding NTA using a spatial join.
Aggregate Counts: Aggregates complaint counts for each NTA.
Merge with NTA Boundaries: Merges the aggregated counts back into the NTA shapefile for visualization.
Reproject to WGS84: Converts the data to WGS84 (required for Folium compatibility).
Create Map:
Adds a choropleth layer to show complaint counts using color gradients.
Includes tooltips on each polygon to display neighborhood names and counts interactively.
Save Output: The resulting map is saved as an HTML file in the specified output location.
This function outputs an interactive map that allows users to explore complaint densities across NYC neighborhoods in an intuitive and visually appealing way.

Assumptions
Dynamic Dataset: The project fetches the most recent 7 days of data dynamically. Outputs may differ depending on when the script is run.
Missing Values: Rows with missing latitude and longitude (<1%) are dropped. If geocoding is enabled, missing coordinates are filled using incident_address.
Complaint Type: Choropleth map is created for specific complaint types ("HEAT/HOT WATER").

Contributor
Ushashi Podder
