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
```plaintext
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

## How to Run the Project
To execute the pipeline, simply run:
python main.py from your teminal or bash

## Outputs Generated
- Raw Data: data/raw.csv
- Preprocessed Data: data/preprocessed.csv
- Aggregated Data: data/aggregated.csv
- Interactive Plot: data/interactive_visualization.html
- Interactive Choropleth Map: data/interactive_choropleth_plotly.html

## Script Details
main.py
The entry point of the project orchestrates all steps:

Sets up the data directory.
Fetches raw data from the NYC 311 API.
Preprocesses the raw data.
Aggregates data for analysis.
Generates an interactive plot of complaint trends.
Creates an interactive choropleth map for a specific complaint type.
etl.py
Fetches NYC 311 service request data for the last 7 days. Saves the raw data as data/raw.csv.

utils.py
Contains utility functions, such as creating the data directory.

data_preprocessing.py
Preprocesses raw data, handling missing values, geocoding, and validation. Outputs data/preprocessed.csv.

aggregation.py
Aggregates data by created_date_hour and complaint_type. Outputs data/aggregated.csv.

visualization.py
Creates an interactive multi-line plot of complaint trends over time. Outputs data/interactive_visualization.html.

spatialdata_processing.py
Generates an interactive choropleth map of complaints by neighborhood. Outputs data/interactive_choropleth_plotly.html.

Assumptions
Dynamic Dataset: Fetches data for the most recent 7 days dynamically, ensuring up-to-date insights.
Missing Values: Handles missing coordinates by dropping rows (<1%) or imputing using borough averages.
Complaint Type: The choropleth map focuses on specific complaint types, e.g., "HEAT/HOT WATER."

