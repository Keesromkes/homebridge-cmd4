# Homebridges-cmd4 - CMD4 Developers Guide.

<BR><BR>
## Table of Contents
* [**About CMD4 Developers Guide**](#about-cmd4-developers-guide)
* [**Where to Begin**](#where-to-begin)
* [**Homebridge API**](#homebridge-api)
* [**Platform Accessories**](#platform-accessories)
* [**Standard Accessories**](#standard-accessories)
* [**Migrating from Homebridge-cmdswitch2**](#migrating-from-homebridge-cmdswitch2)
* [**Developer Notes**](#developer-notes)
* [**Adding in fakegato history**](#adding-in-fakegato-history)
* [**Unit Testing**](#unit-testing)
* [**License**](#license)

<BR><BR>
## About CMD4 Developers Guide
&nbsp;&nbsp;&nbsp; This document will help you understand what is needed to integrate your own scripts into Cmd4.

<BR><BR>
## Where to begin
&nbsp;&nbsp;&nbsp; Cmd4 comes with a fully populated and documented config.json file that points to a fully populated and configured State.js file. These are excellent places of reference.<BR>
&nbsp;&nbsp;&nbsp; Next you should look at scripts that might already exist. Within the Cmd4 directory structure there is a path of "Extras/Cmd4Scripts/ExampleScripts" that may already exist for you as a starting point.<BR>
* [**basic_ping.sh**](https://github.com/ztalbot2000/homebridge-cmd4/raw/master/Extras/Cmd4Scripts/Examples/basic_ping.sh)<BR>
* [**advanced_ping.sh**](https://github.com/ztalbot2000/homebridge-cmd4/raw/master/Extras/Cmd4Scripts/Examples/advanced_ping.sh)<BR>
* [**wakeonlan.sh**](https://github.com/ztalbot2000/homebridge-cmd4/raw/master/Extras/Cmd4Scripts/Examples/wakeonlan.sh)<BR>

<BR><BR>

## Homebridge API
&nbsp;&nbsp;&nbsp; Cmd4 is not possible without Homebridge. Prior to Cmd4 Version 3, Cmd4 just created Standard Plugin Accessories. With Version 3 of Cmd4, Cmd4 follows the Hombridge API as defined on [Homebridge API](https://developers.homebridge.io/#/) to be followed exactly. Both Platform and Accessory Plugins can be created. In fact the examples can be recreated exactly.<BR>
<BR>Note: The major difference in CMD4 Version 3 is that the default accessory created is a Platform Plugin as compared to an Accessory Plugin. This is beneficial as many more Platform with the same type can be created and they can be published externally as we shall see.<BR>
<BR><BR>

## Platform Accessories
&nbsp;&nbsp;&nbsp; The best way to explain the difference is to understand how the Homebridge API defines a Television as a Platform Accessory. [Homebridge Television](https://developers.homebridge.io/#/service/Television). CMD4 Version 3 can recreate the exact same configuration as:<as:

```json
{
   "bridge":
   {
      "name": "MAC Test Homebridge",
      "username": "CC:22:3D:E3:CE:30",
      "port": 51826,
      "pin": "555-55-555"
   },
   "platforms" :
   [
      {
         "platform": "Cmd4",
         "outputConstants":                false,
         "accessories" :
         [
            {
              "type":                      "Television",
              "category":                  "TELEVISION",
              "publishExternally":         true,
              "name":                      "Example TV",
              "active":                    "ACTIVE",
              "activeIdentifier":           1,
              "configuredName":            "Example TV",
              "sleepDiscoveryMode":        "ALWAYS_DISCOVERABLE",
              "accessories":
              [
                 {  "type":                   "televisionSpeaker",
                     "displayName":            "My_TelevisionSpeaker",
                     "name":                   "My_TelevisionSpeaker",
                     "active":                 "ENABLED",
                     "volumeSelector":         10,
                     "volumeControlType":      "ABSOLUTE",
                     "state_cmd": "node .homebridge/Cmd4Scripts/State.js",
                     "polling": [
                        {"active":         "ACTIVE", "interval": 50,  "timeout": 5000},
                        {"volumeSelector": 10,       "interval": 50,  "timeout": 5000}
                     ]
                  }
              ],
              "linkedTypes":
              [
                 {"type": "InputSource",
                  "displayName": "HDMI1",
                  "configuredName":         "HDMI 1",
                  "currentVisibilityState": "SHOWN",
                  "inputSourceType":        "HDMI",
                  "isConfigured":           "CONFIGURED",
                  "identifier":              1,
                  "targetVisibilityState":    "SHOWN",
                  "name":                     "HDMI 1"
                 },
                 {"type": "InputSource",
                  "displayName":             "HDMI 2",
                   "configuredName":         "HDMI 2",
                   "currentVisibilityState": "SHOWN",
                   "inputSourceType":        "HDMI",
                   "isConfigured":           "CONFIGURED",
                   "identifier":              2,
                   "targetVisibilityState":   "SHOWN",
                   "name":                    "HDMI 2"
                  },
                  {"type":                    "InputSource",
                   "displayName":             "Netflix",
                   "configuredName":          "Netflix",
                   "currentVisibilityState":  "SHOWN",
                   "inputSourceType":         "HDMI",
                   "isConfigured":            "CONFIGURED",
                   "identifier":               3,
                   "targetVisibilityState":    "SHOWN",
                   "name":                     "Netflix"
                  }
               ],
               "displayOrder":              0,
               "currentMediaState":         "STOP",
               "targetMediaState":          "STOP",
               "pictureMode":               "STANDARD",
               "remoteKey":                 "SELECT",
               "polling": [
                  {"active":                "ACTIVE", "interval": 50,  "timeout": 5000},
                  {"activeIdentifier":       1,       "interval": 50,  "timeout": 5000},
                  {"currentMediaState":     "STOP",   "interval": 540, "timeout": 5000}
               ],
               "stateChangeResponseTime":    3,
               "state_cmd": "node .homebridge/Cmd4Scripts/State.js"
             }
          ]
       }
   ]
}
```

<BR>
Note. There are two new json designations. The first is "category"<BR>
```json
   "category": "TELEVISION"
```
<BR>
This is the hint to homekit of which icon to use and for Televisions, a TV icon will not show without it.<BR>
<BR>
The second new json designation is "publishExternally"<BR>
```json
   "publishExternally": true
```
<BR>
As per the Homebridge API, this allows the Platform Accessory to be published separately from the bridge and is a requirement for multiple TV's.<BR>

## Standard Accessories
A Standard Accessory does not need a Platform. The Homebridge example given is. [Homebridge Switch](https://developers.homebridge.io/#/api/accessory-plugins). CMD4 Version 3 can recreate the exact same configuration as:<as:

```json
{
    "bridge": {
        "name": "MAC Test Homebridge",
        "username": "CC:22:3D:E3:CE:30",
        "port": 51826,
        "pin": "555-55-555"
    },
    "accessories": [
       {
          "accessory":                "Cmd4",
          "type":                     "Switch",
          "outputConstants":          false,
          "displayName":              "My_Switch",
          "name":                     "My_Switch",
          "on":                       "FALSE",
          "Manufacturer":             "Custom Manufacturer",
          "Model":                    "Custom Model",
          "stateChangeResponseTime":   3,
          "state_cmd": "node .homebridge/Cmd4Scripts/State.js"
       }
    ]
}
```

This configuration defines a Cmd4 Standard Accessory with the designation:<BR>
```json
   "accessory": "Cmd4"
```
<BR>
Notice that there is no Platform definition. Otherwise everything is the same. You can even add linked accessories as before.<BR>
<BR><BR>

## Migrating from Homebridge-cmdswitch2
&nbsp;&nbsp;&nbsp; Homebridge-cmdswitch2 is great if you just want to turn something On or Off; Hence the switch reference in its name. In fact, there is no need to migrate if that is all you want to do.

As a plugin, Homebridge-cmd4 easily coexists with Homebridge-cmdswitch2 or any other homebridge plugin. However, if you want to do something more finite, like adjusting the brightness or getting the value of a DAC, then Homebridge-Cmd4 is for you.

&nbsp;&nbsp;&nbsp; If you do wish to move anyway to Cmd4 or wish to see another example of interfacing to a real device, here is a very simple example without any parameter checking on how it would be done for a switch.

### Step 1.  homebridge-cmdswitch2 config.json
&nbsp;&nbsp;&nbsp; Homebridge-cmdswitch2 defines their *REQUIRED* fields in their config.json as:
<BR>

```json
   ...
   "platforms": [{
   "platform": "cmdSwitch2",
   "name": "CMD Switch",
   "switches": [{
       "name" : "HTPC",
       "on_cmd": "wakeonlan XX:XX:XX:XX:XX:XX",
       "off_cmd": "net rpc shutdown -I XXX.XXX.XXX.XXX -U user%password",
       "state_cmd": "ping -c 2 -W 1 XXX.XXX.XXX.XXX | grep -i '2 received'"
   }, {
       "name" : "Playstation 4",
       "on_cmd": "ps4-waker",
       "off_cmd": "ps4-waker standby",
       "state_cmd": "ps4-waker search | grep -i '200 Ok'",
       "polling": true,
       "interval": 5,
       "timeout": 2000,
      }]
   }]
   ...

```

### Step 2.  homebridge-cmd4 config.json
&nbsp;&nbsp;&nbsp; Homebridge-cmd4 only uses one command string as there are many options beyond on/off. This command string is:
   **`"state_cmd": "< path to some executable or script >"`**

&nbsp;&nbsp;&nbsp; In this example, we will use:
   **`"state_cmd": "bash .homebridge/Cmd4Scripts/PS4.sh"`**

   Note: for the device name, DO NOT USE SPACES as this
         will cause problems parsing the command line.

&nbsp;&nbsp;&nbsp; The config.json file for homebridge-cmd4 now looks like:

```json
   ...
   {
      "platform": "Cmd4",
      "name": "Cmd4",
      "accessories":
      [
         {
             "type": "Switch",
             "name": "PS_4",
             "on": false,
             "state_cmd": "bash .homebridge/Cmd4Scripts/PS4.sh"
             "polling": true,     <OR>
             "polling": [{"on": false, "interval": 5, "timeout": 4000}
                        ],
             "interval": 5,
             "timeout": 4000,
          }
      ]
   }
   ...

```

### Step 3.  Contents of PS4.sh
&nbsp;&nbsp;&nbsp; An equivalent script is:
* [**PS4.sh**](https://github.com/ztalbot2000/homebridge-cmd4/raw/master/Extras/Cmd4Scripts/Examples/PS4.sh)
<BR><BR>

## Developer Notes
### Step 1.  The provided jsmin differs from others
&nbsp;&nbsp;&nbsp; The resulting file is still readable. Only C and C++ comments are removed. The included config.json is created via:<BR>
&nbsp;&nbsp;&nbsp; *SHELL*> `gcc jsmin.c -o jsmin`<BR>
&nbsp;&nbsp;&nbsp; *SHELL*> `jsmin < config.min.json > config.json`
 
### Step 2.  The parameters to the state_cmd
&nbsp;&nbsp;&nbsp;  These are defined as:<BR>
&nbsp;&nbsp;&nbsp; Get < Accessory Name > < Characteristic ><BR>
&nbsp;&nbsp;&nbsp; Set < Accessory Name > < Characteristic > < Value >
 
### Step 3.  Polling is supported
&nbsp;&nbsp;&nbsp; Even if you do not use polling, care was taken that all Set Target states are immediately followed by a Get of the Current state. This is so that after closing a garage door for instance, Homekit gets updated that the door was closed.
 
### Step 4.  Sending constants to your script.
&nbsp;&nbsp;&nbsp; By placing in your config.json file the tag "outputConstants": true, instead of values, your script will receive constants instead of values (Where Applicable). Homebridge-Cmd4 will except constants or values as input.  See the config.min.json file for the defined constants.
 
***Important***
&nbsp;&nbsp;&nbsp; Homebridge-cmd4 just outputs the value to be set.  For whatever reason the lower layers of homebridge set on/off to be "true" and "false" instead of 0 & 1, which is incorrect, but changing it would break others scripts.
&nbsp;&nbsp;&nbsp;  Homebridge-cmd4 has always recognized either 0/1 or true/false when receiving the devices value.
When in doubt, check the parameters yourself.
Thanks Florian for pointing out the original documented bash script was incorrect

&nbsp;&nbsp;&nbsp; Your now ready to go and try Fans, Switches, Garage Doors, Locks, Sensors ....

<BR><BR>
## Adding in fakegato-history
See [fakegato-history](https://github.com/simont77/fakegato-history)

&nbsp;&nbsp;&nbsp; Not all accessories are supported by Eve or fakegato-history. As more and more are, they can easily be added to Homebridge-Cmd4 if they are not already by following these step.

### Step 1.  Add fakegato config information
&nbsp;&nbsp;&nbsp; Edit your homebridge/config.json file and add to the accessory config portion the following lines:

```json
"fakegato":{"eve":"thermo",
            "currentTemp": "currentTemperature",
            "setTemp": "targetTemperature",
            "valvePosition": "0",
            "storage": "fs",
            "storagePath": ".homebridge/FakegatoStorage",
            "folder": "folderName",
            "keyPath": "/place/to/store/my/keys/"
           }
```

### Step 2.  Fakegato parameters
&nbsp;&nbsp;&nbsp; If you notice, the parameters follow the fakegato-history spec.

The only difference is that the characteristics value will be substituted for the fakegato keys. In this example "currentTemperature" will be substituted with 50.0 if that is what was returned to Cmd4.

The value "0" should be used for any characteristics value which is not possible to retrieve.

### Step 3.  History requires polling
&nbsp;&nbsp;&nbsp; For history to be collected you will have to enable polling and interval for the accessory, and according to the fakegato-history documents it should be less than 10 minutes (600 seconds). The new polling config section allows for each characteristic to be polled at their individual times.

```json
"polling": [{"currentHeatingCoolingState": 0,
             "interval": 540,  "timeout": 4000},
            {"currentTemperature": 50.0,
             "interval": 60,   "timeout": 4000}
           ],
```
<BR><BR>

## Unit Testing
&nbsp;&nbsp;&nbsp; Unit testing is done using the Mocha framework for Javascript and was introduced in homebridge-cmd4 version 2.1.2. There are 2796 test cases and they all run successfully.  They test the homebridge-cmd4 module to make sure that all characteristics, services and names are correct. They also test the provided State.js and PS4.sh for their respective Get/Set characteristics.  The provided config.json is also tested for proper definitions of all the homebridge-cmd4 config parameters.

&nbsp;&nbsp;&nbsp; Unit testing is only possible in a development environment and can be achieved in the following manner.

### Step 1.  Install homebridge-cmd4 in a local environment
&nbsp;&nbsp;&nbsp; This is done separate from the global environment and does not impact the global environment.<BR><BR>
&nbsp;&nbsp;&nbsp; *SHELL*> `npm install homebridge-cmd4`

### Step 2.  Change to the homebridge-cmd4 directory
&nbsp;&nbsp;&nbsp; *SHELL*> `cd homebridge-cmd4`

### Step 3.  Install homebridge-cmd4 development dependencies
&nbsp;&nbsp;&nbsp; *SHELL*> `npm install --save-dev`

### Step 4.  Run the provided test cases
&nbsp;&nbsp;&nbsp; *SHELL*> `npm test`

Note: There is one bug where the testcases do not run correctly every second attempt.  It has something to do with synchronous tests for Get/Set.  Please ignore it at this time.  Just run it again.

<BR><BR>
## License
See [LICENSE](LICENSE)



<!---
Link References (Not Local)
-->

[homebridge]:https://github.com/nfarina/homebridge
[homebridge_api]:https://developers.homebridge.io/#/
[ztalbot2000]:https://github.com/ztalbot2000
