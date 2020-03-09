# NYC_Complaints_311
  - ## The people of New Yorker use the 311 system to report complaints about the non-emergency problems to local authorities. Various agencies in New York are assigned these problems. The Department of Housing Preservation and Development of New York City is the agency that processes 311 complaints that are related to housing and buildings. In the last few years, the number of 311 complaints coming to the Department of Housing Preservation and Development has increased significantly. Although these complaints are not necessarily urgent, the large volume of complaints and the sudden increase is impacting the overall efficiency of operations of the agency. Therefore, the Department of Housing Preservation and Development needs help on managing the large volume of 311 complaints they are receiving every year.
  - ## This project involves two datasets: one is [311 complaint dataset](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9), the other is [PLUTO dataset for housing](https://data.cityofnewyork.us/City-Government/Primary-Land-Use-Tax-Lot-Output-PLUTO-/xuk2-nczf)
  - ## Download the part of "311 complaint dataset" using SODA API, shown as below
```python 
import pandas as pd
from sodapy import Socrata  # need pip install sodapy first

# Unauthenticated client only works with public data sets. Note 'None'
# in place of application token, and no username or password:
#client = Socrata("data.cityofnewyork.us", None)

#select=created_date,unique_key,complaint_type,incident_zip,incident_address,street_name,address_type,city,resolution_description,borough,latitude,longitude,closed_date,location_type,status

MyAppToken = 'your_token'    #token copy from NYHousingComplaint app in opendata.socrata.com

# Example authenticated client (needed for non-public datasets):
client = Socrata('data.cityofnewyork.us', 
                 MyAppToken, 
                 username="your_id",   #my account at SODA developer settings
                 password="your_pass")     #use own pass

# First 10 million results, returned as JSON from API / converted to Python list of
# dictionaries by sodapy.
# define query statement: select columns where agency is Housing Preservation and Development (HPD)
query = """
select 
  unique_key,created_date,closed_date,complaint_type,status,resolution_description,location_type,borough,incident_zip,incident_address,street_name,address_type,city,latitude,longitude
where
  agency = 'HPD'
limit 10000000
"""
 
# "https://data.cityofnewyork.us/resource/erm2-nwe9.json"  
# use text between resource and .json as identifier for client.get 
results = client.get("erm2-nwe9", 
                     query=query)

# Convert to pandas DataFrame
results_df = pd.DataFrame.from_records(results)

# write the dataframe results to csv file
# pwd: 'E:\\New_Career\\DS_Statistics\\Python_DS_IBM\\Capstone'
results_df.to_csv('HPDComplaint.csv')

# check data
pd.read_csv('HPDComplaint.csv').head()
```
  - ## Analysis of these dataset will answer the following questions to help the agency:
    - ### 1. Which type of complaint should the Department of Housing Preservation and Development of New York City focus on first?
    - ### 2. Should the Department of Housing Preservation and Development of New York City focus on any particular set of boroughs, ZIP codes, or street (where the complaints are severe) for the specific type of complaints you identified in response to Question 1?
      - #### The analysis for the first two questions can be found in [HPDComplaintsAnalysis.ipynb](https://github.com/OnePiece101/NYC_Complaints_311/blob/master/HPDComplaintsAnalysis.ipynb)
    - ### 3. Does the Complaint Type that you identified in response to question 1 have an obvious relationship with any particular characteristic or characteristics of the houses or buildings?
    - ### 4. Can a predictive model be built for a future prediction of the possibility of complaints of the type that you have identified in response to question 1?
      - #### The analysis for the last two questions can be found in [Question3-4_Relationship_HousingCharacteristics_Complaints.ipynb](https://github.com/OnePiece101/NYC_Complaints_311/blob/master/Question3-4_Relationship_HousingCharacteristics_Complaints.ipynb)
  - ## Concluding Remarks I&II:
    * ### The top priority of HPD to solve heating-related complaints should be on Grand Concourse of zip 10467/10458 in Bronx borough.
    * ### Next, HPD should focus on heating-related complaints in Brooklyn borough, especially Liden Blvd/Ocean Ave/Bedford Ave with zip 11226. 
    * ### In addition, HPD should also pay attention to the heating-related complaints from Broadway with zip 10031/10040 in Manhattan.
  - ## Conclusion III:
    * ### In this section, relationship between top 5 types of complaints and various housing characteristics was investigated.
    - ### Feature selection was performed to select most important features that are correlated to the types of complaints, including the year and month created, location (GPS coords) of the house, building floor area, lot area, lot and building depth, the year bulit and altered.
    * ### The best model was found to be random forest, with the roc_auc score of 0.85, and the model could be used to predict the 5 types of complaints in NYC.
    * ### The NYC government could use the features mentioned above to predict future types of complaints including general construction, heating, plumbing, and unsanity condition, and then set a series plans to improve the community.
  - ## Conclusion IV: 
    * ### In this section, relationship between the number of heating-related complaints (identified in Q1&Q2) and various housing characteristics was investigated.
    - ### Feature selection was performed to select most important features that are correlated to the number of heating-complaints, including residential area, number of floors, building depth, the year case was created, building area, and lot area.
    * ### The best model was found to be support vector machine, with the R2 score of 0.11.
    * ### The R2 score for the best model is pretty low, so it is hard to predict the number of heating-related complaints with current features, and it is recommended to collect more features for prediction. However, although weakly correlated, the government could also work on the features mentioned above to improve the community living condition.
