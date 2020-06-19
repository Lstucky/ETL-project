## ETL-Project Technical Report

### Overview

Our objective is to gather various datasets about national parks, transform the data into workable dataframes, and load them into a SQL database. 

To do this, we found a dataset that listed all National Parks, acreage, latitude, and longitude. Then supplemented this with three other datasets. The first containing all trails within national parks and various stats pertaining to the trail; the second gave is number of visitors; the third, all animals found within the national parks.

We cleaned and normalized our data so that we could merge them into meaningful tables and load them into a SQL database.

### Sources

* National Parks - https://data.world/inform8n/us-national-parks-visitation-1904-2016-with-boundaries/workspace/file?filename=All+National+Parks+Visitation+1904-2016.csv
* National Park Trails -  https://www.kaggle.com/planejane/national-park-trails
* National Park Species - https://www.kaggle.com/nationalparkservice/park-biodiversity?select=parks.csv
* National Park Visitations - https://data.world/inform8n/us-national-parks-visitation-1904-2016-with-boundaries/workspace/file?filename=All+National+Parks+Visitation+1904-2016.csv

### Procedures

* **Extract**
    * Each csv file was read into a Pandas DataFrame in jupyter notebook.

* **Transform**
    * We took only only the columns that were necessary and renamed the columns to that they were consistent across the datasets.
    * We reviewed our datasets by using value_counts() and unique() methods.
    * We determined that there was some variance between the national park names across our datasets - we used Excel and the .difference() method to determine what differences we had. We then used .replace() to make minor changes in the park names to get more matches.
        * Examples: "The" to "the"; we dropped "and Preserve"
    * We decided on our final database design and merged dataframes according to the structure.
    * There were some National Parks that did not match across all datasets, but we deicded to leave as many as we could that did not pose problematic when merging.
    * Cleaning Difficulties
        * `nps_df.csv` - this dataset had state abbreviations and we wanted the full state. We used a dictionary or conversions in conjunciton with .replace() in order to fix this.
        * `AllTrails_data_nationalpark.csv` - this dataset had the latitude and longitude in a dictionary and we wanted the lat and long in seperate columns. We ran a loop through the column and split them on the comma, adding them into lists in the loop. We needed to strip the extra characters from values so that only the numbers remained. The lat and long lists were then added as columns to the dataframe. We also wanted to convert the length values from meters to miles. 
        * `species.csv` - This dataset proved to have an error in the csv file that needed correction prior to reading into a dataframe. We found this error only while attempting to import into SQL - there was one row that did not have a closing quotation mark, causing every value to be included in one element until it got to another quotation mark. We isolated the row and viewed the csv in a notebook to remove the extraneous quotation mark.
        * `park_visitation.csv` - This dataset included number of visitations for each park from 1904 to 2016. We took only the most recent year and dropped the rest. 
    * Interesting Findings - the species dataset included a species that was marked as "extinct" - The BLue Pike fish in Cuyahoga National park.

* **Load**
    * We created the tables to match our final dataframes, with primary and foreign keys. 
    * Once our datasets were saved to an `Outputs` directory, we imported them into PostgeSQL.

### Steps to Replicate
    1. Run each cleaning jupyter notebook 
    2. Run merging juypter notebook
    3. Create NationalParks dataframe in PostgeSQL
    4. Run schema to create tables
    5. Import each datasets from the `Outputs` directory into each table
    6. Run SQL analysis

### Final Thoughts
    * 
