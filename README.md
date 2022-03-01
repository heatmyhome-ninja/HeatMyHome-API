# HeatMyHome API

This repository contains the code used to run the API server used by the [HeatMyHome-Website](https://github.com/heatmyhome-ninja/HeatMyHome-Website).
Although the API is currently not publically callable, due to limited compute hours you may wish to read the code and coumentation to develop your own API / Heating Simulator, or host the server for your own use.

# Documentation

The URL of the API is: [customapi.heatmyhome.ninja](https://customapi.heatmyhome.ninja)

The API returns JSON objects, which contains the following keys: "status", "result", "error", with "result" and "error" keys being conditional upon the status. A list of example requests are provided below to show the possible outputs of the API dependent on the appended subdomain / url parameters.

Request-Response List
- [No Subdomain or Parameters](#no-subdomain-or-parameters)
- [EPC Subdomain: No Parameters](#epc-subdomain-no-parameters)
- [EPC Subdomain: Invalid Postcode](#epc-subdomain-invalid-postcode)
- [EPC Subdomain: Invalid EPC Certificate](#epc-subdomain-invalid-epc-certificate)
- [EPC Subdomain: Unhandled Error](#epc-subdomain-unhandled-error)
- [EPC Subdomain: Valid Postcode](#epc-subdomain-valid-postcode)
- [EPC Subdomain: Valid EPC Certificate](#epc-subdomain-valid-epc-certificate)
- [Simulate Subdomain: No Parameters](#simulate-subdomain-no-parameters)
- [Simulate Subdomain: Not All Parameters Provided](#simulate-subdomain-not-all-parameters-provided)
- [Simulate Subdomain: Floor Area Out of Range](#simulate-subdomain-floor-area-out-of-range)
- [Simulate Subdomain: TES Max Out of Range](#simulate-subdomain-tes-max-out-of-range)
- [Simulate Subdomain: Unhandled Error](#simulate-subdomain-unhandled-error)
- [Simulate Subdomain: Timeout Error](#simulate-subdomain-timeout-error)
- [Simulate Subdomain: Successful Simulation](#simulate-subdomain-successful-simulation)
___
### No Subdomain or Parameters 
https://customapi.heatmyhome.ninja

When no parameters entered API returns recommendation to goto either EPC API or Simulator subdomain. 
```json
{
    "status": 404,
    "error": "must call customapi.heatmyhome.ninja/epc or customapi.heatmyhome.ninja/simulate with their respective url parameters",
    "info": "API Version 0.9"
}
```
___
### EPC Subdomain: No Parameters 
https://customapi.heatmyhome.ninja/epc

With EPC API you may request a list of addresses and EPC certificate numbers for a given postcode, or request the EPC Space Heating Value and Floor Area of a given property.
```json
{
    "status": 404,
    "error": "must provide url parameter (either customapi.heatmyhome.ninja/epc?postcode=CV47AL or customapi.heatmyhome.ninja/epc?certificate=2808-3055-6321-9909-2974)"
}
```
___
### EPC Subdomain: Invalid Postcode
https://customapi.heatmyhome.ninja/epc?postcode=ABCDEFG

If the postcode is not recognised by the gov.uk epc register it will return this error.
```json
{
    "status": 404,
    "error": "must provide url parameter (either customapi.heatmyhome.ninja/epc?postcode=CV47AL or customapi.heatmyhome.ninja/epc?certificate=2808-3055-6321-9909-2974)"
}
```
___
### EPC Subdomain: Invalid EPC Certificate
https://customapi.heatmyhome.ninja/epc?certificate=1234-5678-1234-5678-1234

If the certificate number is not recognised by the gov.uk epc register it will return this error.
```json
{
    "status": 404,
    "error": "certificate <1234-5678-1234-5678-1234> is not valid"
}
```

___
### EPC Subdomain: Unhandled Error
https://customapi.heatmyhome.ninja/epc?certificate=1234-5678-1234-5678-1234

In the event that any unhandled error occurs, such as if the gov.uk website went down, the following error is returned where INSERT_ERROR_HERE would be replaced by the unhandled error message.
```json
{
    "status": 404,
    "error": "An unhandled error occured. INSERT_ERROR_HERE",
}
```
___
### EPC Subdomain: Valid Postcode
https://customapi.heatmyhome.ninja/epc?certificate=1234-5678-1234-5678-1234

If postcode is valid a list of addresses and their corresponding epc certicate numbers are returned. However the postcode may not contain any domestic EPC certicates, in which can the array is empty. Note JSON result address list shortened for demonstration purposes.
```json
{
    "status": 200,
    "result": [
        ["Bluebell Warden, Bluebell Residences, COVENTRY, CV4 7AL", "2808-3055-6321-9909-2974"],
        ["The Gatehouse, The Westwood Site, COVENTRY, CV4 7AL", "8901-8006-7229-6896-8143"],
        ["Toar Cottage, Gibbet Hill Road, COVENTRY, CV4 7AL", "2018-8084-6235-6021-5020"],
        ["University of Warwick, Gibbet Hill Road, COVENTRY, CV4 7AL", "0740-2870-6466-0891-7525"],
    ]
}
```
___
### EPC Subdomain: Valid EPC Certificate
https://customapi.heatmyhome.ninja/epc?certificate=1234-5678-1234-5678-1234

If EPC certifcate is valid floor area, space heating value and valid until date return. Valid until date not currently utilised by website. EPC certicate may not contain an floor area or space heating value, in which case the value for those keys are just returned as an empty string, and the client must check for empty strings. Client must also filter out numbers from units (which can be done using regex).
```json
{
    "status": 200,
    "result": {
        "floor-area": "109 square metres",
        "space-heating": "5963 kWh per year",
        "valid-until": "19 September 2021"
    }
}
```
___
### Simulate Subdomain: No Parameters 
https://customapi.heatmyhome.ninja/simulate

With Simulator API you must provide all parameters otherwise the API will return an error (no paramaters are optional) 
```json
{
    "status": 404,
    "error": "not all parameters defined. Example parameters: customapi.heatmyhome.ninja/simulate?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=60&tes_max=0.5",
    "inputs": {}
}
```
___
### Simulate Subdomain: Not all parameters provided
https://customapi.heatmyhome.ninja/simulate?postcode=CV47AL?postcode=CV47AL&latitude=52.3833

Not providing all parameters returns the following error. The simulator also returns a list of inputs so you can check the API interpretted your request correctly.
```json
{
    "status": 404,
    "error": "not all parameters defined. Example parameters: customapi.heatmyhome.ninja/simulate?postcode=CV47AL?postcode=CV47AL&latitude=52.3833?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=60&tes_max=0.5",
    "inputs": {
        "postcode": "CV47AL?postcode=CV47AL",
        "latitude": "52.3833"
    }
}
```
___
### Simulate Subdomain: Floor Area Out of Range
https://customapi.heatmyhome.ninja/simulate?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=900000&tes_max=0.5

Due to floor area affecting computation time, floor area is limited to a max of 1500m<sup>2</sup>. Note not all parameters are not limit checked server side, as this is done client side. However limiting floor area is important to prevent the server being stressed incase a large floor area is requested.
```json
{
    "status": 404,
    "error": "The floor area is set to: 900000. This is either not a number, less than 25 m^2, or greater than 1500m^2",
    "inputs": {
        "postcode": "CV47AL",
        "latitude": "52.3833",
        "longitude": "-1.5833",
        "occupants": "2",
        "temperature": "20",
        "space_heating": "3000",
        "floor_area": "900000",
        "tes_max": "0.5"
    }
}
```
___
### Simulate Subdomain: TES Max Out of Range
https://customapi.heatmyhome.ninja/simulate?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=60&tes_max=4

Due to TES max affecting computation time, TES max is limited to a max of 3.0m<sup>2</sup>. Note not all parameters are not limit checked server side, as this is done client side. However limiting floor area is important to prevent the server being stressed incase a large TES max is requested.
```json
{
    "status": 404,
    "error": "The tes-max is set to: 4. This is either not a number, less than 0.1 m^3, or greater than 3.0m^3",
    "inputs": {
        "postcode": "CV47AL",
        "latitude": "52.3833",
        "longitude": "-1.5833",
        "occupants": "2",
        "temperature": "20",
        "space_heating": "3000",
        "floor_area": "60",
        "tes_max": "4"
    }
}
```
___
### Simulate Subdomain: Unhandled Error
https://customapi.heatmyhome.ninja/simulate?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=60&tes_max=0.5

In the event that any unhandled error occurs, such as a bug in the WASM code, the following error is returned where INSERT_ERROR_HERE would be replaced by the unhandled error message.
```json
{
    "status": 404,
    "error": "An unhandled error occured. INSERT_ERROR_HERE",
}
```

___
### Simulate Subdomain: Timeout Error
https://customapi.heatmyhome.ninja/simulate?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=60&tes_max=0.5

Simulations should take no more than a few seconds to run if the server is not processing any other requests. However in the event of heavy traffic a simulation may take longer. Once the server begins process a requests it is given 30s to process it before it returns a timeout error.
```json
{
    "status": 404,
    "error": "simulation exceeded allowed runtime: 30000 ms. Server may be busy.",
    "inputs": {
        "postcode": "CV47AL",
        "latitude": "52.3833",
        "longitude": "-1.5833",
        "occupants": "2",
        "temperature": "20",
        "space_heating": "3000",
        "floor_area": "60",
        "tes_max": "0.5"
    }
}
```
___
### Simulate Subdomain: Successful Simulation
https://customapi.heatmyhome.ninja/simulate?postcode=CV47AL&latitude=52.3833&longitude=-1.5833&occupants=2&temperature=20&space_heating=3000&floor_area=60&tes_max=0.5

Successful simulation returns the optimal properties per system. It also includes the calaculated thermal transmittance and demand values for the property, however these are not currently utilised by the website. If the user request to save the results the result key of the JSON object is what is saved in order to reload results later.
```json
{
    "status": 200,
    "inputs": {
        "postcode": "CV47AL",
        "latitude": "52.3833",
        "longitude": "-1.5833",
        "occupants": "2",
        "temperature": "20",
        "space_heating": "3000",
        "floor_area": "60",
        "tes_max": "0.5"
    },
    "result": {
        "thermal-transmittance": 1,
        "optimised-epc-demand": 2992,
        "npc-years": 20,
        "demand": {
            "boiler": {
                "hot-water": 1460,
                "space": 2737,
                "total": 4198,
                "peak-hourly": 9.7393
            },
            "heat-pump": {
                "hot-water": 1460,
                "space": 2845,
                "total": 4305,
                "peak-hourly": 1.6715
            }
        },
        "systems": {
            "electric-boiler": {
                "none": {
                    "pv-size": 0,
                    "solar-thermal-size": 0,
                    "thermal-energy-storage-volume": 0.2,
                    "operational-expenditure": 373,
                    "capital-expenditure": 949,
                    "net-present-cost": 6435,
                    "operational-emissions": 924094
                },
                "photovoltaic": {
                    "pv-size": 14,
                    "solar-thermal-size": 0,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 173,
                    "capital-expenditure": 3759,
                    "net-present-cost": 6308,
                    "operational-emissions": 515563
                },
                "flat-plate": {
                    "pv-size": 0,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.2,
                    "operational-expenditure": 316,
                    "capital-expenditure": 3527,
                    "net-present-cost": 8182,
                    "operational-emissions": 774946
                },
                "evacuated-tube": {
                    "pv-size": 0,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.2,
                    "operational-expenditure": 305,
                    "capital-expenditure": 3637,
                    "net-present-cost": 8129,
                    "operational-emissions": 746851
                },
                "flat-plate-and-photovoltaic": {
                    "pv-size": 12,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 168,
                    "capital-expenditure": 5896,
                    "net-present-cost": 8372,
                    "operational-emissions": 477528
                },
                "evacuated-tube-and-photovoltaic": {
                    "pv-size": 12,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 147,
                    "capital-expenditure": 6006,
                    "net-present-cost": 8172,
                    "operational-emissions": 424266
                },
                "photovoltaic-thermal-hybrid": {
                    "pv-size": 2,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.2,
                    "operational-expenditure": 300,
                    "capital-expenditure": 4957,
                    "net-present-cost": 9366,
                    "operational-emissions": 747550
                }
            },
            "air-source-heat-pump": {
                "none": {
                    "pv-size": 0,
                    "solar-thermal-size": 0,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 145,
                    "capital-expenditure": 6238,
                    "net-present-cost": 8369,
                    "operational-emissions": 355062
                },
                "photovoltaic": {
                    "pv-size": 14,
                    "solar-thermal-size": 0,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": -107,
                    "capital-expenditure": 9318,
                    "net-present-cost": 7751,
                    "operational-emissions": -23068
                },
                "flat-plate": {
                    "pv-size": 0,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 128,
                    "capital-expenditure": 8815,
                    "net-present-cost": 10703,
                    "operational-emissions": 324390
                },
                "evacuated-tube": {
                    "pv-size": 0,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 124,
                    "capital-expenditure": 8925,
                    "net-present-cost": 10745,
                    "operational-emissions": 315712
                },
                "flat-plate-and-photovoltaic": {
                    "pv-size": 12,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": -77,
                    "capital-expenditure": 11455,
                    "net-present-cost": 10319,
                    "operational-emissions": 6972
                },
                "evacuated-tube-and-photovoltaic": {
                    "pv-size": 12,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": -90,
                    "capital-expenditure": 11565,
                    "net-present-cost": 10236,
                    "operational-emissions": -9912
                },
                "photovoltaic-thermal-hybrid": {
                    "pv-size": 2,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 111,
                    "capital-expenditure": 10245,
                    "net-present-cost": 11880,
                    "operational-emissions": 295010
                }
            },
            "ground-source-heat-pump": {
                "none": {
                    "pv-size": 0,
                    "solar-thermal-size": 0,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 90,
                    "capital-expenditure": 7938,
                    "net-present-cost": 9257,
                    "operational-emissions": 220896
                },
                "photovoltaic": {
                    "pv-size": 14,
                    "solar-thermal-size": 0,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": -198,
                    "capital-expenditure": 11018,
                    "net-present-cost": 8104,
                    "operational-emissions": -163122
                },
                "flat-plate": {
                    "pv-size": 0,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 77,
                    "capital-expenditure": 10515,
                    "net-present-cost": 11653,
                    "operational-emissions": 202183
                },
                "evacuated-tube": {
                    "pv-size": 0,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 74,
                    "capital-expenditure": 10625,
                    "net-present-cost": 11717,
                    "operational-emissions": 197829
                },
                "flat-plate-and-photovoltaic": {
                    "pv-size": 12,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": -164,
                    "capital-expenditure": 13155,
                    "net-present-cost": 10746,
                    "operational-emissions": -123339
                },
                "evacuated-tube-and-photovoltaic": {
                    "pv-size": 12,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": -173,
                    "capital-expenditure": 13265,
                    "net-present-cost": 10720,
                    "operational-emissions": -133570
                },
                "photovoltaic-thermal-hybrid": {
                    "pv-size": 2,
                    "solar-thermal-size": 2,
                    "thermal-energy-storage-volume": 0.1,
                    "operational-expenditure": 60,
                    "capital-expenditure": 11945,
                    "net-present-cost": 12833,
                    "operational-emissions": 172818
                }
            },
            "hydrogen-boiler": {
                "grey": {
                    "operational-expenditure": 229,
                    "capital-expenditure": 2120,
                    "net-present-cost": 5482,
                    "operational-emissions": 1781634
                },
                "blue": {
                    "operational-expenditure": 434,
                    "capital-expenditure": 2120,
                    "net-present-cost": 8500,
                    "operational-emissions": 279838
                },
                "green": {
                    "operational-expenditure": 858,
                    "capital-expenditure": 2120,
                    "net-present-cost": 14744,
                    "operational-emissions": 1853926
                }
            },
            "hydrogen-fuel-cell": {
                "grey": {
                    "operational-expenditure": 224,
                    "capital-expenditure": 25158,
                    "net-present-cost": 28459,
                    "operational-emissions": 1749444
                },
                "blue": {
                    "operational-expenditure": 426,
                    "capital-expenditure": 25158,
                    "net-present-cost": 31423,
                    "operational-emissions": 274782
                },
                "green": {
                    "operational-expenditure": 843,
                    "capital-expenditure": 25158,
                    "net-present-cost": 37553,
                    "operational-emissions": 1820430
                }
            },
            "gas-boiler": {
                "operational-expenditure": 187,
                "capital-expenditure": 1620,
                "net-present-cost": 4364,
                "operational-emissions": 853506
            },
            "biomass-boiler": {
                "operational-expenditure": 192,
                "capital-expenditure": 9750,
                "net-present-cost": 12570,
                "operational-emissions": 419757
            }
        }
    }
}
```

