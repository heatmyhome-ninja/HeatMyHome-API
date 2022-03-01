# HeatMyHome API

This repository contains the code used to run the API server used by the [HeatMyHome-Website](https://github.com/heatmyhome-ninja/HeatMyHome-Website).
Although the API is currently not publically callable, due to limited compute hours you may wish to read the code and coumentation to develop your own API / Heating Simulator, or host the server for your own use.

# Documentation

The URL of the API is: [customapi.heatmyhome.ninja](https://customapi.heatmyhome.ninja)

The API returns JSON objects, which contains the following keys: "status", "result", "error", with "result" and "error" keys being conditional upon the status. A list of example requests are provided below to show the possible outputs of the API dependent on the appended subdomain / url parameters.
___
### No Subdomain or Parameters 
```
https://customapi.heatmyhome.ninja
``` 
When no parameters entered API returns recommendation to goto either EPC API or Simulator subdomain. 
``` 
{"status":404,"error":"must call customapi.heatmyhome.ninja/epc or customapi.heatmyhome.ninja/simulate with their respective url parameters","info":"API Version 0.9"} 
```
___
### EPC Subdomain: No Parameters 
```
https://customapi.heatmyhome.ninja/epc
``` 
With EPC API you may request a list of addresses and EPC certificate numbers for a given postcode, or request the EPC Space Heating Value and Floor Area of a given property.
``` 
{"status":404,"error":"must provide url parameter (either customapi.heatmyhome.ninja/epc?postcode=CV47AL or customapi.heatmyhome.ninja/epc?certificate=2808-3055-6321-9909-2974)"} 
```
___
### Simulate Subdomain: No Parameters 
```
https://customapi.heatmyhome.ninja/simulate
```
With Simulator API you must provide all parameters otherwise the API will return an error (no paramaters are optional) 
```
{"status":404,"error":"not all parameters defined. Example parameters: customapi.heatmyhome.ninja/simulate?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=60&tes_max=0.5","inputs":{}}
```
___
### Simulate Subdomain: Not all parameters provided
```
https://customapi.heatmyhome.ninja/simulate?postcode=CV47AL?postcode=CV47AL&latitude=52.3833`
```
Not providing all parameters returns the following error. The simulator also returns a list of inputs so you can check the API interpretted your request correctly.
```
{"status":404,"error":"not all parameters defined. Example parameters: customapi.heatmyhome.ninja/simulate?postcode=CV47AL?postcode=CV47AL&latitude=52.3833?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=60&tes_max=0.5","inputs":{"postcode":"CV47AL?postcode=CV47AL","latitude":"52.3833"}}
```
___
### Simulate Subdomain: Floor area out of range
```
https://customapi.heatmyhome.ninja/simulate?postcode=CV47AL?postcode=CV47AL&latitude=52.3833?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=900000&tes_max=0.5
```
Due to floor area affecting computation time, floor area is limited to a max of 1500m<sup>2</sup>. Note not all parameters are not limit checked server side, as this is done client side. However limiting floor area is important to prevent the server being stressed incase a large floor area is requested.
```
{"status":404,"error":"The floor area is set to: 900000. This is either not a number, less than 25 m^2, or greater than 1500m^2","inputs":{"postcode":"CV47AL?postcode=CV47AL","latitude":["52.3833?postcode=CV47AL","52.3833"],"longitude":"-1.5833","occupants":"2","temperature":"20","space_heating":"3000","floor_area":"900000","tes_max":"0.5"}}
```
___
### Simulate Subdomain: TES max out of range
```
https://customapi.heatmyhome.ninja/simulate?postcode=CV47AL?postcode=CV47AL&latitude=52.3833?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=60&tes_max=4
```
Due to TES max affecting computation time, TES max is limited to a max of 3.0m<sup>2</sup>. Note not all parameters are not limit checked server side, as this is done client side. However limiting floor area is important to prevent the server being stressed incase a large TES max is requested.
```
{"status":404,"error":"The tes-max is set to: 4. This is either not a number, less than 0.1 m^3, or greater than 3.0m^3","inputs":{"postcode":"CV47AL?postcode=CV47AL","latitude":["52.3833?postcode=CV47AL","52.3833"],"longitude":"-1.5833","occupants":"2","temperature":"20","space_heating":"3000","floor_area":"60","tes_max":"4"}}
