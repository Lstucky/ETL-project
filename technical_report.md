## ETL-Project Technical Report

### Overview

Our objective is to gather various datasets about national parks, transform the data into workable dataframes, and load them into a SQL database. 

To do this, we found a dataset that listed all National Parks, acreage, latitude, and longitude. Then supplemented this with three other datasets. The first containing all trails within national parks and various stats pertaining to the trail; the second gave is number of visitors; the third, all animals found within the national parks.

We cleaned and normalized our data so that we could merge them into meaningful tables and load them into a SQL database.

### Sources

* National Parks Acres - https://data.world/inform8n/us-national-parks-visitation-1904-2016-with-boundaries/workspace/file?filename=All+National+Parks+Visitation+1904-2016.csv
* National Park Trails -  https://www.kaggle.com/planejane/national-park-trails
* National Park Species - https://www.kaggle.com/nationalparkservice/park-biodiversity?select=parks.csv
* National Park Visitations - https://data.world/inform8n/us-national-parks-visitation-1904-2016-with-boundaries/workspace/file?filename=All+National+Parks+Visitation+1904-2016.csv
 
### Procedures

* **Extract**
    * Each csv file was read into a Pandas DataFrame in jupyter notebook.

* **Transform**
    * We took only the columns that were necessary and renamed the columns so they were consistent across the datasets.  We used excel to save a dataset overview of which columns we initially wanted to keep and draft of possible columns to join.
    	*File - data_set_overview.xlsx
    * We reviewed our datasets by using the following methods: value_counts(), unique(), sort_values(), and describe().
    * We determined that there was some variance between the national park names across our datasets - we used Excel and the .difference() method to determine what differences we had. We then used .replace() to make minor changes in the park names to get more matches and .drop() to remove a park that was not a National Park.
        * Examples: "The" to "the"; we dropped "and Preserve"
    * We decided on our final database design by color coding our keys and merged dataframes according to the structure.
	*File - Database-design.xlsx
    * There were some National Parks that did not match across all datasets, but we decided to leave 56 parks which appeared to be the most we could leave without posing to be problematic when merging.
    * Cleaning Challenges
        * `nps_df.csv` - the dataset is from National Park Service and provides size (acres) of parks.  It had state abbreviations and we wanted the full state. We used a dictionary or conversions in conjunciton with .replace() in order to fix this.  We had to eliminate variance in park names by using a dictionary in conjunction with .replace() and .strip().
        * `AllTrails_data_nationalpark.csv` - the dataset is from the AllTrails app and provides trail information.  It had the latitude and longitude in a dictionary and we wanted the lat and long in separate columns. We ran a loop through the column and split them on the comma, adding them into lists in the loop. We needed to strip the extra characters from values so that only the numbers remained. The lat and long lists were then added as columns to the dataframe. Three columns had floats that we decided to round.  We also wanted to convert the length values from kilometers to miles. 
        * `species.csv` - the dataset is from National Park Service and provides detailed species information observed in parks.  It proved to have an error in the csv file that needed correction prior to reading into a dataframe. We found this error only while attempting to import into SQL - there was one row that did not have a closing quotation mark, causing every value to be included in one element until it got to another quotation mark. We isolated the row and viewed the csv in a notebook to remove the extraneous quotation mark.
        * `park_visitation.csv` - the dataset is already scraped from the National Park Service website and provides visitations by year.  It included number of visitations for each park from 1904 to 2016. We took only the most recent year and dropped the rest. 
    * Interesting Findings - the species dataset included a species that was marked as "extinct" - The Blue Pike fish in Cuyahoga National park.

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
The four datasets provided a challenge as the sources varied from being from the National Park Service, webscraped from the National Park Service, and webscraped from the All Trails app.  Prior to transforming the data, we spent most of our discussion on the database design.  We wanted to balance simplifying the data while keeping the potentially interesting columns of data for later analysis.  A transitive relationship was created between the following sql tables: Trails -> National_Parks -> Trail_Stats and with Trail_Stats -> National_Parks | Trails -> Species Stats.  The Parks_Trails and Parks_Species served as junction tables to help with the many-to-many relationships.  While working primarily in Pandas in Jupyter Notebooks, issues arose with some parks being identified in multiple states and some park names combined in one and not the other.  We did left joins for most of the tables.  An inner join was used when merging the one pair of datasets to prevent null values in a designated primary key column.  Final validation was done in Postgres by testing joins of the tables. 
 
