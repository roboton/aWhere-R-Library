# aWhere API R Package for Field-Based Use Cases

This is the primary method for accessing the aWhere platform, and with this approach you register Field Locations with the platform and can add planting data to each field to more easily monitor agronomics and track models. If your work is aware of specific field locations and tracking specific growing seasons, this is the appropriate approach to use. 

In the aWhere platform you will register Field Locations with the `create_field()` function. You'll give each location a unique ID of your choosing and then use that ID to reference the location in other API calls. 

Optionally, you can also create Plantings which carry the crop and growing season information, and that data is used to more easily calculate seasonal derived data such as accumulations for a growing season. Other APIs, such as models (for which we don't yet have R functions), require the use of Planting records. 

_Note: These functions will only return metric units_


## Usage

In order to use any of the functions in this package, or the APIs in general, you will need API credentials provided by aWhere. These keys are used to generate an Access Token, which is then passed to each API to authenticate the request. This R package will handle token generation and usage automatically, but you will need to get API keys first. 

To get API keys, follow these instructions at the [aWhere Developer Community](http://developer.awhere.com/start). Note that normally the API Key and Secret are made up of random characters. 


### Using Credentials Directly

Prior to using of the any other functions, you'll need to use `get_token()` function to generate a token. Pass the API Key and API Secret from aWhere as the two parameters to this function. For example: 

    get_token("JFKL24JF290FJSKAFDF","jf0afsd9af0a"); 

You could also save the key and secret as variables. This function saves the token to a direct child of the base environment for use by any other aWhere function. 

### Loading Credentials from a Saved File

An alternative approach is to save your credentials to an external text file and use the `load_credentials()` function to load them into the environment and generate a token. 

First, create a text file on your computer. Enter your API Key on the first line, and your Secret on the second. Save the file and note the complete path to it. For example, this might be `C:\Users\USERNAME\Desktop\credentials.txt`. 

In R, supply the path to the credentials file to the function: 

    load_credentials("C:\Users\USERNAME\Desktop\credentials.txt")

This function invokes `get_token()` so no further work is needed to generate an Access Token.



## API Functions Available 

**Contents**

* Field Location
	* Create a Field
	* Get Field Information
	* Update a Field
* Plantings
	* Create a Planting
	* Get a Planting
	* Update a Planting
	* Delete a Planting
* Weather 
	* Observations (Daily History)
	* Forecast
	* Norms 
	* Current Conditions
* Agronomics
	* Agronomic Values (Daily History)
	* Norms


	
___________________________________________________________________________
---------------------------------------------------------------------------

### Field Locations

By default, aWhere's APIs use Field Locations to reference the locations you wish to request data for. This is most common for tracking agricultural values over a growing season. Learn more at the [aWhere Developer Community](http://developer.awhere.com/api/reference/fields-plantings). 


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Create a Field 

Use this function to create a Field Location in the aWhere Platform. You'll use the ID for this field when referencing the location in other functions. [API Documentation](http://developer.awhere.com/api/reference/fields/create-field)


##### Function Signature

    create_field(field_id, latitude, longitude, farm_id, field_name, acres)

##### Parameters

* `field_id` _(character)_
	* An ID you use to reference the field location in all other functions.
	* Can be any combination of letters, numbers, dashes, and underscores up to 50 characters
	* Required
* `latitude` _(numeric)_
	* The latitude of the locations' centroid, in decimal notation
	* Required
* `longitude` _(numeric)_
	* The longitude of the location's centroid, in decimal notation
	* Required
* `farm_id` _(character)_ 
	* An ID that references the farm or entity that owns or runs the field location.
	* Can be any combination of letters, numbers, dashes, and underscores up to 50 characters
	* Required
* `field_name` _(character)_
	* A name for the field 
	* Can be any combination of letters, numbers, dashes, underscores, and spaces
	* Optional 
* `acres` _(numeric)_
	* The size of the location in acres
	* Optional, but may be required by your contract with aWhere 

##### Example

    create_field("field456","40.8282","-100.5795","farmA","Some Field Location","100")

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Get Field Information

This function will return the field information saved in the aWhere Platform. [API Documentation](http://developer.awhere.com/api/reference/fields/get-fields)

##### Function Signature

    get_fields(field_id)

##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required

##### Example

    get_fields('field123')

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Update Field Information

If you want to change certain properties about the Field Location, use this function. Note that at this time only the name or farm ID can be changed. [API Documentation](http://developer.awhere.com/api/reference/fields/update-field)

##### Function Signature

    update_field(field_id, variable_search, value_search, variable_change, value_change)

##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required
* `variable_search` _(character)_
	* Acceptable values are "name" or "farm_id"
	* Required
* `value_search` _(character)_
	* If the `variable_search` matches this value, then the next property will be changed. Otherwise no update will happen.
	* Required 
* `variable_change` _(character)_
	* Acceptable values are "name" or "farm_id"
	* Practically, this will usually match `variable_search`, but you could opt to change one property only if the other matches `value_search`
	* Required
* `value_change` _(character)_
	* The value to use for `variable_change`

##### Examples

This example will change the farm_id to "TestFarmA" only if it current matches "ABC":

    update_field('farm_id', 'ABC', 'farm_id', 'TestFarmA')

This example will change the name of the field only if the farm ID matches "TestFarmA":

    update_field('farm_id', 'TestFarmA', 'name', 'Primary Test Farm')



___________________________________________________________________________
---------------------------------------------------------------------------

### Plantings

Whereas Field Locations define a physical location, Planting records record information about the crop and growing season. They are used when automatically calculating agronomic values since the start of a growing season. Learn more at the [aWhere Developer Community](http://developer.awhere.com/api/reference/fields-plantings). 


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Create a Planting

Use this function to create a Planting for a Field Location. [API Documentation](http://developer.awhere.com/api/reference/plantings/create)

##### Function Signature 

    create_planting(field_id, crop, planting_date, proj_yield_amount, proj_yield_units, proj_harvest_date, yield_amount, yield_units, harvest_date) 


##### Parameters 

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required
* `crop` _(character)_
	* The ID or short-hand code for the crop that is planted in the field
	* The ID can be retrieved from the [Crops API](http://developer.awhere.com/api/reference/crops) 
	* Short-hand code would be the generic name of the crop. Current options include:
		* corn | wheat | barley | canola | cotton | sugarbeet | sunflower
	* Required
* `planting_date` _(character)_ 
	* The date the crop was planted in the field
	* Format is YYYY-MM-DD
	* Required 
* `proj_yield_amount` _(numeric)_
	* A number representing the expected yield.
	* Optional
* `proj_yield_units` _(character)_
	* The units associated with `proj_yield_amount` such as "bushels"
	* Optional, unless `proj_yield_amount` is supplied.
* `proj_harvest_date` _(character)_
	* The date the crop is expected to be harvested
	* Format is YYYY-MM-DD
	* Optional
* `yield_amount`
	* A number representing the actual yield
	* Optional
* `yield_units`
	* The units associated with `yield_amount`, such as "bushels"
	* Optional, unless `yield_amount` is supplied
* `harvest_date`
	* The date the field was actually harvested
	* Format is YYYY-MM-DD
	* Optional

##### Example

    create_planting('field123', 'corn', '2015-07-01')


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Get Planting Information

Use this function to retrieve information about the planting. [API Documentation](http://developer.awhere.com/api/reference/plantings/get-plantings)

##### Function Signature

    get_planting(field_id, planting_id, current)

##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Optional: If not supplied then all plantings in the account are returned.
* `planting_id` _(numeric)_
	* The Planting ID as set by the aWhere platform
	* Optional: If not supplied but a field_id is, then all plantings for that field_id are returned
* `current` _(boolean)_
	* Whether to return only the current (most recent) planting 
	* Optional 

##### Example 

This example returns the most current (most recent) planting for field123: 

    get_planting('field123','',T)


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Update Planting Information

Use this function to update the details about a Planting record. Note that once a planting is created the crop information cannot be changed (delete a the Planting record and create a new one instead). [API Documentation](http://developer.awhere.com/api/reference/plantings/update)

##### Function Signature 

    update_planting(field_id, planting_id, planting_date, proj_yield_amount, proj_yield_units, proj_harvest_date, yield_amount, yield_units, harvest_date)

##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required
* `planting_id` _(numeric)_
	* The Planting ID provided by the aWhere platform
	* This cannot be changed
	* Required 
* `planting_date` _(character)_ 
	* The date the crop was planted in the field
	* Format is YYYY-MM-DD
	* Required 
* `proj_yield_amount` _(numeric)_
	* A number representing the expected yield.
	* Optional
* `proj_yield_units` _(character)_
	* The units associated with `proj_yield_amount` such as "bushels"
	* Optional, unless `proj_yield_amount` is supplied.
* `proj_harvest_date` _(character)_
	* The date the crop is expected to be harvested
	* Format is YYYY-MM-DD
	* Optional
* `yield_amount`
	* A number representing the actual yield
	* Optional
* `yield_units`
	* The units associated with `yield_amount`, such as "bushels"
	* Optional, unless `yield_amount` is supplied
* `harvest_date`
	* The date the field was actually harvested
	* Format is YYYY-MM-DD
	* Optional


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Update Planting Information

If you want to keep your planting records clean for reporting and historical tracking purposes you can delete errant or incorrect plantings using this function. [API Documentation](http://developer.awhere.com/api/reference/plantings/delete)

##### Function Signature 

    delete_planting(field_id, planting_id)

##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required
* `planting_id` _(numeric)_
	* The Planting ID provided by the aWhere platform
	* Required 

##### Example 

    delete_planting("field123", 32481941)




___________________________________________________________________________
---------------------------------------------------------------------------

### Weather Data

The aWhere Platform provides agriculturally relevant weather data globally. 

All weather data requests are requested point by point. There is not a way to download regional data in a single API call. Use the [batch jobs system](http://developer.awhere.com/api/reference/batch) when requesting a large amount of data (e.g., many hundreds or thousands of points). 

Learn more [about the weather data](http://developer.awhere.com/api/about-our-data) we provide and our [Weather Data APIs](http://developer.awhere.com/api/reference/weather) at the aWhere Developer Community. 



- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Observations (Daily History)

This function uses the Observations API to retrieve observed weather data for any given day. By default, you can request daily data from up to 30 months ago. For data beyond that, use the Norms API (function below) to calculate long-term norms. Or, speak with your aWhere representative for access to more of the daily data archive. [API Documentation](http://developer.awhere.com/api/reference/weather/observations)

##### Function Signatures

    daily_observed_fields(field_id, day_start, day_end)


##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required
* `day_start` _(character)_
	* The starting date of a range of dates for which you want weather data 
	* Alternatively, if you only want data for a single date, enter that date here 
	* If not supplied, the API defaults to returning the last week of data
	* Format is YYYY-MM-DD and the date must fall before today
	* Optional
* `day_end` _(character)_
	* The ending date of a range of dates for which you want weather data. 
	* If not supplied, but a `day_start` is, then the API returns data only for a single day
	* Format is YYYY-MM-DD and the date must fall before today and after `day_start`
	* Optional
	
##### Examples

    daily_observed_fields("field123", "2015-07-01")


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Forecast

Retrieve the forecast for today plus up to the next 8 days with this function. The forecast is available at multiple temporal resolutions - in 1, 2, 3, 4, 6, 8, 12, or 24-hour summary blocks. [API Documentation](http://developer.awhere.com/api/forecast-weather-api)

##### Function Signatures

    forecasts_fields(field_id, day_start, day_end, block_size)


##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required 
* `day_start` _(character)_
	* The starting date of a range of dates for which you want forecast data 
	* Alternatively, if you only want data for a single date, enter that date here 
	* If not supplied, the API defaults to today and the next seven days of data
	* Format is YYYY-MM-DD and the date must be equal to today or any of the next seven days
	* Optional
* `day_end` _(character)_
	* The ending date of a range of dates for which you want weather data. 
	* If not supplied, but a `day_start` is, then the API returns data only for a single day
	* Format is YYYY-MM-DD and the date must fall after today and after `day_start`
	* Optional
* `block_size` _(numeric)_
	* The number of forecast hours to roll into a single forecast block 
	* Options are 1, 2, 3, 4, 6, 8, 12, and 24
	* Default is 1 (hourly forecast)
	* Optional
	

##### Examples

    forecasts_fields("field123", "2016-02-29")


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Long Term Norms 

The Weather Norms API allows you to offload the calculation of multi-year averages for any days of the year across any number of years for which we have data (minimum 3). This eliminates the need, most of the time, to download years and years of daily data just to calculate averages. The results also include the standard deviation for each average. [API Documentation](http://developer.awhere.com/api/reference/weather/norms)

##### Function Signatures 

    weather_norms_fields(field_id, monthday_start, monthday_end, year_start, year_end, exclude_years) 

##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required 
* `monthday_start` _(character)_
	* The month and day of the first day in a range for which you want norms.
	* Alternatively, if you only want data for a single date, enter that date here 
	* Format is MM-DD
	* Required
* `monthday_end` _(character)_
	* The month and day of the last day in the range.
	* If not supplied, but a `monthday_start` is, then the API returns data only for a single day
	* Format is MM-DD
	* Optional
* `year_start` _(character)_
	* The first of a range of years over which to calculate norms (inclusive)
	* Note: a minimum of three years is required
	* Use a four-digit year (YYYY)
	* Optional; if not used, the API defaults to a 10-year norm
* `year_end` _(character)_
	* The last of a range of years over which to calculate norms (inclusive)
	* Note: a minimum of three years is required
	* Use a four-digit year (YYYY)
	* Optional:
		* If `year_start` is used then this is required
		* if not used, the API defaults to a 10-year norm
* `exclude_years` _(character)_
	* A comma-separated list of years that you don't want included in the average
	* Note: a minimum of three years is required even after years are excluded
	* Use four-digit years (YYYY)
	* Optional


##### Example

    weather_norms_fields('field123','07-01','07-31','2010','2015')


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Current Conditions 

The Current Conditions API returns a snapshot of area weather for a location using recent data from the nearest available station. While the data is QA'd before delivery, this service does not do the same advanced processing or interpolation as Daily Observed data. [API Documentation](http://developer.awhere.com/api/reference/weather/current)

##### Function Signatures 

    current_conditions_fields(field_id, sources)

##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required
* `sources` _(character)_
	* Filters what kind of stations to use, as different station networks have different standards for quality and reporting
	* Options are: 
		* metar = METAR stations are the National Weather Service stations commonly found at airports
		* mesonet = MESONET stations are typically high-quality stations maintained by academic or other highly-attentive organizations
		* pws = Personal Weather Stations are typically good quality but do not have the same guarantees as other networks
		* metar-mesonet = select from either METAR or MESONET stations
		* all = Use all available stations
	* Default is all 
	* Optional 

##### Example

This example excludes personal weather stations and returns the data from nearest METAR or MESONET station:

    current_conditions_fields('field123', 'metar-mesonet')




___________________________________________________________________________
---------------------------------------------------------------------------

### Agronomic Data

aWhere provides a rich set of agronomic values that adds deeper layers of insight on top of weather data alone. These functions provide quick access to derived values like GDD or PET, and the long-term norms for each value. If you intend to use aWhere's modeling capabilities in the future, you must create Field Locations and Plantings. 

All weather data requests are requested point by point. There is not a way to download regional data in a single API call. Use the [batch jobs system](http://developer.awhere.com/api/reference/batch) when requesting a large amount of data (e.g., many hundreds or thousands of points). 


Learn more at the [aWhere Developer Community](http://developer.awhere.com/api/reference/agronomics)


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Agronomic Values 

Agronomic Values are calculated numbers that can be used to show the agronomic status of a field or crop. The aWhere platform can calculate GDD (using a variety of different equations), PET (using the Penman-Monteith Equation), P/PET, and accumulated precipitation, accumulated GDD, accumulated PET, and P/PET over a range of days. [API Documentation](http://developer.awhere.com/api/reference/agronomics/values)

##### Function Signatures 

    agronomic_values_fields(field_id, day_start, day_end, accumulation_start_date, gdd_method, gdd_base_temp, gdd_min_boundary, gdd_max_boundary) 


##### Parameters 

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required
* `day_start` _(character)_
	* The starting date of a range of dates for which you want agronomic values 
	* Alternatively, if you only want data for a single date, enter that date here 
	* If not supplied, the API defaults to returning the last week of data
	* Format is YYYY-MM-DD and the date must fall before today
	* Optional
* `day_end` _(character)_
	* The ending date of a range of dates for which you want data. 
	* If not supplied, but a `day_start` is, then the API returns data only for a single day
	* Format is YYYY-MM-DD and the date must fall before today and after `day_start`
	* Optional
* `accumulation_start_date` _(character)_
	* If you want the accumulations to start counting from a date before `day_start` enter that date here
	* Format is YYYY-MM-DD and the date must fall before today and before `day_start`
	* Optional
* `gdd_method` _(character)_
	* Which GDD equation to use 
	* Options are standard, modifiedstandard, min-temp-cap, min-temp-constant
	* See [documentation on selecting and configuring a GDD equation](http://developer.awhere.com/api/reference/agronomics/values#about-gdds-equations-and-default-values)
	* Optional
* `gdd_base_temp` _(numeric)_
	* The base temp to use with the GDD equation
	* See [documentation on selecting and configuring a GDD equation](http://developer.awhere.com/api/reference/agronomics/values#about-gdds-equations-and-default-values)
	* Optional
* `gdd_min_boundary` _(numeric)_
	* The lower boundary value for the GDD equation (if used)
	* See [documentation on selecting and configuring a GDD equation](http://developer.awhere.com/api/reference/agronomics/values#about-gdds-equations-and-default-values)
	* Optional
* `gdd_max_boundary` _(numeric)_
	* The upper boundary value for the GDD equation (if used)
	* See [documentation on selecting and configuring a GDD equation](http://developer.awhere.com/api/reference/agronomics/values#about-gdds-equations-and-default-values)
	* Optional

##### Example

    agronomic_values_fields('field123','2015-07-01','2015-07-31','2015-06-01','modifiedstandard','10','10','30')



- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#### Agronomic Norms 

The Agronomic Norms API returns the long-term normals for the agronomic values on any given set of days across any range of years for which we have data. This allows you to calculate the desired averages without having to download huge data sets. [API Documentation](http://developer.awhere.com/api/reference/agronomics/norms). 

##### Function Signatures 

    agronomic_norms_fields(field_id, monthday_start, monthday_end, year_start, year_end, exclude_years, accumulation_start_date, gdd_method, gdd_base_temp, gdd_min_boundary, gdd_max_boundary) 

##### Parameters

* `field_id` _(character)_
	* The ID you used when creating the field location.
	* Required 
* `monthday_start` _(character)_
	* The month and day of the first day in a range for which you want norms.
	* Alternatively, if you only want data for a single date, enter that date here 
	* Format is MM-DD
	* Required
* `monthday_end` _(character)_
	* The month and day of the last day in the range.
	* If not supplied, but a `monthday_start` is, then the API returns data only for a single day
	* Format is MM-DD
	* Optional
* `year_start` _(character)_
	* The first of a range of years over which to calculate norms (inclusive)
	* Note: a minimum of three years is required
	* Use a four-digit year (YYYY)
	* Optional; if not used, the API defaults to a 10-year norm
* `year_end` _(character)_
	* The last of a range of years over which to calculate norms (inclusive)
	* Note: a minimum of three years is required
	* Use a four-digit year (YYYY)
	* Optional:
		* If `year_start` is used then this is required
		* if not used, the API defaults to a 10-year norm
* `exclude_years` _(character)_
	* A comma-separated list of years that you don't want included in the average
	* Note: a minimum of three years is required even after years are excluded
	* Use four-digit years (YYYY)
	* Optional
* `gdd_method` _(character)_
	* Which GDD equation to use 
	* Options are standard, modifiedstandard, min-temp-cap, min-temp-constant
	* See [documentation on selecting and configuring a GDD equation](http://developer.awhere.com/api/reference/agronomics/values#about-gdds-equations-and-default-values)
	* Optional
* `gdd_base_temp` _(numeric)_
	* The base temp to use with the GDD equation
	* See [documentation on selecting and configuring a GDD equation](http://developer.awhere.com/api/reference/agronomics/values#about-gdds-equations-and-default-values)
	* Optional
* `gdd_min_boundary` _(numeric)_
	* The lower boundary value for the GDD equation (if used)
	* See [documentation on selecting and configuring a GDD equation](http://developer.awhere.com/api/reference/agronomics/values#about-gdds-equations-and-default-values)
	* Optional
* `gdd_max_boundary` _(numeric)_
	* The upper boundary value for the GDD equation (if used)
	* See [documentation on selecting and configuring a GDD equation](http://developer.awhere.com/api/reference/agronomics/values#about-gdds-equations-and-default-values)
	* Optional


##### Example

    agronomic_norms_fields('field123','07-01','07-31','2010','2015')
