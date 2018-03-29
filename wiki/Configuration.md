Configuration
=============

Naming
------

The configuration file shall be named *ideAlarmConfig.lua* and it shall be placed in the dzVents
script folder, e.g. */path/to/domoticz/scripts/dzVents/scripts/*.

Syntax
------

The syntax for the configuration file is LUA.

Common settings for all alarm zones
-----------------------------------

*ALARM_TEST_MODE*

Initially you might want to start in Test Mode. Doing that will prevent audible alarm
output. However, you must remember to disable Test Mode again when you've finished testing. Write a
note or something! change _C.ALARM_TEST_MODE = false to _C.ALARM_TEST_MODE = true

*NAG_SCRIPT_TRIGGER_INTERVAL*

Interval for how often the script shall trigger to check if nagging about open doors needs
to be made. The format is as defined by dzVents timer trigger options. This value should be
kept low and the default value or lower is recommended. Default value is {'every other minute'}

*NAG_INTERVAL_MINUTES*

Interval for how often ideAlarm shall nag you about doors etc that you've forgot to close.

*loggingLevel*

You can specify a specific logging level for ideAlarm hence overriding the dzVents default
logging level. Below is an example.

~~~~ lua
_C.loggingLevel = function(domoticz)
  return domoticz.LOG_INFO -- Select one of LOG_DEBUG, LOG_INFO, LOG_ERROR, LOG_FORCE to override system log level
end
~~~~

Have a look at the example configuration file where to define it. It's commented out by default.

Configuration of the alarm zones
--------------------------------

The example config file has defined a single alarm zone. If you need multiple alarm zones, you might want to have a look at The double zones example config file.

The benefit of having multiple alarm zones is that you can arm them differently. That is, you might want to arm your home as 'Armed Home' while you want the tenants area 'Disarmed' and the garden shed armed as 'Armed Away'. However don't over-complicate things by creating unnecessary alarm zones as it's very easy to add more zones afterwards.

Below is an example of an alarm zone. (It's not the complete configuration file)

~~~~ lua
{
  name='My Home',
  armingModeTextDevID=550,
  statusTextDevID=554,
  entryDelay=15,
  exitDelay=20,
  alertDevices={'Siren', 'Garden Lights'},
  sensors = {
    ['Entrance Door'] = {['class'] = SENSOR_CLASS_A, ['nag'] = true, ['nagTimeoutMins'] = 5, ['armWarn'] = true, ['enabled'] = true},
    ['Another Door'] = {['class'] = SENSOR_CLASS_A, ['nag'] = true, ['nagTimeoutMins'] = 5, ['armWarn'] = true, ['enabled'] = true},

    -- enabled can be a boolean or a function as in the example below
    -- The sensor below will only trigger the alarm if
    -- "Master" is not at home and it's dark
    ['Garden Shed Door'] = {['class'] = SENSOR_CLASS_A, ['nag'] = true, ['nagTimeoutMins'] = 5, ['armWarn'] = true, ['enabled'] =
      function(domoticz)
        return (domoticz.devices('Master Present').state ~= 'On'
          and domoticz.time.isNightTime)
      end},

  },
  armAwayToggleBtn='Toggle Z1 Arm Away',
  armHomeToggleBtn='Toggle Z1 Arm Home',
  mainZone = true,
  canArmWithTrippedSensors = true,
  syncWithDomoSec = true, -- Only one zone is allowed to sync with Domoticz built in Security Panel
},
~~~~

*name*: (Required)
  - Give your alarm zone a descriptive name.

*armingModeTextDevID*: (Required)
  - The Domoticz virtual text device idx that You created for this zone. Holds the zones arming mode.

*statusTextDevID*: (Required)
  - The Domoticz virtual text device idx that You created for this zone. Holds the zones event status.

*entryDelay*: (Required)
  - Entry delay in seconds. Valid range: 0-999. Default setting: 15.

*exitDelay*: (Required)
  - Exit delay in seconds. Valid range: 0-999. Default setting: 60.

*alertDevices*: (Elements are Optional)
  - A Lua table containing the named Domoticz devices that shall be automatically switched on during an
    alert situation. Typically you put your siren devices names here but it can actually be any kind of
    Domoticz devices that can be switched "On" and "Off". If you have no alert devices or you'd like to
    control them using custom logic you should supply an empty table {}.

*sensors*:
  - See [Sensors](#sensors-in-the-configuration-file).

*armAwayToggleBtn*: (Required)
  - Switch device to toggle alarm status between Disarmed and Armed away.

*armHomeToggleBtn*: (Required)
  - Switch device to toggle alarm status between Disarmed and Armed home.

*mainZone*: (Required)
  - Set this to true if this is your main zone. Otherwise set this to false. (The main will be the default zone).
    You don't need to have a main zone but if you define one, it's important that you define only a single zone
    as your main zone.

*canArmWithTrippedSensors*: (Required)
  - Set this to true if you want to be able to arm this zone even if sensors are tripped when arming. If set to
    false, arming attempts with tripped sensors won't be possible and will cause an error.

*syncWithDomoSec*: (Required)
  - Set this to true if you'd like to synchronize arming mode changes with Domoticz's built in Security Panel.
    Synchronization is bi-directional. Only a single zone is allowed to sync with Domoticz's built-in
    Security Panel.

Sensors in the configuration file
---------------------------------

*Sensor Name*:
  - String
  - The key value for each defined sensor. This must be the name of a Domoticz device.

*class*:
  - String
  - Must be set to one of SENSOR_CLASS_A or SENSOR_CLASS_B. Sensors defined as SENSOR_CLASS_A will trigger
    in any of domoticz.SECURITY_ARMED_HOME and domoticz.SECURITY_ARMED_AWAY. Sensors defined as SENSOR_CLASS_B
    will trigger in domoticz.SECURITY_ARMED_AWAY.

*nag*:
  - Boolean
  - Reserved for future functionality. The idea is to make ideAlarm periodically announce (nag) if a sensor
    has been left open for a period of time.

*nagTimeoutMins*:
  - Integer
  - The number of minutes after which a sensor will be regarded as timed out and therefore will be nagged about.
    Nagging will only occur for sensors in disarmed zones and also for SENSOR_CLASS_B sensors when the zone is
    armed home. Nagging will not occur for disabled sensors. Also, nagging will only occur for sensors that has
    *armWarn* set to true.

*armWarn*:
  - Boolean
  - Can be set to false if you wish to exclude the sensor from being checked when arming a zone with tripped
    sensors. This setting will also affect if the sensor will be nagged about. Default value is true.

*enabled*:
  - Boolean or Function
  - Set to true in normal circumstances. It can be set to false to exclude the sensor from the alarm zone. When
    false, it won't trigger alarms and *armWarning*, and nagging will not occur. Default value is true. This can
    alternatively be defined as a function. By using a function you can, for example, make a sensor enabled only
    when it's dark or depending on the state of other domoticz devices.

Only a single zone can be synchronized with Domoticz
----------------------------------------------------

If you have defined multiple alarm zones, please be aware that only a single zone is allowed to sync with Domoticz built in Security Panel. Please also check your configuration file that one of your zones (only one) is defined as mainZone.
Check that your configuration file is valid Lua Script

Validation
----------
After editing your configuration file, always check that it's valid:

Copy and paste your configuration file into the form on codepad to verify that it has the
correct LUA syntax. Don't forget to mark your paste as "Private" so it won't be seen by the public.
