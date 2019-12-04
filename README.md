# Identifying the impact of volunteer EMS in NYC dispatch data

## Problem Statement

NYC's 911 emergency dispatch system sends out ambulances on thousands of calls every day. A scattering of volunteer ambulance companies throughout the city respond to emergency calls as well, acting to supplement the 911 ambulances and free them up for other calls. Using the EMS Dispatch dataset from NYC Open Data, this project attempts to discern the impact of volunteer EMS agencies on 911 dispatch volume and call resolution.

## Executive Summary

The NYC EMS Dispatch Data file was 2 GB in size, containing records of 8.5 million calls over 6 years, from Jan 1, 2013 to Dec 31, 2018. To reduce the volume of data and make it easier to handle, preprocessing was done in Notebook 1. Since many volunteer EMTs keep normal working hours, the major data reduction involved isolating only incidents that began between 7 PM and midnight, removing almost 80% of the original data. Additionally, rows missing either zipcodes or incident disposition codes (which indicate the final outcome of an incident, and are a focal point of this project) were deleted. All date-times were converted from `object` to `datetime` format--which was not preserved when the processed data was saved to a .csv, but the reformatting was such that when the .csv is read, using `parse_dates`, the result is date-time format in no time at all. <br>

Canceled calls consist of ~3.3% of the dataset, and with any calls canceled before arrival at the scene (since those would be more likely to be canceled by dispatch rather than the presence of another unit) removed from that subset, our final working dataframe has about 45,000 records (down from 8.5 million in the original file to 1.9 million in the pre-processed dataset). This was much easier to handle. Next we looked at the distribution of call types (think sick, difficulty breathing, cardiac arrest, etc). While there are over 100 call types that show up in the dataset, only the top 20-30 show up with any frequency, with `SICK` and `INJURY` by far at the head of the pack. Distributions of severity levels for all calls and for just the canceled calls are fairly similar, though the canceled distribution shows fewer incidents with levels 6 & 7. <br>

Time spent on-scene for a canceled call can vary widely, from seconds to hours. Both have reasonable explanations: extremely short on-scene times are likely due to the presence of another ambulance already at the scene when the 911 unit arrives, while long on-scene times are likely the result of the EMS unit staying present to assist, either another unit or with an extended event, and not needing to transport any patients at the end of it. In the case of another ambulance already being at the scene, sometimes 911 accidentally assigns duplicate units to a case, and sometimes a volunteer unit beats the 911 unit to the scene. Even when another unit is already on-scene, both sets of EMTs will often check on the patient together, in case the extra assistance is necessary--for things like patient treatment or carrying a patient down several flights of stairs. Most cunits spend under 10 minutes on-scene in the case of a canceled call. <br>

There does seem to be a relationship between overall number of canceled calls per zipcode and the presence of volunteer EMS units in the area. The highest spikes in canceled call volume are in zipcodes that do have dedicated VACs, though some are more active than others. When this distribution is mapped, it becomes more evident, while anomalous zipcodes also become more apparent--parks and other low-population, higher-traffic areas tend to have fairly high cancellation rates as well. When we look at total call volume per capita, we start to see the influence of the biggest volunteer ambulance agency in NYC, but one which specifically doesn't use the 911 system at all. Hatzolah has its own emergency phone number, and handles such a high volume of calls that there seems to be a visible decrease in per capita calls in the areas where they operate, because fewer people are calling 911 for medical emergencies--they're calling Hatzolah instead. <br>

k-modes clustering was attempted to see if separable patterns or combinations of features were evident in the data--such as maybe a higher frequency of certain call types in certain zipcodes, or something similar. Unfortunately, the clustering algorithm picked up on a certain feature type-- `INCIDENT_DISPATCH_AREA` -- and stuck with it. Other features had many more categories, and given that k-modes actually works by picking out modes, a feature with fewer categories will have each category appear more frequently than would be the case in a feature with more options. This may have lead to a self-selection of the modes.

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
|VALID_INCIDENT_RSPNS_TIME_INDC | Indicates that the components comprising the calculation of the INCIDENT_RESPONSE_SECONDS_QY are valid|
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

NYC operates hundreds of ambulances across the 5 boroughs and sends them out on thousands of calls every day. EMTs working the 911 system work long, often busy hours, and when volunteer EMTs are available and capable of caring for a given patient, the 911 EMTs will often be willing to hand over the patient because it makes them available for the next call (and means one less report to write). While this does happen, the question was whether it happens frequently enough, or in a consistent enough pattern, to be visible in the 911 EMS dispatch data, which is scrubbed of any personal details, including any location more specific than zipcode, or any identification of the 911 unit assigned to the call. And just from rates of canceled calls across zipcodes, there did seem to be some evidence of volunteer EMS activity in the data--cancellation rates were higher in areas with active volunteer ambulance corps.

In terms of the modeling that was done, k-modes defines clusters based on the number of matching categories between data points. As `INCIDENT_DISPATCH_AREA`, of all the features used in k-modes, is the one with the smallest number of (and most evenly distributed) categories, it is logical that it would be the primary way that individual calls are assigned to clusters. The results weren't particularly meaningful, but a superior model was not identified.

## Future Work

* Further investigation of time spent on-scene during canceled calls--and during other disposition types as well
* Reduce call types to ~top 20, and see if this affects the results of k-modes
* More work with k-modes, to see if it's able to produce more meaningful results, depending on relative proportions of different features
* Additional exploration of the dataset

## Resources
* [NYC EMS agencies](https://www.nycremsco.org/wp-content/uploads/2017/11/EMS_Agency_Listing.pdf)
* [NYC EMS codes](https://www.n2nov.net/emscodes.html)