# Query surge hours

> Query surge hours:

```python
import requests
 
url = "https://besttime.app/api/v1/forecasts/surge"

params = {
    'api_key_public': 'pub_e11661721b084d36b8f469a2c012e754',
    'venue_id': 'ven_51387131543761435650505241346a394a6432395362654a496843',
    'day_step': 0,
    'hour_step':0
}

response = requests.request("GET", url, params=params)
print(response.json())
```

```shell
# cURL
curl --location --request GET 'https://besttime.app/api/v1/forecasts/surge?api_key_public=pub_e11661721b084d36b8f469a2c012e754&venue_id=ven_51387131543761435650505241346a394a6432395362654a496843&
day_step=0&
hour_step=0'
```

```javascript
const params = new URLSearchParams({ 
  'api_key_public': 'pub_e11661721b084d36b8f469a2c012e754',
  'venue_id': 'ven_51387131543761435650505241346a394a6432395362654a496843',
  'day_step': 0,
  'hour_step': 0
});

fetch(`https://besttime.app/api/v1/forecasts/surge?${params}`, {
  method: 'GET'
}).then(function(data) { 
  console.log(data); 
});
```

### Input attributes Query surge hours

The 'query surge hours' endpoint is used to retrieve all surge hour information from an existing forecast for a specific day of the week.
By default, the response includes the surge hour information for the current day (at the local timezone of the venue). 

- **venue_id** `string` <span style="color:orange">REQUIRED</span>  
 The unique ID for the venue. The venue_id can be retrieved from a 'new forecast' endpoint response, or by the 'all venues' endpoint which shows all previously forecasted venues.  
 &nbsp; 
 - **day_int** `int` <span style="color:blue">OPTIONAL</span>  
 Day of the week. Range `0` (Monday) to `6` (Sunday). `day_int` cannot be used in combination with `day_step` and `hour_step`.  
 &nbsp; 
- **day_step** `int` <span style="color:blue">OPTIONAL</span>  
  Adjust the day (day of week of the venue in the local timezone). E.g. `0` means current day, and `1` means tomorrow. Range: min `-31`, max `31`. `day_step` cannot be used in combination with `day_int`.  
 &nbsp;  
- **hour_step** `int` <span style="color:blue">OPTIONAL</span>  
  Adjust the hour (hour of the venue in the local timezone). E.g. `0` means current hour, and `-2` means two hours ago. Range: min `-12`, max `12`. `hour_step` cannot be used in combination with `day_int`.  
 &nbsp;
- **api_key_public** `string` <span style="color:orange">REQUIRED</span>  
 Public API Key. See more info on [API keys](#api-reference)  
 &nbsp; 

<aside class="notice">
Query surge hours endpoint: https://besttime.app/api/v1/forecasts/surge
</aside>

<aside class="notice">
HTTP method: GET
</aside>


> The above request returns a JSON response like this:

```json
{
    "analysis": {
        "day_info": {
            "day_int": 0,
            "day_rank_max": 5,
            "day_rank_mean": 5,
            "day_text": "Monday",
            "venue_closed": 4,
            "venue_open": 4
        },
        "surge_hours": {
            "most_people_come": 8,
            "most_people_come_12h": "8AM",
            "most_people_come_passed": 0,
            "most_people_come_start_in": "Most people are coming in now",
            "most_people_come_start_in_sec": 0,
            "most_people_leave": 1,
            "most_people_leave_12h": "1AM",
            "most_people_leave_passed": 0,
            "most_people_leave_start_in": "16 hour and 26 minutes",
            "most_people_leave_start_in_sec": 59160
        }
    },
    "epoch_analysis": 1583911633,
    "forecast_updated_on": "2020-03-11T07:27:13.841800+00:00",
    "status": "OK",
    "venue_info": {
        "venue_current_gmttime": "Mon, 16 Mar 2020 15:34:59 GMT",
        "venue_current_localtime_iso": "2020-03-16T08:34:59.778538-07:00",
        "venue_id": "ven_51387131543761435650505241346a394a6432395362654a496843",
        "venue_name": "McDonald's",
        "venue_timezone": "America/Los_Angeles"
    },
    "venue_name": "McDonald's"
}
```

### Response attributes Query Surge hours


- **analysis** `object`  
 Containing the all the surge hour analysis and venue day info.
 - analysis.**surge_hours** `list`  
   List with surge hour objects, containing details of one or multiple surge periods per day.  
  &nbsp;
     - analysis.surge_hours.**surge_start** `int`  
       Start hour of the surge hour, using the 24 hour notation.  
       &nbsp;
     - analysis.surge_hours.**surge_start_passed** `int`  
       Indicates if the surge hour start is already passed. Indicates `1` for passed, `0` for not passed.
       &nbsp;
     - analysis.surge_hours.**surge_start_12** `string`  
       Start hour of the surge hour, using the 12 hour notation.  
       &nbsp;
     - analysis.surge_hours.**surge_start_in** `string`  
       Time remaining until the surge hour starts. Notation 'HH hour and MM minutes'. If surge hour start has been passed it will indicate `Start of surge period already passed`  
       &nbsp;
     - analysis.surge_hours.**surge_start_in_sec** `int`  
       Time remaining until the surge hour starts, in seconds.  
       &nbsp;
     - analysis.surge_hours.**surge_end** `int`  
       End hour of the surge hour, using the 24 hour notation.  
       &nbsp;
     - analysis.surge_hours.**surge_end_passed** `int`  
       Indicates if the surge hour end is already passed. Indicates `1` for passed, `0` for not passed.
       &nbsp;
     - analysis.surge_hours.**surge_end_12** `string`  
       End hour of the surge hour, using the 12 hour notation.  
       &nbsp;
     - analysis.surge_hours.**surge_end_in** `string`  
       Time remaining until the surge hour ends. Notation 'HH hour and MM minutes'. If surge hour end has been passed it will indicate `End of surge period already passed`  
       &nbsp;
     - analysis.surge_hours.**surge_end_in_sec** `int`  
       Time remaining until the surge hour ends, in seconds.  
       &nbsp;
 - analysis.**surge_hours_list** `list`  
   List with surge hours (`int`), in 24-hour notation.  
  &nbsp;
 - analysis.**surge_hours_list_12h** `list`  
   List with surge hours (`string`), in 12-hour notation.  
  &nbsp;
 - analysis.**surge_hours_list_coming** `list`  
   List with surge hours (`int`) which still have to come. In 24-hour notation.  
  &nbsp;
 - analysis.**surge_hours_list_coming_12h** `list`  
   List with surge hours (`string`) which still have to come, in 12-hour notation.  
  &nbsp;
- analysis.**day_info** `object`  
   Details about the day    
  &nbsp;
     - analysis.day_info.**day_int** `int`  
       Day integer range `0` (Monday) to `6` (Sunday)  
       &nbsp;
     - analysis.day_info.**day_rank_max** `int`  
       Day ranking based on the maximum busyness of the day. Range `1` to `7`. E.g. `2` indicates the 2nd most busy day of the week.  
       &nbsp;
     - analysis.day_info.**day_rank_mean** `int`  
       Day ranking based on mean busyness (total volume) of the day. Range `1` to `7`. E.g. `7` indicates the least busy day of the week.  
       &nbsp;
     - analysis.day_info.**day_text** `string`  
       Day name. E.g. `monday`  
       &nbsp;
     - analysis.day_info.**venue_closed** `int`  
       Hour of day when the venue closes. Range `0` to `23` hour  
       &nbsp;
     - analysis.day_info.**venue_open** `int`  
       Hour of day when the venue opens. Range `0` to `23` hour  
       &nbsp;
- **epoch_analysis** `int`  
 Epoch timestamp when the forecast was made.  
 &nbsp; 
- **forecast_updated_on** `TimeZone Aware DateTime string`  
 Date and time (Time Zone aware) of the original forecast.  
 &nbsp; 
- **status** `string`  
 Status of the response. Either `OK` or `Error`.  
 &nbsp; 
- **venue_info** `object`  
 Details of the forecasted venue.  
 &nbsp; 
  - venue_info.**venue_name** `string`  
   Name of the venue. This is the name of the venue as found by the geocoding lookup. Note this name could be slightly different than the `venue_address` used as input.  
  &nbsp;
 - venue_info.**venue_address** `string`  
   Address of the venue. This is the address of the venue as found by the geocoding lookup. Note this address could be different than the `venue_address` used as input.  
  &nbsp;
 - venue_info.**venue_current_gmtttime** `string`  
   Time at the venue in Greenwich Mean Time. Adjusting the `hour_step` and `day_step` will also alter this time.  
 - venue_info.**venue_current_localtime_iso** `string`  
   Local time at the venue.  
  &nbsp;
 - venue_info.**venue_id** `string`  
   Unique BestTime.app venue id. The `venue_id` is generated based on the venue name + address geocoding result. Therefore, when forecasting the same venue again it results in the same venue id. The `venue_id` is the primary input parameter to lookup (query) an existing forecast, using the [query endpoints] (#query-endpoints).
   The `venue_id` is used to perform queries.
  &nbsp;
 - venue_info.**venue_timezone** `string`  
  The timezone of the venue. E.g. `America/Los Angeles`  
  &nbsp;


### Combine a new forecast with this query in a single API call
This query endpoint takes data from an earlier forecasted venue. You can also combine a fresh forecast and get the results from this query endpoint using:

-  HTTP method: `POST` (instead of `GET`)
-  The same API query endpoint URL `https://besttime.app/api/v1/forecasts/surge`
-  `venue_name` and `venue_address` as input or `venue_id`
- The input attributes from this query endpoint

See the [New Forecast](#forecast-new-link) endpoint for more information on the `venue_name` and `venue_address` input. This will be counted as new forecast credits instead of a query credit.

