# Identifying the impact of volunteer EMS in NYC dispatch data

## Problem Statement

NYC's 911 emergency dispatch system sends out ambulances on thousands of calls every day. A scattering of volunteer ambulance companies throughout the city respond to emergency calls as well, acting to supplement the 911 ambulances and free them up for other calls. Using the EMS Dispatch dataset from NYC Open Data, this project attempts to discern the impact of volunteer EMS agencies on 911 dispatch volume and call resolution.

## Executive Summary

The NYC EMS Dispatch Data file was 2 GB in size, containing records of 8.5 million calls over 6 years, from Jan 1, 2013 to Dec 31, 2018. To reduce the volume of data and make it easier to handle, preprocessing was done in Notebook 1. Since many volunteer EMTs keep normal working hours, the major data reduction involved isolating only incidents that began between 7 PM and midnight, removing almost 80% of the original data. Additionally, rows missing either zipcodes or incident disposition codes (which indicate the final outcome of an incident, and are a focal point of this project) were deleted. All date-times were converted from `object` to `datetime` format--which was not preserved when the processed data was saved to a .csv, but the reformatting was such that when the .csv is read, using `parse_dates`, the result is date-time format in no time at all. <br>
Canceled calls consist of ~3.3% of the dataset, and with any calls canceled before arrival at the scene (since those would be more likely to be canceled by dispatch rather than the presence of another unit) removed from that subset, our final working dataframe has about 45,000 records (down from 8.5 million in the original file to 1.9 million in the pre-processed dataset). This was much easier to handle. Next we looked at the distribution of call types (think sick, difficulty breathing, cardiac arrest, etc). While there are over 100 call types that show up in the dataset, only the top 20-30 show up with any frequency, with `SICK` and `INJURY` by far at the head of the pack. 

### Workflow
* [1. Data Processing and Reduction](./code/data_processing_and_reduction.ipynb)
* [2. Dispatch EDA and Clustering](./code/dispatch_EDA_and_clustering.ipynb)

### DATA
* [NYC EMS Dispatch Data](https://data.cityofnewyork.us/Public-Safety/EMS-Incident-Dispatch-Data/76xm-jjuj)
    * Stored off-line because dataset too large for GitHub to comfortably accommodate
* [Reduced/Processed Dispatch Data--used in EDA notebook](https://drive.google.com/file/d/1ry0yvvLWxXXjHFxiUqGnR_Y-OwtTM0dp/view?usp=sharing)
* [Zipcode Boundaries from NYC Open Data](https://data.cityofnewyork.us/Business/Zip-Code-Boundaries/i8iw-xf4u)

| Variable | Description |
| --- | --- |
|CAD_INCIDENT_ID | An incident identifier comprising the julian date and a 4 character sequence number starting at 1 each day|
|INCIDENT_DATETIME | The date and time the incident was created in the dispatch system|
|INITIAL_CALL_TYPE  | The call type assigned at the time of incident creation|
|INITIAL_SEVERITY_LEVEL_CODE | The segment(priority) assigned at the time of incident creation|
|FINAL_CALL_TYPE  | The call type at the time the incident closes|
|FINAL_SEVERITY_LEVEL_CODE | The segment(priority) assigned at the time the incident closes|
|FIRST_ASSIGNMENT_DATETIME | The date and time the first unit is assigned|
|VALID_DISPATCH_RSPNS_TIME_INDC | Indicates that the components comprising the calculation of the DISPATCH_RESPONSE_SECONDS_QY are valid|
|DISPATCH_RESPONSE_SECONDS_QY | The time elapsed in seconds between the incident_datetime and the first_assignment_datetime|
|FIRST_ACTIVATION_DATETIME | The date and time the first unit gives the signal that it is enroute to the location of the incident|
|FIRST_ON_SCENE_DATETIME | The date and time the first unit signals that it has arrived at the location of the incident|
|VALID_INCIDENT_RSPNS_TIME_INDC | Indicates that the components comprising the calculation of theINCIDENT_RESPONSE_SECONDS_QY are valid|
|INCIDENT_RESPONSE_SECONDS_QY | The time elapsed in seconds between the incident_datetime and the first_on_scene_datetime|
|INCIDENT_TRAVEL_TM_SECONDS_QY | The time elapsed in seconds between the first_assignment_datetime and the first_on_scene_datetime|
|FIRST_TO_HOSP_DATETIME | The date and time the first unit gives the signal that it is enroute to the hospital|
|FIRST_HOSP_ARRIVAL_DATETIME | The date and time the first unit signals that it has arrived at the hospital|
|INCIDENT_CLOSE_DATETIME | The date and time the incident closes in the dispatch system|
|HELD_INDICATOR | Indicates that for some reason a unit could not be assigned immediately|
|INCIDENT_DISPOSITION_CODE | A code indicating the final outcome of the incident. See incident dispositions|
|BOROUGH | The borough of the incident location|
|INCIDENT_DISPATCH_AREA | The dispatch area of the incident|
|ZIPCODE | The zip code of the incident|


## Conclusions

k-modes defines clusters based on the number of matching categories between data points. As `INCIDENT_DISPATCH_AREA`, of all the features used in k-modes, is the one with the smallest number of (and most evenly distributed) categories, it is logical that it would be the primary way that individual calls are assigned to clusters.  

## Future Work

* Further investigation into on-scene times, possibly median time per zipcode
* Reduce call types to ~top 20, and see if this reduces the variability in k-modes
* 
