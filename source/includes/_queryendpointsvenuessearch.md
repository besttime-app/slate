# Venue Search 

> Find and add new venues based on a search query.

```python
import requests

url = "https://besttime.app/api/v1/venues/search"

params = {
    'api_key_private': 'pri_50990bf1f8828f6abbf6152013113c6b',
    'q': 'quiet supermarkets in sydney australia sunday morning',
    'num': 200,
    'fast': False,
    'format': 'raw'
}

response = requests.request("POST", url, params=params)
print(response.json())
```

```shell
# cURL
curl --location --request POST 'https://besttime.app/api/v1/venues/search?api_key_private=pri_50990bf1f8828f6abbf6152013113c6b&q=quiet%20supermarkets%20in%20sydney%20australia%20sunday%20morning&num=200&fast=false&format=raw'
```

```javascript
const params = new URLSearchParams({ 
    'api_key_private': 'pri_50990bf1f8828f6abbf6152013113c6b',
    'q': 'quiet supermarkets in sydney australia sunday morning',
    'num': 200,
    'fast': false,
    'format': 'raw'
});

fetch(`https://besttime.app/api/v1/venues/search?${params}`, {
  method: 'POST'
}).then(function(data) { 
  console.log(data); 
});
```

Find and add multiple new venues based on a search query. The search query can e.g. contain the name of a venue (e.g. McDonald's, Walmart,etc), or a type of venue (e.g. supermarkets, pizza, beach, things to do, etc). 

The search query can be narrowed down with additional filters like the location (e.g. a neighborhood, city, or country), or add geographical data (lat, lng, radius) to find venues related to the search query in a geographic location.

Note: Use the 'New foot traffic forecast' API endpoint when searching for a single venue (this is much faster).

Multiple API endpoints are involved from entering a search input until returning foot-traffic data for the found venues.
The Venue Search model will lookup venues in the background and will forecast them subsequently. Remember that this will therefore also result in forecast API credit usage. The endpoint will reply with a background task URL, `job_id`, and a `collection_id` (see [Collections](#venue-collections)).  You can poll the Venue Search Progress endpoint to poll to progress. The venue search functionality can also be used without API using the website [Venue Search Tool](https://besttime.app/api/v1/searchvenues) or on the [Radar tool](https://besttime.app/api/v1/radar/filter).

Once finished and if available for the venue, the Venue Search Progress endpoint will return the foot traffic data for the found venues. This data is similar to the `day_info` and `day_raw` response values of the [New Foot Traffic Forecast](#input-attributes-new-forecast) endpoint. However, it does not include all additional analyses. Use the [Query Week](#query-week) endpoint to get the whole forecast analysis using the venue_id. Normally API credits are charged for this endpoint, but is free within one day after created a new forecast (through the Venue Search- or directly through the [New Foot Traffic Forecast](#input-attributes-new-forecast) endpoint).

<b>Highly recommended to read first:
- [BestTime tools beginners tutorial](https://blog.besttime.app/foot-traffic-nightlife-bars/)
- [BestTime Software API beginners tutorial](https://blog.besttime.app/beginners-guide-foot-traffic-data-software-api/)
</b>

### Venue filters

The Venue Search Progress endpoint will return a link to view the results in the 'Radar tool' and the 'venue filter' API endpoint once the background job has been completed (`job_finished: true`). The venue search endpoint accepts filters similar to the 'venue filter' endpoint and Radar tool. By providing these parameters the links to the Radar tool and Venue Filter endpoint will automatically include the filter parameters. Only after analyzing each venue (foot-traffic forecast) most filters will be applied in the Radar tool/ Venue Filter endpoint. The venue search result therefore also includes venues that do not meet the filter criteria, but match the other search query text. The filters that are directly applied in the search are: `opened`, `lat`, `lng`, and `radius` for both search methods ('normal' and 'fast' method). The 'fast' method filters directly as well on `price_min` and `price_max`. The 'normal' search method will filter directly on `rating_min`.

### Natural language in the search query as filters

Besides separate parameters as filter inputs, the `q` search query understands also a variety of natural language that will be translated into the related filter parameters. You can use it to filter on busyness, day of week, day part, or time range. For example:  

Busyness natural language examples:  

- Search query `Busy bars in Sydney Australia` will result in a search for `bars in Sydney Australia` and sets the filter parameter minimum busyness to 50% `busy_min=50`.  

- Search query `Quiet supermarkets in Los Angeles, CA` will result in a search for `supermarkets in Los Angeles, CA` and sets the filter parameter maximum busyness to 50% `busy_max=50`.  

- Search query `10% to 60% busy shops in New York City` will result in a search for `shops in New York City` with `busy_min=10` and `busy_max=60` as filter parameters.

Strings that will trigger the busyness filters are:  

`busy` -> `busy_min=50`,  

`quiet`-> `busy_max=50`,  

Define a range with multiple percentages including the word `busy`:  

E.g. `40% to 90% busy bars in New York City`.
Define a custom minimum or maximum using the strings:  

- `min`,`minimal`, `minimum`,`least`  

- `max`,`maximal`,`maximum`,`most`  

E.g. `at least 60% busy bars in Melbourne Australia` will set the filter to `busy_min=60`


Day of week  natural language examples:  

- Search query `shopping malls in Singapore on Monday` will result in a search for `shopping malls 
in Singapore` and with `day_int=0` as filter parameters.  

- Search query `bars in Melbourne Australia Friday` will result in a search for `bars in Melbourne Australia` with `day_int=4` as the filter parameter.  

Strings (case insensitive) that will trigger the day of week are :
- `Monday` -> `day_int=0`,  
- `Tuesday` -> `day_int=1`,  
- `Wednesday`  -> `day_int=2`,  
- `Thursday` -> `day_int=3`,  
- `Friday` -> `day_int=4`,  
- `Saturday` -> `day_int=5`,  
- `Sunday` -> `day_int=6`

Time of day natural language examples:  

- Search query `quiet shopping malls in Singapore this morning` will result in a search for `shopping malls in Singapore` and with `busy_max=50`, `hour_min=6`, and `hour_max=11` as the filter parameters.  

- Search query `busy bars in Melbourne Australia Saturday evening` will result in a search for `bars in Melbourne Australia` and with `busy_min=50`, `hour_min=18`, and `hour_max=0` as the filter parameters.  

- Search query `min 60% busy bars in Melbourne Australia Saturday after 9 PM` will result in a search for `bars in Melbourne Australia` and with `busy_min=60`, `day_int=5`,`hour_min=21` as the filter parameters.  

- Search query `max 40% busy restaurants in Melbourne Australia on Wednesday from 13 until 15` will result in a search for `restaurants in Melbourne Australia` and with `busy_max=40`, `day_int=2`, `hour_min=13`, and `hour_max=15` as the filter parameters.  

Strings (case insensitive) that will trigger the time of day filters:  

- Dayparts (only works in combination with a day of the week string like e.g. `Monday`, or `this`):  

  `morning` -> `hour_min=6`, `hour_max=11`,  
  `afternoon` -> `hour_min=12`, `hour_max=17`,  
  `evening` -> `hour_min=18`, `hour_max=0`,  
  `night` -> `hour_min=21`, `hour_max=5`,  
  
- Min and/or max time in combination with a 12 or 24 hour notation:  
  `after`, `before`, `until`, `till`, `from`  
  e.g. `until 1pm` or `from 9 till 15`  

- `now` sets the the `hour_min` and `hour_max` value to filter foot-traffic data to the current hour local time of the first venue in the result. E.g. `busy bars in Sydney Australia now`. `Now` cannot be used in combination with day and other time triggers.  

- `live` will only show venues with real-time live data. `live` cannot be used in combination with day and other time triggers.  

This query endpoint requires the private API key. 

### Input attributes Venue Search

- **api_key_private** `string` <span style="color:orange">REQUIRED</span>  
 Private API Key. The endpoint will only return venues that are forecasted with this private API key. See [API keys](#api-reference) for more info.
 &nbsp;
- **q** `string` <span style="color:orange">REQUIRED</span>  
 Text query to search venues with a matching venue name (e.g. Whole Foods), or venue type (e.g. restaurants), and location (e.g. neighborhood, city, state or country). Max length `128` characters. You can use natural language to automatically add venue filters. See [Natural Language in a search query](#natural-language-in-the-search-query-as-filters)
 &nbsp;
- **num** `int` <span style="color:blue">OPTIONAL</span>  
Maximum number of search results, with increments of 20 venues, and a range from `20` to `200`.
The default number is `20`. API credits for this endpoint are counted per `20` search results. The search time grows linearly with the number of requested numbers (see also parameter `fast`).    
&nbsp;
- **format** `string` <span style="color:blue">OPTIONAL</span>  
Format for the foot traffic forecast of `venue_foot_traffic_forecast` returned in the final search results (if the venue has foot traffic data available). Choices are: `none, raw, all`. Choosing `raw` will only return the hourly foot traffic percentages for every day of the week (default). `all` will return the raw data including all foot traffic analyses, similar to the 'New Foot Traffic Forecast' and 'Query Week' response format. Select `None` skip the foot traffic data in the response to reduce the response size and increase the performance (e.g. if you use the foot traffic filter endpoint after this).   
 &nbsp;
- **opened** `string` <span style="color:blue">OPTIONAL</span>  
Search for venues with specific opening times. Options are `24`, `now`, `all` . `24` will return venues with a 24 hour opening time. `now` will return venues that are opened at this moment. `all` will return all venues regardless of their opening hours. Defaults to `all`.
 &nbsp;
- **fast** `boolean` <span style="color:blue">OPTIONAL</span>  
Boolean to select the normal speed or fast search method. Searching with the fast method is charged with more API credits. Defaults to `true` (fast search speed). The fast method is limited to a maximum `num` of `60`. Selecting a higher number will automatically use the normal speed method. Select `false` to save on API credits or to search for more venues. See [API Credits](#credits) for more info. Fixed packages each have a limited number of fast and normal search queries per month. The Pro - metered plan has a limit of 10000 fast venue search calls per calendar month. Contact us for high-volume fast or normal search queries.
 &nbsp;

- **collection_id** `string` <span style="color:blue">OPTIONAL</span>  
Add the results to an existing or user-defined collection_id. If this parameter is omitted a new unique collection_id will be generated. All successfully forecasted venues will be automatically added to this collection. By giving an existing collection_id the user can merge the new venues with an existing venue collection. See [Collections](#collections) for more info.
 &nbsp;

- **lat** `float` <span style="color:blue">OPTIONAL</span>  
   Geographic latitude of the search circle. A maximum of 3 decimal floating points are allowed (≈ 111 meter). This will be enforced starting from September 1st, 2021.  `lat` must be combined with `lng`, and `radius`. The search circle cannot be combined with the bounding box parameters. Either a combination of a `lat`, `lng`, with a `radius` or `lat_min`, `lng_min`, `lat_max`, and `lng_max` is required.  
  &nbsp;
- **lng** `float` <span style="color:blue">OPTIONAL</span>  
   Geographic longitude of the search circle. A maximum of 3 decimal floating points are allowed (≈ 111 meter). This will be enforced starting from September 1st, 2021.  `lng` must be combined with `lat`, and `radius`. The search circle cannot be combined with the bounding box parameters. Either a combination of a `lat`, `lng`, with a `radius` or `lat_min`, `lng_min`, `lat_max`, and `lng_max` is required.  
  &nbsp; 
- **radius** `int` <span style="color:blue">OPTIONAL</span>  
   Radius of the search circle in meter.  `radius` must be combined with `lat`, and `lng`. The search circle cannot be combined with the bounding box parameters. Either a combination of a `lat`, `lng`, with a `radius` or `lat_min`, `lng_min`, `lat_max`, and `lng_max` is required.  
  &nbsp;   
- **live** `bool` <span style="color:blue">OPTIONAL</span>    
 The live attribute value changes the behavior of both the Venue Search and optionally the subsequent Venue Filter response. Venue Search: When `live=true`, the Venue Search will always refresh the data and Search Progress response will contains live data (if available). When `format=raw` it will only include the raw live percentage in `venue_foot_traffic_live`. When `format=all` the response includes the live data - including the analysis similar to the [Live foot-traffic data](#live-foot-traffic-data) endpoint) - in `venue_foot_traffic_live`.
 Venue Filter: Sets the time and day filter to the current day and hour in local time, and will display the live busyness. Venues without live data will be filtered out. The local time of the first venue is taken that matches the filter criteria. Cannot be used in combination with the `now`, `day_int`, `hour_min`, and `hour_max` parameters.  
 &nbsp; 

<aside class="notice">
The 'venue search' endpoint accepts also the filters below, similar to the `venue filter` filter parameters. These 'venue filter' parameters are NOT applied on the initial 'search venue' venue results (as with the parameters mentioned above), but are only passed on to the 'venue filter' and 'radar' URL links. When a 'venue search' is performed with filters set to e.g. `busy_min=60` the 'venue search` results will include and forecast all found venues. Only the subsequent 'venue filter'/ 'radar tool' step applies the filters. 
</aside>  
&nbsp;


- **busy_min** `int` <span style="color:blue">OPTIONAL</span>  
Minimum busyness for the filtered venues, ranging from `0` to `100` procent.  Use `busy_conf` parameter to change the filter method.  
 &nbsp;
- **busy_max** `int` <span style="color:blue">OPTIONAL</span>  
Maximum busyness for the filtered venues, ranging from `0` to `100` procent. Use `busy_conf` parameter to change the filter method.    
 &nbsp;
- **busy_conf** `string` <span style="color:blue">OPTIONAL</span>  
Selects how `busy_min` and `busy_max` filters on busyness percentage. Possible options are `any` or `all`. Defaults to  `any`. `any` will return venues when at least one of the (selected) hours matches the `busy_min` and/or `busy_max` filter(s). `all` will return venues when all (selected) hours match the `busy_min` and/or `busy_max` filter(s). Use the `hour_min` and/or `hour_max` parameters to select specific hours were the `busy_min` and/or `busy_max` filters are applied on.  
 &nbsp;
- **foot_traffic** `string` <span style="color:blue">OPTIONAL</span>  
 Selects which part(s) of the foot traffic data will be returned in the response. Can be `limited`, `day`, or `both`. Default is `limited`. With `limited` selected, only the foot traffic data between the selected hours (`hour_min` and `hour_max`) is returned in the `day_raw` value (or whole day when no specific hours are selected). This also applies when combining with `now` or `live` in the input. When `day` is selected, foot traffic data for the whole day is returned (even if filters on specific hours are applied) in the `day_raw_whole` value. The `day_raw` value is in this case not returned. When `both` is selected, both the previously described `day_raw` and `day_raw_whole` value results are returned in the response.   
 &nbsp;
- **hour_min** `int` <span style="color:blue">OPTIONAL</span>  
Start hour, using the 24 hour notation. Ranging from `0` to `24` hour within the day window.  See [Forecast day window and weekdays](#forecast-day-window-and-weekdays). Cannot be used in combination with the `now` and `live` parameters set to be `true`.  
 &nbsp;
- **hour_max** `int` <span style="color:blue">OPTIONAL</span>  
Start hour, using the 24 hour notation. Ranging from `0` to `24` hour within the day window.  See [Forecast day window and weekdays](#forecast-day-window-and-weekdays). Cannot be used in combination with the `now` and `live` parameters set to be `true`.  
 &nbsp;
- **day_int** `int` <span style="color:blue">OPTIONAL</span>  
 Day of the week. Range `0` (Monday) to `6` (Sunday). Will default to current day in local time of the first found venue that meets the filter. Cannot be used in combination with the `now` and `live` parameters set to be `true`.
 &nbsp; 
- **now** `bool` <span style="color:blue">OPTIONAL</span>  
 Sets the time and day filter to the current day and hour in local time. Note: This option only takes into account one timezone! So it is best to use this parameter only when requesting venues within one timezone. When a collection_id is given, the timezone of the first venue in the collection is chosen. When bounding box parameters are given, the timezone of the center of the box is chosen as timezone. When `lat`, `lng` are given the timezone of the given coordinate is chosen. So if the determined timezone indicates currently 2 PM, the foot traffic forecast for all venues - regardless of the individual venue timezones - will be for 2 PM. Cannot be used in combination with the `live`, `day_int`, `hour_min`, and `hour_max` parameters.  
 &nbsp; 
- **live** `bool` <span style="color:blue">OPTIONAL</span>    
 The live attribute value changes the behavior of both the Venue Search and Venue Filter endpoints. Venue Search: When `live=true`, the Venue Search will always refresh the data and Search Progress response will contains live data (if available). When `format=raw` it will only include the raw live percentage in `venue_foot_traffic_live`. When `format=all` the response includes the live data - including the analysis similar to the [Live foot-traffic data](#live-foot-traffic-data) endpoint) - in `venue_foot_traffic_live`
 Venue Filter: Sets the time and day filter to the current day and hour in local time, and will display the live busyness. Venues without live data will be filtered out. The local time of the first venue is taken that matches the filter criteria. Cannot be used in combination with the `now`, `day_int`, `hour_min`, and `hour_max` parameters.  
 &nbsp; 
- **live_refresh** `bool` <span style="color:blue">OPTIONAL</span> <span style="color:green"> New</span>     
 Live refresh set to `true` will refresh all live and forecast data for each individual venue meeting the filter.  This will slow down the request and results in extra API credits per refreshed venue.  Default is `false`.We recommend setting this to `false` when using the Venue Search endpoint while setting `live=true` (as this will refresh the life data in the Search and later again in the Filter endpoint, resulting higher API credit usage and slower results).
 &nbsp; 
- **collection_id** `string` <span style="color:blue">OPTIONAL</span> <span style="color:green"> New</span>     
 Returns only venues added to given collection. See Collections for more info. See [Collections](#venue-collections).  
 &nbsp; 
- **types** `list` <span style="color:blue">OPTIONAL</span>  
 Filters on one or more venue types. All types are selected if the `types` parameter is ommited. Possible types are `['APPAREL', 'ARTS', 'BANKING', 'BAR', 'BOTANICAL_GARDEN', 'CAFE', 'CAR_RENTAL', 'CHURCH', 'CITY_HALL', 'COFFEE', 'DENTIST', 'DOCTOR', 'EMBASSY', 'EVENT_VENUE', 'FAST_FOOD', 'FOOD_AND_DRINK', 'FOOD_DELIVERY', 'GAS_STATION', 'GOVERNMENT', 'GROCERY', 'LODGING','MARKET', 'MOVIE_THEATER', 'MUSEUM', 'Other', 'PARK', 'PERFORMING_ARTS', 'PERSONAL_CARE', 'PHARMACY', 'PUBLIC_TRANSIT', 'RESTAURANT', 'SCHOOL', 'SHOPPING', 'SKILL_INSTRUCTION', 'SPA', 'SPORTS_COMPLEX', 'SUPERMARKET', 'TEA', 'TOURIST_DESTINATION', 'VISITOR_CENTER']`  
 &nbsp; 

- **lat_min** `float` <span style="color:blue">OPTIONAL</span>  
   Minimum latitude of the bounding box (South-West). A maximum of 3 decimal floating points are allowed (≈ 111 meter). This will be enforced starting from September 1st, 2021.  `lat_min` must be combined with `lat_max`, `lng_min` and `lng_max`. The bounding box cannot be combined with the circle parameters. Either a combination of a `lat`, `lng`, with a `radius` or `lat_min`, `lng_min`, `lat_max`, and `lng_max` is required. It is recommended to use the `lat`, `lng`, and `radius` parameters over the bounding box parameters in the search tool. The bounding box filters are only applied after the search result using the Radar Tool/ Venue filter endpoint.
  &nbsp; 
- **lng_min** `float` <span style="color:blue">OPTIONAL</span>  
   Minimum longitude of the bounding box (South-West). A maximum of 3 decimal floating points are allowed (≈ 111 meter). This will be enforced starting from September 1st, 2021.  `lng_min` must be combined with `lng_max`, `lat_min` and `lat_max`. The bounding box cannot be combined with the circle parameters. Either a combination of a `lat`, `lng`, with a `radius` or `lat_min`, `lng_min`, `lat_max`, and `lng_max` is required. It is recommended to use the `lat`, `lng`, and `radius` parameters over the bounding box parameters in the search tool. The bounding box filters are only applied after the search result using the Radar Tool/ Venue filter endpoint.
  &nbsp; 
- **lat_max** `float` <span style="color:blue">OPTIONAL</span>  
   Maximum latitude of the bounding box (North-East). A maximum of 3 decimal floating points are allowed (≈ 111 meter). This will be enforced starting from September 1st, 2021.  `lat_max` must be combined with `lat_min`, `lng_min` and `lng_max`. The bounding box cannot be combined with the circle parameters. Either a combination of a `lat`, `lng`, with a `radius` or `lat_min`, `lng_min`, `lat_max`, and `lng_max` is required. It is recommended to use the `lat`, `lng`, and `radius` parameters over the bounding box parameters in the search tool. The bounding box filters are only applied after the search result using the Radar Tool/ Venue filter endpoint.
  &nbsp; 
- **lng_max** `float` <span style="color:blue">OPTIONAL</span>  
   Maximum longitude of the bounding box (North-East). A maximum of 3 decimal floating points are allowed (≈ 111 meter). This will be enforced starting from September 1st, 2021.  `lng_max` must be combined with `lng_min`, `lat_min` and `lat_max`. The bounding box cannot be combined with the circle parameters. Either a combination of a `lat`, `lng`, with a `radius` or `lat_min`, `lng_min`, `lat_max`, and `lng_max` is required. It is recommended to use the `lat`, `lng`, and `radius` parameters over the bounding box parameters in the search tool. The bounding box filters are only applied after the search result using the Radar Tool/ Venue filter endpoint. 
  &nbsp; 
- **price_min** `int` <span style="color:blue">OPTIONAL</span>  
   Minimum price level for a venue. Range `1` to `5`. Not all venues have a price level. Using `price_min` filters out all venues without a price level. When the 'fast' search method is used `price_min` and `price_max` will be directly filtered in the initial search tool, instead of after the search results in the subsequent Radar Tool/ Venue Filter endpoint. 
  &nbsp; 
- **price_max** `int` <span style="color:blue">OPTIONAL</span>    
   Maximum price level for a venue. Range `1` to `5`. Not all venues have a price level. Using `price_max` filters out all venues without a price level. When the 'fast' search method is used `price_min` and `price_max` will be directly filtered in the initial search tool, instead of after the search results in the subsequent Radar Tool/ Venue Filter endpoint.
  &nbsp; 
- **rating_min** `float` <span style="color:blue">OPTIONAL</span>    
   Minimum rating for a venue. Possible values are `2.0, 2.5, 3.0, 3.5, 4.0, 4.5`. Venues without a rating (0) will NOT be returned when applying this filter.   
  &nbsp; 
- **rating_max** `float` <span style="color:blue">OPTIONAL</span>  
   Maximum rating for a venue. Possible values are `2.0, 3.0, 3.5, 4.0, 4.5, 5.0`. Venues without a rating (0) WILL be returned when applying this filter.
  &nbsp; 
- **reviews_min** `int` <span style="color:blue">OPTIONAL</span>    
   Minimum number of reviews for a venue. Minimum value `0`.  
  &nbsp;  
- **reviews_max** `int` <span style="color:blue">OPTIONAL</span>    
   Maximum number of reviews for a venue. Minimum value `0`.
  &nbsp; 


<aside class="notice">
Search venue endpoint: https://besttime.app/api/v1/venues/search
</aside>

<aside class="notice">
HTTP method: POST
</aside>

<aside class="warning">
API Credit usage per API call for this endpoint depends on the number `num` of requested venues and the search speed (Normal or Fast). See 'API key credits' for more information.
</aside>


<aside class="warning">
The Venue Search endpoint is by default limited to 30 requests per minute, and 300 requests per hour. Contact us for higher limits.
</aside>


> The above request returns a JSON response with links to the background job:

```json
{
    "_links": {
        "venue_search_progress": "https://besttime.app/api/v1/venues/progress?job_id=e0880f28-3a19-4871-a355-4ca21f10c2c8&collection_id=col_ac734e76ad2d4696a5a66541c67587e8&format=raw"
    },
    "collection_id": "col_ac734e76ad2d4696a5a66541c67587e8",
    "job_id": "e0880f28-3a19-4871-a355-4ca21f10c2c8",
    "status": "OK"
}
```


### Response attributes Venue Search
The JSON response will contain a URL to the Venue Search Progress endpoint to track the progress of the current venue search that runs in the background. This URL is the same as the [Venue Search Progress](#response-attributes-venue-search) API endpoint.

- **_links** `object` 
  &nbsp;
  - _links.**venue_search_progress** `string`  
    Link to the venue search background job. Use this link to check the progress on the search venues query. When the job is finished it will display the found venues, how many venues have forecast data, and links to show the found venues with foot-traffic results directly in the 'Radar tool` and 'venue filter' endpoints. For more info see [Venue Search Progress](#response-attributes-venue-search).
    &nbsp;
    
    &nbsp;
- **collection_id** `string` 
 Unique ID for the collection, 36 characters long. See [Collections](#venue-collections). 
 - **job_id** `string` 
 Unique ID for the venue search background job.  
- **status** `string` 
 Status of the response. Either `OK` or `error`.


## Venue Search Progress

> Venue Search Progress endpoint

```python
import requests

url = "https://besttime.app/api/v1/venues/progress"

params = {
    'job_id': '0a693bb3-7bd6-4d43-9495-a2773f1c9e29',
    'collection_id': 'col_ffbebb4003974979b75a14844d60e9c5',
    'format': 'raw'
}

response = requests.request("GET", url, params=params)
print(response.json())
```

```shell
# cURL
curl --location --request GET 'https://besttime.app/api/v1/venues/progress?
job_id=0a693bb3-7bd6-4d43-9495-a2773f1c9e29&collection_id=col_ffbebb4003974979b75a14844d60e9c5&
format=raw'
```

```javascript
const params = new URLSearchParams({ 
    'job_id': '0a693bb3-7bd6-4d43-9495-a2773f1c9e29',
    'collection_id': 'col_ffbebb4003974979b75a14844d60e9c5',
    'format': 'raw'
});

fetch(`https://besttime.app/api/v1/venues/progress?${params}`, {
  method: 'GET'
}).then(function(data) { 
  console.log(data); 
});
```

### Input attributes Venue Search Progress

- **job_id** `string` <span style="color:orange">REQUIRED</span>  
 Private API Key. The endpoint will only return venues that are forecasted with this private API key. See [API keys](#api-reference) for more info.
 &nbsp;
- **collection_id** `string` <span style="color:blue">OPTIONAL</span>  
Adding the `collection_id` passes on the collection_id in the result links once the venue search is finished. See [Collections](#venue-collections).  
 &nbsp;


> The above request returns a JSON response with the progress of the venue search. Once it is complete it will show the second displayed JSON response.

```json
{
    "collection_id": "col_5c546908473645c1b9bad36b7fef7765",
    "count_completed": 140,
    "count_failure": 0,
    "count_forecast": 129,
    "count_live": 74,
    "count_total": 200,
    "job_finished": false,
    "job_id": "eb6aa504-e147-4b2d-a191-03d721764279",
    "status": "OK"
}
```


> When the venue search is complete it will return a JSON response with the following structure :

```json
{
    "_links": {
        "background_progress_api": "http://besttime.app/api/v1/venues/progress?job_id=eb6aa504-e147-4b2d-a191-03d721764279&ven=False",
        "background_progress_tool": "http://besttime.app/api/v1/misc/addarea_progress?q=quiet+supermarkets+in+sydney+australia+sunday+morning&job_id=eb6aa504-e147-4b2d-a191-03d721764279&map_lat=-33.8627418&map_lng=151.2165809&lat_min=-33.8877752&lat_max=-33.8377084&lng_min=151.1962989&lng_max=151.2368629&map_zoom=14&radius=6712&collection_id=col_5c546908473645c1b9bad36b7fef7765&api_key_private=pri_50990bf1f8828f6abbf6152013113c6b&busy_min=0&busy_max=50&day_int=6&hour_min=6&hour_max=11&now=True&live_refresh=False&auto_continue=1",
        "radar_tool": "http://besttime.app/api/v1/radar/filter?q=quiet+supermarkets+in+sydney+australia+sunday+morning&map_lat=-33.8627418&map_lng=151.2165809&lat_min=-33.8877752&lat_max=-33.8377084&lng_min=151.1962989&lng_max=151.2368629&map_z=14&collection_id=col_5c546908473645c1b9bad36b7fef7765&api_key_private=pri_50990bf1f8828f6abbf6152013113c6b&busy_min=0&busy_max=50&day_int=6&hour_min=6&hour_max=11&now=True&live_refresh=False",
        "venue_filter_api": "http://besttime.app/api/v1/venues/filter?lat_min=-33.8877752&lat_max=-33.8377084&lng_min=151.1962989&lng_max=151.2368629&collection_id=col_5c546908473645c1b9bad36b7fef7765&api_key_private=pri_50990bf1f8828f6abbf6152013113c6b&busy_min=0&busy_max=50&day_int=6&hour_min=6&hour_max=11&now=True&live_refresh=False"
    },
    "bounding_box": {
        "lat": -33.8627418,
        "lat_max": -33.8377084,
        "lat_min": -33.8877752,
        "lng": 151.2165809,
        "lng_max": 151.2368629,
        "lng_min": 151.1962989,
        "map_zoom": 14,
        "radius": 6712
    },
    "collection_id": "col_5c546908473645c1b9bad36b7fef7765",
    "count_completed": 200,
    "count_failure": 0,
    "count_forecast": 181,
    "count_live": 90,
    "count_total": 200,
    "job_finished": true,
    "job_id": "eb6aa504-e147-4b2d-a191-03d721764279",
    "status": "OK",
    "venues": [
        {
            "forecast": false,
            "processed": true,
            "venue_address": "21 Shelley St, Sydney NSW 2000, Australia",
            "venue_lat": -33.8670477,
            "venue_lon": 151.2023238,
            "venue_name": "Kings Wharf Supermarket",
            "venue_id": "ven_6372542d36476a8759686d52676b646155646e713661514a496843"
        },
        {
            "forecast": true,
            "processed": true,
            "venue_address": "4/490 Crown St, Surry Hills NSW 2010, Australia",
            "venue_lat": -33.8866095,
            "venue_lon": 151.2138922,
            "venue_name": "Maloneys Grocer",
            "venue_id": "ven_9372542d36476a8759686d52676b646155646e713661514a496847",
            "venue_foot_traffic_forecast": [
                {
                    "day_info": {
                        "day_int": 0,
                        "day_max": 25,
                        "day_mean": 12,
                        "day_rank_max": 6,
                        "day_rank_mean": 7,
                        "day_text": "Monday",
                        "venue_closed": 21,
                        "venue_open": 6
                    },
                    "day_int": 0,
                    "day_raw": [
                        5,
                        5,
                        5,
                        10,
                        15,
                        20,
                        20,
                        25,
                        25,
                        20,
                        15,
                        10,
                        5,
                        5,
                        0,
                        0,
                        0,
                        0,
                        0,
                        0,
                        0,
                        0,
                        0,
                        0
                    ]
                },
                {
                    "day_info": {
                        "day_int": 1,
                        "day_max": 24,
                        "day_mean": 12,
                        "day_rank_max": 7,
                        "day_rank_mean": 7,
                        "day_text": "Tuesday",
                        "venue_closed": 21,
                        "venue_open": 6
                    },
                    "day_int": 1,
                    "day_raw": [
                        0,
                        5,
                        5,
                        10,
                        15,
                        ...
        },
        ... Only the first two results are displayed here
    ],
    "venues_n": 200
}
```


### Response attributes Venue Search Progress
The JSON response will contain the progress of the Venue Search query and once completed it will return the remaining attributes as shown in the second part of the attributes below.

- **count_total** `int` 
 Total number of found venues matching the search query. When the venue search is still not finished (`job_finished: false`) this number could still go up until the maximum `num` of requested venues.  This number could also be below the number of requested venues, if there are no more matching venues are found.
- **count_completed** `int` 
 Number of venues processed (forecasted) in the background. 
- **count_forecasted** `int` 
 Number of venues with foot-traffic data (forecast data).  
- **count_live** `int` 
 Number of venues with live foot-traffic data.  
- **count_failed** `int` 
 Number of failed venues that resulted in errors. This number does not include venues without forecast data.  
- **job_finished** `bool` 
 Boolean indicating if the Venue Search has been completed. `true` or `false`.  
- **collection_id** `string` 
Unique ID for the collection, 36 characters long.  
- **job_id** `string` 
Unique ID for the venue search background job.  
- **status** `string` 
 Status of the response. Either `OK` or `error`.

The attributes below will be displayed when the Venue Search job is finished (`job_finished: true`).


- **_links** `object` 
  &nbsp;
  - _links.**radar_tool** `string`  
    URL to show the current Venue Search query results in the radar tool. The URL includes all relevant filters (both the manually applied filters and the filters triggered by the natural language filter detection functionality). 
    &nbsp;
  - _links.**venue_filter_api** `string`  
    URL to show the current Venue Search query results in the Venue Filter API endpoint. The URL includes all relevant filters (both the manually applied filters and the filters triggered by the natural language filter detection functionality).  
    &nbsp;
    &nbsp;

- **venues** `list` 
  List of venues meeting the Venue Search query without filters (busyness, day, time, etc). 
  &nbsp;
  - venues[N].**forecast** `bool`  
    Indicates if the venue has foot-traffic forecast data. `true` or `false`. 
    &nbsp;
  - venues[N].**processed** `bool`  
    Indicates if the venue has been processed (analyzed) for foot-traffic data. `true` or `false`. 
    &nbsp;
  - venues[N].**venue_address** `string`  
   Address of the venue. This is the address of the venue as found by the geocoding lookup. Note this address could be different than the `venue_address` used as input.  
    &nbsp; 
  - venues[N].**venue_name** `string`  
   Name of the venue. This is the name of the venue as found by the geocoding lookup. Note this name could be slightly different than the `venue_address` used as input.  
   &nbsp;
  - venues[N].**venue_lat** `float`  
   Geographic latitude of the venue.
  &nbsp;
  - venues[N].**venue_lng** `float`  
   Geographic longitude of the venue.
  &nbsp;
  - venues[N].**venue_id** `string`  
   Unique BestTime.app venue id.    
  &nbsp;    
  - venues[N].**venue_foot_traffic_forecast** `string`  
   Foot-traffic forecast data for each hour of the day, for every day of the week. See [New Foot Traffic Forecast](#input-attributes-new-forecast) for details on the `day_info` and `day_raw` attributes. 
   
    Alternatively, use the [Query Week](#query-week) endpoint to get the whole forecast analysis using the venue_id. Normally API credits are charged for this endpoint, but is free within one day after created a new forecast (through the Venue Search- or directly through the [New Foot Traffic Forecast](#input-attributes-new-forecast) endpoint).     
   &nbsp;


   
- **venues_n** `int`
Total number of venues in list `venues`.
- **bounding_box** `object` 
  Geographical bounding box coordinates that fits all venues. Useful to for displaying venues on a map. As an alternative the map center lat, lng, map_zoom and a radius parameters are provided to view all results on a map.
  &nbsp;
  - bounding_box.**lat** `string`  
    Geographic map latitude center of venue search result.  
    &nbsp;
  - bounding_box.**lat_max** `string`  
    Maximum latitude of the bounding box (North-East). 
    &nbsp;
  - bounding_box.**lat_min** `string`  
    TOTO 
    &nbsp;
  - bounding_box.**lng** `string`  
    Geographic map longitude center of venue search result.   
    &nbsp;
  - bounding_box.**lng_max** `string`  
    Maximum longitude of the bounding box (North-East). 
    &nbsp;
  - bounding_box.**lng_min** `string`  
    Minimum longitude of the bounding box (South-West). 
    &nbsp;
  - bounding_box.**map_zoom** `string`  
    Recommended map_zoom value to make all found venues show on a map in combination with the provided `lat`, `lng` map center coordinates. 
    &nbsp;
  - bounding_box.**radius** `string`  
    Radius in meters of the venue search results. 
    &nbsp;

