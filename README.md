# Truck Detection Challenge

**Contents**:

- [Smarthop Challenge](#smarthop-challenge)
  - [Input](#input)
  - [Goal](#goal)
  - [Expected Output](#expected-output)
- [Set up the Challenge Environment](#set-up-the-challenge-environment)
  - [Define Scaffold](#define-scaffold)
  - [Set up Local Environment](#set-up-local-environment)
  - [Set up and Authenticate `gcloud` CLI](#set-up-and-authenticate-gcloud-cli)
- [Challenge Steps](#challenge-steps)
- [Develop Location and Time Data Analysis](#develop-location-and-time-data-analysis)


Extract insights and data from unstructured data like images 
or PDFs and GPS data

## Input

* GPS data points (JSON encoded) of a trucks trip

* BOL file (document that states what and where to pickup and 
where to deliver the cargo)

## Goal

Detect if the Truck passed the Pickup and Delivery Points and 
for how long it was there at each stop (if any)

## Expected Output

* A notebook with the answers about the Truck trip

* The output in text and structured json to model entities

# Set up the Challenge Environment

## Define Scaffold

Create scaffold files for the project:
  
* `Makefile`: to automate building steps

* `requirements.txt`: with the required dependencies for the 
project

* `smarthop-challenge.ipynb`: notebook with the code to get and 
analyse truck data from PDF and GPS

* `GPS_BOL_Dataset/`: Directory with the project input data. 
Contains:
  
  - `GPS_BOL_Dataset/3e13d38e-a9b6-4bd6-b42d-7af3df2977af.pdf`: 
  BOL pdf file
  
  - `GPS_BOL_Dataset/hopbackend.booked_trip_tracking_locations 2.json`: 
  GPS data in `json` format

## Set up Local Environment

* Create Python Virtual Environment for the project

* Run `make install` to install the project dependencies

## Set up and Authenticate `gcloud` CLI 

* Install and configure `gcloud`: 
https://cloud.google.com/sdk/docs/install
  
  ```
  > gcloud auth list
  ```
  ```
          Credentialed Accounts
  ACTIVE  ACCOUNT
  *       sebastian.caceres.g@gmail.com

  To set the active account, run:
      $ gcloud config set account `ACCOUNT`
  ```

* Create a project in Cloud Console and set a compute zone: 
  
  ```
  > gcloud config list
  ```
  ```
  [accessibility]
  screen_reader = False
  [compute]
  region = us-east1
  zone = us-east1-c
  [core]
  account = sebastian.caceres.g@gmail.com
  disable_usage_reporting = False
  project = angular-compass-421116

  Your active configuration is: [sebastiancg]
  ```

* Create a bucket in Cloud Storage to place the converted image: 
  
  ```
  > gcloud storage buckets create gs://data-extraction-vision --project=angular-compass-421116
  ```
  ```
  > gcloud storage ls
  ```
  ```
  gs://data-extraction-vision/
  ```

* Add `Service Account Token Creator` role to the Owner account

* Create a Service Account with the `Service Account User` role:
  
  ```
  gcloud iam service-accounts list
  ```
  ```
  DISPLAY NAME  EMAIL                                                        DISABLED
  vision-admin  vision-admin@angular-compass-421116.iam.gserviceaccount.com  False
  ```

  ```
  gcloud iam service-accounts get-iam-policy vision-admin@angular-compass-421116.iam.gserviceaccount.com
  ```
  ```
  bindings:
  - members:
    - serviceAccount:vision-admin@angular-compass-421116.iam.gserviceaccount.com
    role: roles/iam.serviceAccountUser
  etag: BwYiZpzQ2Zw=
  version: 1
  ```

* Use the Owner account to impersonate the Service Account:
  
  ```
  gcloud auth application-default login --impersonate-service-account vision-admin@angular-compass-421116.iam.gserviceaccount.com
  ```

* Enable Google Cloud APIs:
  
  - Gemini API: https://ai.google.dev/gemini-api/docs
  
  - Geodecoding API: https://developers.google.com/maps/documentation/geocoding/overview

  - Distance Matrix API: https://developers.google.com/maps/documentation/distance-matrix/overview

* Create an API Keys:
  
  - To access Google Maps services: Geodecoding and Distance 
  Matrix APIs: `'GOOGLE_CLOUD_API_KEY'`

  - To access Gemini API: `'GOOGLE_AI_STUDIO_API_KEY'`

  - To access OpenAI API: https://platform.openai.com/: 
  `'OPENAI_API_KEY'`

* Add these API key to your environment variables

# Challenge Steps

1. Explore the structure and components of the input data

2. Import libraries and set Google variables

3. Load GPS data using `json` dependency

     * Define how to get location (lat, long) and date data from 
     GPS data

4. Define how to get the distances between two locations 
   
     * Option A: Using Google Maps's Distance Matrix API
      
     * Option B: Using the `geopy` Dependency (used)

5. Extract Pickup and Delivery addresses from BOL

     * Convert BOL file from `.pdf` to `.jpg` format 
     
     * Extract Addresses from Image/PDF file:

       - Option A: Use **OpenAI API**
       
       - Option B: Use **Google's Gemini API**

6. Obtain Pickup and Delivery coordinates using Google Maps's
Geodecoding API

1. Check if Trucks passed near Pickup and Delivery points

2. Check the time Trucks stayed at Pickup and Delivery points

3. Present Analysis Results

# Develop Location and Time Data Analysis

The Python Notebook 
[smarthop-challenge.ipynb](project-results/smarthop-challenge.ipynb) 
contains the code with the analysis results.

Final analysis results are also saved in the 
[analysis_results.json](project-results/analysis_results.json) 
file.
