# Location reverse script through Facade
Get an address from a postal code or latitude/longitude. Or reverse it and get a latitude/longitude from an address or postal code.

Please note that the data is not always right seeing we rely on the Google API. 
It is even almost always wrong when you try to resolve an IP address. 
So please use it as indication and not as fact. 
That being said, there is no other script that comes closer to the correct location as far as I can tell.


### PHP requirements:
- PHP Curl


### To do:
- Create Guzzle client support switch


### Why not yet:
Guzzle continously changes it's workings. I haven't found the time yet.


## How to make it work

Add to app/config => providers
```
Noprotocol\LaravelLocation\LocationServiceProvider::class,
```

Add to app/config => aliases
```
'Location' => Noprotocol\LaravelLocation\Facades\LocationFacade::class,
```

To get the configuration run:
```
php artisan vendor:publish --tag=location
```

If you have a Google key add a line to your .env file:
```
GOOGLE_KEY=[key]
```
Script will work out of the box without a key, but it has limited requests. 
Please look at Google documentation hell to see how what the limit rating is.


These (quick example):
```
$location = Location::locale('nl')->coordinatesToAddress(['latitude' => 52.385288, 'longitude' => 4.885361])->get();

$location = Location::locale('nl')->addressToCoordinates(['country' => 'Nederland', 'street' => 'Nieuwe Teertuinen', 'street_number' => 25])->get();

$location = Location::locale('nl')->postalcodeToCoordinates('1072 BT', '44')->coordinatesToAddress()->get();

$location = Location::locale('nl')->ipToCoordinates()->coordinatesToAddress()->get(); // if IP resolves properly, which it mostly doesn't
```


Will all result in:
```
$location['latitude'] = 52.3565722,
$location['longitude'] = 4.888663;
$location['country'] = 'Nederland';
$location['region'] = 'Noord-Holland';
$location['city'] = 'Amsterdam';
$location['street'] = 'Frans Halsstraat';
$location['street_number'] = '44';
$location['postal_code'] = '1072 BT';
```

To return it as object set the ```get()``` function to true: ```get(true)```


Extended example:
```
try {
	$location = Location::coordinatesToAddress(['latitude' => 52.385288, 'longitude' => 4.885361])->get(true);

	if($error = Location::error()) {
		dd($error);
	}
}
catch(Exception $e) {
	dd($e->getMessage());
}
```

The result is the default template and starts out as empty and gets filled throught the call. So if no data is available 
the result for that entry will be "". After every call the script resets to it's basic settings.


## Debug
With the try catch you can alreay see what you need. But besides this there is also a cached result of the raw response from the 
google API. Please note that this is not the case with the ip request.

```
$location = Location::coordinatesToAddress(['latitude' => 52.385288, 'longitude' => 4.885361])->get();
Location::response(); // results in raw api response
```