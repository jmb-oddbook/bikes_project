# Capital Bike Share Project

This was the third project of CAB. It focused on data wrangling, exploratory data analysis, and working with time series.

>With environmental issues and health becoming trending topics, the usage of bicycles as a mode of transportation has gained popularity in recent years. To encourage bike usage, cities across the world have successfully implemented bike-sharing programs. Under such schemes, riders can rent bicycles using manual/automated kiosks spread across the city for defined periods. In most cases, riders can pick up bikes from one location and return them to any other designated place.<br />
>Bike riders reduce traffic congestion as they form a valid substitution for cars on short/medium trips, contribute to the use of public transport by providing effective last-mile connectivity, and simply take up less space on the road. The characteristics of data being generated by these systems also make an attractive option for the researchers who can try to understand traffic routes in a city." [CAB]

Note that data from Capital Bike Share will not be uploaded to GitHub due to size, please help yourself to the data from the original Capital Bike Share repository. Links given below.


## Part 1 -- Data wrangling

### Data sets
The following data sets were the core of this project:
* Bike sharing data set : This dataset comes from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/bike+sharing+dataset) and has been augmented by weather, seasonal, and calendric data. It contains the years 2011 and 2012.
* Trip history data : Can be downloaded for the years 2010 up to the last month (here: April 2022) from [Capital Bikeshare](https://ride.capitalbikeshare.com/system-data) *sub* Trip History Data.

These data sets were used for supplemental information and to expand the scope of the analysis:
* Station information data set : Can be downloaded as json from [Capital Bikeshare](https://ride.capitalbikeshare.com/system-data) *sub* Real Time Data.<br />
*Note: there is no information when the respective station was opened or if the station was moved during it's existence.*
* Weather data [lorem ipsum]

The Capital Bikeshare System Data website also offers supplementary material such as member surveys and summaries.

### Bike sharing data set
#### Attributes
Description of the fields in the two data sets hour.csv and day.csv supplied by the UCI Archive. There are discrepancies between the text displayed on the website of the data set and the readme file in the data set's zip file. Comments in *italics* by me, JMB.
* **instant** : record index
* **dteday** : date *yyyy-mm-dd*
* **season** : season (1-spring, 2-summer, 3-fall, 4-winter) *this is according to the readme file, the website writes (1-winter, 2-spring, 3-summer, 4-fall)*
* **yr** : year (0 - 2011, 1 - 2012)
* **mnth** : month (0 - *Jan* to 12 - *Dec*)
* **hr** : hour (0 to 23) [only in *hour.csv*]
* **holiday** : whether the day is a holiday or not (extracted from http://dchr.dc.gov/page/holiday-schedule)
* **weekday** : day of the week *(0 - Sunday, 1 - Monday, ..., 6 - Saturday ; why would you start with a Sunday??)*
* **workingday** : if day is neither weekend nor holiday it s 1, otherwise it is 0
* **weathersit** : **1** (Clear, few clouds, partly cloudy), **2** (Mist + cloudy, mist + broken clouds, mist + few clouds, mist), **3** (Light snow, light rain + thunderstorm + scattered clouds, light rain + scattered clouds), **4** (Heavy rain + ice pellets + thunderstorm + mist, snow + fog)
* **temp** : Normalized temperature in Celsius. *Website: The values are derived via (t-t_min)/(t_max-t_min), t_min=-8, t_max=+39 (only in hourly scale); Readme states: divided to 41 (max)*
* **atemp** : Normalized feeling temperature in Celsius. *Website: The values are derived via (t-t_min)/(t_max-t_min), t_min=-16, t_max=+50 (only in hourly scale); Readme states: divided to 50 (max)*
* **hum** : Normalized humidity. The values are divided to 100 (max)
* **windspeed** : Normalized wind speed. The values are divided to 67 (max)
* **casual** : count of casual users
* **registered** : count of registered users
* **cnt** : count of total rental bikes including both casual and registered


#### Issues found in the data
(1) Mapping of seasons:<br />
The readme file states 1 = spring and the website states 1 = winter, putting March either at the end (3rd month) of winter or at the end of spring. Both data sets (day.csv and hour.csv) have season #1 ranging from the 21 Dec to 21 Mar, thus mapping seasons astronomically. (Other ways of mapping seasons are according to religious conventions, meteorologically, or phaenologically according to the development of the plants).<br />
Nevertheless, season #1 ought to be "winter". To better compare meteorological statistics and for climate comparisons the World Meteorologial Organization (WMO) lets all seasons start at the beginning of a month. For the WMO spring thus begins on the 1st of March.<br>
In this data analysis the recommendations of the WMO were followed, and the seasons were remapped based on the month recorded in the instance.

(2) Missing instances or ranges in hour.csv:<br />
There are missing instances or ranges even though the weather site used by the researchers offers weather for these instances.<br />
The missing data lies with the data set from Capital Bikes. There is data missing in the trips data set for these instances / ranges.

This carries over into the day.csv data set as the daily records were computed using the hourly records, missing instances and all.

(3) Strange weather:<br />
An example: checking for Hurricane Sandy, which I'm sure we can agree to call an 'extreme weather phenomenon'. State of emergency was declared for the US east coast Oct 26, Oct 29-30 all government buildings closed and DC Metro services suspended.<br />
The weather given is: clear (1), misty (2), and light rain (3).<br />
Personally, I'd rate a hurricane somewhat higher than 'light rain'.<br />
So where did the researchers get the weather data from for this period? The weather source cited (freemeteo.com) has no data between 2012-09-28 and 2012-11-28, but according to NOAA the weather station at Reagan National was transmitting data.

Most optimal solution would be to get the data directly from NOAA and remap it to the bike ride data set. This is a bit overkill for this project, maybe if we used all available ride data from 2010 to 2022. => later


### Trip history data
#### Attributes
Each data set of trip history data contained the same 8 fields. We have replaced the spaces in the names with underscores:
* **duration** : how long the trip lasted in seconds. Renamed to **duration_sec**
* **start_date** : the date and time the trip began, given as yyyy-mm-dd hh:mm:ss
* **end_date** : the date and time the trip ended, given as yyyy-mm-dd hh:mm:ss
* **start_station_number** : the station ID at which the trip began, a 5 digit integer number
* **start_station** : the name of the station at which the trip began
* **end_station_number** : the station ID at which the trip ended, a 5 digit integer number
* **end_station** : the name of the station at which the trip ended
* **bike_number** : the ID of the bike taken on the trip, a 5 digit integer number prefixed by W
* **member_type** : if the borrower was a 'registered' member or a 'casual' user
To more easily identify the data sets when concatenated an additional field **year** was added.


#### Issues found in the data
(1) Unknown borrowers:<br />
There are 21 records of trips where the borrower was marked as 'Unknown'. In the hourly data set these records have not been included in the calculation of total riders. These records were dropped.

(2) Missing bike numbers:<br />
There are 4801 records that are missing a valid bike number, instead they show what appears to be a hexadecimal code, e.g. ?(0XFFFFFFFFAAC5A4C0). There are 16 unique versions of this code. I tried decoding but could not gain any meaningful text. These records were dropped.


## Part 2 -- Exploratory data analysis
There are 194 unique start and end stations. No empty values.

There are 136 records that are either stalled transactions or of users that reconsidered. Criteria for identifying a stalled transaction were identical start and end stations and the duration of the trip is 60 seconds or less. These records were removed.

I also checked for trips that are 60 seconds or less and have a different start and stop station. There are 27 of these. In all except one record the stations lie very close together. Perhaps the borrower changed their mind? And in one record (ID 507845) the borrower discovered instantaneous matter transmission and cycled 3.5 miles in 1 minute (google maps gives the time for a car to travel this street distance as 12 minutes - this *is* D.C.). These 27 records were removed.

There are 6 records that have trips that are longer than 23 hours. Either the customer forgot to book the bike back in, the endpoint did not accept the transaction, or something else happened. The company gives a fine of $1200 for customers not returning a bike within 24 hours, so there is a strong incentive for the borrower to make sure to bring the bike back. These 6 records were removed.


## Part 3 -- Visualisations

## Part 4 -- Potential KPIs

## Part 5 -- Presentation

## Appendix 1 -- Visualisation with Tableau

## Appendix 2 -- ML: Forecast of bike demand

## Appendix 3 -- Forecast with Facebook (Meta?) Prophet

## Appendix 4 -- Mapping bike stations (and rides?)
### Station information data set
#### Attributes
Dropped the fields: 'electric_bike_surcharge_waiver', 'rental_uris', 'external_id', 'eightd_station_services', 'has_kiosk', 'rental_methods', 'station_type', 'eightd_has_key_dispenser'

Kept the attributes:
* **region_id** : unique ID of the region where the station is installed
* **legacy_id** : legacy ID of the station
* **station_id** : station ID
* **has_kiosk** : does the station have a kiosk for the borrower to interact with (True, False)
* **capacity** : number of bike docks the station has
* **lon** : longitude of station. Renamed to 'longitude'
* **name** : name of the station. Describes the corner of two streets where the station is installed
* **lat** : latitude of the station. Renamed to 'latitude'
* **short_name** : unique 5 digit identifier of a station
Added the field: **region_name** which contains the name of the region associated with the region_id

[First attempt](geomap.ipynb)