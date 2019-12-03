# Identifying the impact of volunteer EMS in NYC dispatch data

## Problem Statement

NYC's 911 emergency dispatch system sends out ambulances on thousands of calls every day. A scattering of volunteer ambulance companies throughout the city respond to emergency calls as well, acting to supplement the 911 ambulances and free them up for other calls. Using the EMS Dispatch dataset from NYC Open Data, this project attempts to discern the impact of volunteer EMS agencies on 911 dispatch volume and call resolution.

## Executive Summary


### Workflow
* [Data Processing and Reduction](./code/data_processing_and_reduction.ipynb)
* 

### DATA
* [NYC EMS Dispatch Data](https://data.cityofnewyork.us/Public-Safety/EMS-Incident-Dispatch-Data/76xm-jjuj)
 - Stored off-line because dataset too large for GitHub to comfortably accommodate
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


## Conclusions & Future Work
