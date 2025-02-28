### MQTT

MQTT and MQTTS (secure) connections are supported for status and control.

At startup the following message is published with a retain flag to `openevse/announce/xxxx` where `xxxx` is the last 4 characters of the device ID. This message is useful for device discovery and contans the device hostname and IP address.

```json
{
  "state":"connected",
  "id":"c44f330dxxad",
  "name":"openevse-55ad",
  "mqtt":"emon/openevse-55ad",
  "http":"http://192.168.1.43/"
}
```

For device discovery you should subscribe with a wild card to `openevse/announce/#`

When the device disconnects from MQTT the same message is posted with `state":"disconnected"` (Last Will and Testament).

All subsequent MQTT status updates will by default be be posted to `openevse-xxxx` where `xxxx` is the last 4 characters of the device ID. This base-topic can be changed via the MQTT service page.

#### OpenEVSE Status via MQTT

OpenEVSE can post its status values (e.g. amp, wh, temp1, temp2, temp3, pilot, status) to an MQTT server. Data will be published as a sub-topic of base topic, e.g `<base-topic>/amp`. Data is published to MQTT every 30s.

**The default `<base-topic>` is `openevse-xxxx` where `xxxx` is the last 4 characters of the device ID**

Claims & manual override are read accessible here:
`<base-topic>/override/`                    : get manual override ([json data], {"state": "null"} when there's no override)  
`<base-topic>/override/set <json data> `    : set/update manual override ( data as refered from API : https://openevse.stoplight.io/docs/openevse-wifi-v4/e0ab0a4ad5e1e-set-the-manual-override )  
`<base-topic>/override/set toggle`          : toggle manual override  
`<base-topic>/override/set clear`           : clear manual override  

`<base-topic>/claim/`                       : get mqtt service claim ([json data], {"state": "null"} when there's no claim )  
`<base-topic>/claim/set <json data>`        : set/update claim from MQTT service. Has same priority as HTTP service. ( data as refered from API : https://openevse.stoplight.io/docs/openevse-wifi-v4/ebc578ffa7ca7-make-update-an-evse-claim )  
`<base-topic>/claim/set release`            : release claim  

Claim & override properties can be set independantly. Sending json with only some fields will update the current claim properties only. 
To remove a selected claim/override property, just send "clear" as property parameter  ( i.e. `<base-topic>/claim/set {"charge_current": "clear"}` )

Scheduler data:
`<base-topic>/scheduler/`    				: get scheduler data ([json data]  
`<base-topic>/scheduler/set <json data>`	: set/update schedules ( data as refered from API: https://openevse.stoplight.io/docs/openevse-wifi-v4/e87e6f3f90787-batch-update-schedule )  
`<base-topic>/scheduler/clear <id>          :`remove related event  

Main settings:

`<base-topic>/divertmode/set [1 | 2]`       : enable (1)/ disable (2) divert mode  
`<base-topic>/shaper/set [0 | 1]`       : temporary enable (1)/ disable (0) current shaper ( doesn't survive reboot )  


MQTT setup is pre-populated with OpenEnergyMonitor [emonPi default MQTT server credentials](https://guide.openenergymonitor.org/technical/credentials/#mqtt).

* Enter MQTT server host and base-topic
* (Optional) Enter server authentication details if required
* Click connect
* After a few seconds `Connected: No` should change to `Connected: Yes` if connection is successful. Re-connection will be attempted every 10s. A refresh of the page may be needed.

*Note: `emon/xxxx` should be used as the base-topic if posting to emonPi MQTT server if you want the data to appear in emonPi Emoncms. See [emonPi MQTT docs](https://guide.openenergymonitor.org/technical/mqtt/).*
