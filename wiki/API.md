API
===

For basic usage you can interact with ideAlarm (e.g. arming and disarming) by GUI,
the Domoticz Security Panel or by scripts using the virtual switch devices that you
have created when installing. You can also check the Arming Mode and the Status of
each zone by inspecting the value of the virtual text devices. You can also make
scripts trigger on the change of of the virtual text devices.

For more advanced usage you can make any ordinary dzVents script interact with ideAlarm
using the ideAlarm API which is described on this page.

Preparations
------------

Before you can use ideAlarm API in a dzVents script you need to require the ideAlarm
module by inserting a single line of code that you find below.

~~~~ lua
local alarm = require "ideAlarmModule"
~~~~

Just insert the code at the beginning of the execute function in your dzVents script,
like in this example:

~~~~ lua
return {
  active = true,
  on = {
    devices = {
      'My switch'
    }
  },

  execute = function(domoticz, mySwitch)

    local alarm = require "ideAlarmModule"

    if (mySwitch.state == 'On') then
      domoticz.log(alarm.statusAll(domoticz), domoticz.LOG_INFO)
    end
  end
}
~~~~

If you need to trigger the script on the devices that you have defined as trigger devices
you should instead require the ideAlarm module at the very top of your script. Then see
the Triggering on the Arming Mode change example below.

The Alarm Object
----------------

The alarm object holds all information about ideAlarm. It provides global attributes
and methods to query and manipulate your ideAlarm system.

+ **alarm.statusAll(domoticz)**
    + Lists all defined alarm zones and sensors. It includes the arming mode and alarm status for all your defined alarm zones
    + Parameter
        + domoticz: (table) The domoticz object.
    + Return value
        + (string) A formatted alarm zone and sensors listing.
    + Usage example
        + domoticz.log(alarm.statusAll(domoticz), domoticz.LOG_INFO)

alarm.testAlert(domoticz)
  Turns on all your zones defined alert devices for 5 seconds. To use this function, ALARM_TEST_MODE must be set to false, otherwise an error is logged. You may schedule this function to run on a monthly basis preferably at night time.
  Parameter
      domoticz: (table) The domoticz object.
  Return value
      (boolean) true if sucessful. false if not sucessful (E.g. ALARM_TEST_MODE is enabled)
  Usage example
      local happyEvent = alarm.testAlert(domoticz)
alarm.version()
  Use this function to get the ideAlarm version string.
  Return value
      (string) The ideAlarm version.
  Usage example
      domoticz.log('ideAlarm version string: '..alarm.version(), domoticz.LOG_INFO)
alarm.qtyAlarmZones()
  Use this function to retrieve the number of ideAlarm zones that has been defined in the configuration file. Usable for example if you need to loop through all the zones.
  Return value
      (integer) The number of defined ideAlarm zones.
  Usage example
  In this example we disarm all the zones.
      for i=1, alarm.qtyAlarmZones() do alarm.zones(i).disArmZone(domoticz) end
alarm.zones(z)

  Retrieves the zone object for a zone given by name or an ordinal number. Generates a log error if a zone given by name can not be found.

  Parameter
      z: (string/integer) Optional. The alarm zone to retrieve. If this parameter is not given, it defaults to the alarm zone that is marked as the ideAlarm main zone.

  Return value
      (table) The requested ideAlarm zone object.

  Usage examples
      local myZone = alarm.zones('Laundry Room') -- Retrieves the zone named 'Laundry Room'
      local myZone = alarm.zones(1) -- Retrieves the first defined zone
      local myZone = alarm.zones() -- Retrieves the zone defined as the main zone

Alarm Object Constants
----------------------

*alarm.ZS_NORMAL*,
*alarm.ZS_ALERT*
*alarm.ZS_ERROR*
*alarm.ZS_TRIPPED*
*alarm.ZS_TIMED_OUT*
  - Possible alarm zone statuses.

*alarm.SENSOR_CLASS_A*
*alarm.SENSOR_CLASS_B*
  - Sensor classes.

The Zone Object
---------------

To work with a zone object, you'll need to retrieve it first. Use the alarm object to retrieve
one of your zones. If you need to access it repeatedly you can save the zone object in a local
variable like this: local myZone = alarm.zones('Laundry Room'). Then you can call a function
like this: myZone.disArmZone(domoticz). However if you only need to make a single call to a
function you might as well skip storing the zone object in a variable and just do something
similar to: alarm.zones('Laundry Room').disArmZone(domoticz) Below are the attributes and functions
that you can use for the ideAlarm zone object.

zone.alertDevices
    table. The alert devices collection. The Domoticz device names of the devices that shall make you aware about an alert.

zone.armAwayToggleBtn
    string. The Domoticz virtual switch device name of the device that toggles the zone between "Disarmed" and "Armed Away".

zone.armingMode(domoticz)
    Retrieves the zone's arming mode.
    Parameter
        domoticz: (table) The domoticz object.
    Return value
        (string) The arming mode. It will be one of domoticz.SECURITY_DISARMED, domoticz.SECURITY_ARMEDHOME or domoticz.SECURITY_ARMEDAWAY
    Usage example
        if (alarm.zones('My Home').armingMode(domoticz) == domoticz.SECURITY_DISARMED) then print('Heureka!') end

zone.armHomeToggleBtn
    string. The Domoticz virtual switch device name of the device that toggles the zone between "Disarmed" and "Armed Home".

zone.armingModeTextDevID
    integer. The Domoticz virtual text device idx of the device that displays the zone's arming mode.

zone.armZone(domoticz, armingMode, delay)
    Use this function to arm a zone
    Parameter
        domoticz: (table) The domoticz object.
        armingMode (string) The arming mode that the zone shall be set to. It must be one of domoticz.SECURITY_ARMEDHOME or domoticz.SECURITY_ARMEDAWAY
        delay: (integer) Optional. Number of seconds to wait before the new arming mode will be set.
    Return value
        (nil)
    Usage example
        alarm.zones('My Home').armZone(domoticz, domoticz.SECURITY_ARMEDAWAY, 20)

zone.canArmWithTrippedSensors
    boolean. Determines whether this zone allows arming even though there are tripped sensors at the moment of arming. Individual sensors can be excluded from this requirement by setting the sensor configuration value armWarn to false.

zone.disarmZone(domoticz)
    Use this function to disarm a zone
    Parameter
        domoticz: (table) The domoticz object.
    Return value
        (nil)
    Usage example
        alarm.zones('My Home').disarmZone(domoticz)

zone.entryDelay
    integer. The zone's entry delay in seconds.

zone.exitDelay
    integer. The zone's exit delay in seconds.

zone.isArmed(domoticz)
    Checks if the whether the zone is armed (in any of the 2 arming modes domoticz.SECURITY_ARMEDHOME and domoticz.SECURITY_ARMEDAWAY)
    Parameter
        domoticz: (table) The domoticz object.
    Return value
        (boolean) true if armed. false if disarmed.
    Usage example
        if alarm.zones('My Home').isArmed(domoticz) then domoticz.log('Safe and sound', domoticz.LOG_INFO) end

zone.isArmedAway(domoticz)
    Checks if the whether the zone is armed away (domoticz.SECURITY_ARMEDAWAY)
    Parameter
        domoticz: (table) The domoticz object.
    Return value
        (boolean) true if Armed Away, otherwise false.
    Usage example
        if alarm.zones('My Home').isArmedAway(domoticz) then domoticz.log('Safe and sound', domoticz.LOG_INFO) end

zone.isArmedHome(domoticz)
    Checks if the whether the zone is armed home (domoticz.SECURITY_ARMEDHOME)
    Parameter
        domoticz: (table) The domoticz object.
    Return value
        (boolean) true if Armed Home, otherwise false.
    Usage example
        if alarm.zones('My Home').isArmedHome(domoticz) then domoticz.log('Safe and sound', domoticz.LOG_INFO) end

zone.mainZone
    boolean. Determines whether the zone has been defined as the main zone. (E.g default zone)

zone.name
    string. The zone's name.

zone.sensorConfig(sName)
    Looks up and returns the sensor configuration object by given sensor name. Generates an error if a sensor can not be found.
    Parameter
        sName: (string) The name of the sensor that the configuration shall be retrieved for.
    Return value
        (table) The sensor configuration table.
    Usage example
        domoticz.log(alarm.zones('My Home').sensorConfig('My Door').nagTimeoutSecs, domoticz.LOG_INFO)

zone.sensors
    table. The zone's sensors.

zone.status(domoticz)
    Retrieves the zone's status.
    Parameter
        domoticz: (table) The domoticz object.
    Return value
        (string) The alarm zone's status. See possible statuses.

zone.statusTextDevID
    integer. The Domoticz virtual text device idx of the device that displays the zone's status.

zone.syncWithDomoSec
    boolean. Determines whether this zone's status shall synchronize with the Domotic's built in security panel.

zone.toggleArmingMode(domoticz, armingMode)
    Use this function to toggle the arming mode between "Disarmed" and the supplied armingMode.
    Parameter
        domoticz: (table) The domoticz object.
        armingMode (string) The arming mode that the zone shall be set to. It must be one of domoticz.SECURITY_ARMEDHOME or domoticz.SECURITY_ARMEDAWAY
    Return value
        (nil)
    Usage example
        alarm.zones('My Home').toggleArmingMode(domoticz, domoticz.SECURITY_ARMEDAWAY)

zone.trippedSensors(domoticz, mins, armingMode, isArming)
    Use this function to retrieve a list of tripped sensors for the zone. (E.g. sensors with a state of "Open", "On") Sensors that have been configured as disabled won't be included. Also, this function will respect the sensor classes (alarm.SENSOR_CLASS_A and alarm.SENSOR_CLASS_B), that is the sensor is considered tripped in the context of it's class together with the arming mode.
    Parameter
        domoticz: (table) The domoticz object.
        mins: (integer) Optional. The number of minutes that a sensor must have changed it's state within to be included in the result. (Useful because a burglar suspect may actually close the door after opening it!) If the mins parameter is set to 0, only the currently tripped sensors will be returned. If omitted it will default to 1 minute.
        armingMode: (string) Optional. (Any of the 3 arming modes domoticz.SECURITY_DISARMED , domoticz.SECURITY_ARMEDHOME and domoticz.SECURITY_ARMEDAWAY) A sensor will be considered tripped in the context of an arming mode. If omitted it will default to the zones current arming mode.
        isArming: (boolean) Optional. If this variable is set to true this function will ignore sensors that have been configured with armWarn == false. It's typically used in an arming attempt scenario.
    Return value
        (table) A table with the Domoticz device objects
    Usage example
        for _, sensor in ipairs(alarm.zones('My Home').trippedSensors(domoticz, 1, domoticz.SECURITY_ARMEDAWAY, isArming)) do msg = msg..sensor.name..' tripped @ '..sensor.lastUpdate.raw..'. ' end

The Sensor Configuration Object
-------------------------------

To get the sensor configuration object for a zone, you'll need to retrieve it . Use the zone
objects function sensorConfig(sName) to retrieve one of your sensor configuration object.

If you need to access it repeatedly you can save the sensor configuration object in a local
variable like this: local sensorConfig = alarm.zones('My Home').sensorConfig('My Door'). Then
you can access the configuration like this: sensorConfig.nagTimeoutSecs. However if you only
need to get a single configuration value you might as well skip storing the sensor configuration
object in a variable and just do something similar to:

alarm.zones('My Home').sensorConfig('My Door').nagTimeoutSecs

Below are the attributes that you can use for the ideAlarm sensor configuration object.

sensorConfig.armWarn
    boolean. Determines if the sensor shall be included in the tripped sensors warning while attempting to arm. If set to false, the sensor will also bypass the requirement that all sensors must be untripped before arming a zone when the zone option canArmWithTrippedSensors is disabled

sensorConfig.class
    string. The sensors class. It shall be one of alarm.SENSOR_CLASS_A and alarm.SENSOR_CLASS_B.

sensorConfig.enabled
    boolean. If set to false, ideAlarm will ignore this sensor completely.

sensorConfig.nag
    boolean. Reserved for future functionality.

sensorConfig.nagTimeoutSecs
    integer. Reserved for future functionality.
