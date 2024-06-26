
# API Reference

The API Reference explains how:

- authentication works using the API Keys.
- credits are handled with your API usage.
- to create a new venue forecast.
- to query an existing forecast to get specific, or more detailed data.

<a name="api-keys"></a> 
## API Key 


BestTime.app uses API keys to allow access to the API. You can find or generate API keys at the [API keys Management](http://besttime.app/api/v1/api_keys_list) page.

BestTime.app expects for the API key to be included in all API requests to the server.

Authentication for the API is done using API keys.
There are two types of API keys; Private keys are used to create a new forecast, and public keys to query data from existing forecasted venues. The private key can be used to create, delete and list forecasts. As the name suggests, the private key should be kept secret, to avoid other people from forecasting new venues and abusing your limited forecast credits. The public key can be used to query existing venue forecasts. However, it can only be used to get existing forecast data (read-only). 

API keys are generated in pairs, and you can generate multiple API key sets (pairs) in the API key management page. When using multiple API keys, you should remember that you can only query forecasts from the same key set. 

All key set use credits from the same account. When an API key is compromised, you can delete the API key set through the API Key management page.

<b>Highly recommended to read first:
- [BestTime tools beginners tutorial](https://blog.besttime.app/foot-traffic-nightlife-bars/)
- [BestTime Software API beginners tutorial](https://blog.besttime.app/beginners-guide-foot-traffic-data-software-api/)
</b>  

## Authentication

> To authorize, use this code:


```python
import requests

url = "https://besttime.app/api/v1/keys/pri_a00de9e302662c0217a9cf08ab304122"

response = requests.request("GET", url)

print(response.json())
```

```shell
# cURL
curl --location --request GET 'https://besttime.app/api/v1/keys/pri_a00de9e302662c0217a9cf08ab304122'
```

```javascript

fetch(`https://besttime.app/api/v1/keys/pri_a00de9e302662c0217a9cf08ab304122`, {
  method: 'GET'
}).then(function(data) { console.log(data); });
```

You can find or generate API keys at the [API keys Management](http://besttime.app/api/v1/api_keys_list) page.

> The above command returns JSON structured like this:

```json
  {
    "active": true,
    "api_key_private": "pri_a00de9e302662c0217a9cf08ab304122",
    "api_key_public": "pub_e11661721b084d36b8f469a2c012e754",
    "status": "OK",
    "valid": true
  }
```

### Private and public API keys

- **api_key_private** `string`  
 32 Character private API key. Used to create new forecasts or get live data.  
 &nbsp; 
- **api_key_public** `string`  
 32 Character public API key. Used to query (lookup) specific data from an excisting forecast.  
 &nbsp;  

<aside class="notice">
Base endpoint: https://besttime.app/api/v1/keys
</aside>

<aside class="notice">
HTTP method: GET
</aside>


<aside class="notice">
Make sure to replace <code>pri_a00de9e302662c0217a9cf08ab304122</code> with your 36 char private API key.
</aside>


## Credits

For metered API subscription API credits are used to calculate your total monthly bill. See the pricing page for the price per API credit. 

The number of credits per API call depends on the used API endpoint. The tools on the website also use the API internally and will therefore also count towards your total API usage.

Alternatively, you can buy a Package subscription. Then you always pay a fixed fee per month and can call the API as many times as you want (see the BestTime pricing page for more details on the packages).

 
&nbsp;  

| API Endpoint                           | Credits     | API Key required |
|------------------------------------|------------------|------------------|
| New foot traffic forecasts (success)             | 2          | Private          | 
| New foot traffic forecasts (unsuccessful)        | 1         | Private        |
| Live foot traffic data                           | 1         | Private          | 
| Venue (all/ filter/ update)           | 1 / 10 venues | Private          |
| Venue Search (Normal)          | 1 / 20 venues | Private          |
| Venue Search (Fast)          | 5 / 20 venues | Private          |
| Query (existing forecast)                | 1         | Public            |  
 
 &nbsp; 

Unsuccessful forecasts are also counted as credits, with the exception of server errors. This is to prevent overloading the API servers with low quality address inputs.

It is the users responsibility to prevent api key abuse. Hide your API keys secure to prevent other people from using API credits resulting in higher monthly subscription fees.

The Venue Search functionality counts credits for finding matching venues, but this result does not include foot-traffic data. Therefore, the Venue Search function will automatically pushes the found venues to the 'New foot-traffic forecast' API endpoint. A 'normal' speed Venue search for max 20 venues will therefore cost: 1 Venue search normal credit + 20 * 2 New Forecast (successful) credit = 41 credits (equals to approximately $0.32 with the Premium plan). This is the maximum number of credits used. If the search result includes less venues, or if a venue does not have foot-traffic data the number will be lower. In a future version we will give the user the possibility to decide to not automatically forecast all found venues through the Venue Search tool. 


### Subscription plans
BestTime has two types of plans. Metered and packaged plans. The metered plans will automatically charge you depending on the credit usage at the end of a (monthly) billing cycle. The basic plan is the lowest-priced plan. All functionality is available in the basic plan, 

BestTime also offers multiple 'packaged' plans if you don't like the uncertainty of a metered plan. The packaged plans have a fixed price per month and unlimited forecast, live, query and venue API calls. However, each package plan is limited to a certain number of new venues-, and venue search calls per calender month.


Old plans:  &nbsp;  
Credits will be added at the start of every monthly invoice cycle, when you upgrade, or when you buy extra credit bundles (only for selected subscription plans). Credits don't expire and automatically roll over to the next month. If you cancel your subscription coins will stay in your account. However, all your API keys will be deactivated. This means you cannot create new forecasts and query existing forecasts. If you decide to re-subscribe on the same account you can use your old coins, but you cannot access the previous forecasts anymore.

## HTTP (Error) API codes

BestTime uses the following HTTP codes

| Code  | Meaning   | 
|---------------|-------------|
| 200              | OK      | 
| 400             | Bad Request - check your API parameters |
|401               | Unauthorized |
| 404              | Not found - API resource not found |
| 405               | Method Not Allowed - You tried to access the API with an invalid route |
|429 |Too Many Requests - You have been rate-limited
|500 | Internal Server Error - We have a problem with the server and the team has been automatically notified
| 503 | Service Unavailable

&nbsp;  

By default the API is limited to 300 API requests per minute. The Venue Search API endpoint is limited to 30 request per minute and 300 requests per hour. The Venue Filter is limited to 30 request per minute. You will receive a HTTP 429 'too many requests' above this threshold. Contact us for if you need higher limits.