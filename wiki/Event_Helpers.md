Event Helpers
=============

Naming
------

When you installed ideAlarm you already downloaded an Event Helpers file with
predefined but empty functions. The Event Helpers file is named *ideAlarmHelpers.lua*
and placed in the dzVents scripts folder.

Syntax
------

The syntax for the configuration file is LUA.

Custom Helper Functions for Various Alarm Events
------------------------------------------------

Every installation is unique and has it's own needs. Therefore ideAlarm has a number of
alarm events that will trigger custom script functions.

By defining custom script functions

That way you can choose whatever shall happen on those events. You define your custom
scripts as helper functions in the event helpers file. Your helper functions will typically
be able to access an alarm zone object that holds various information and provides functions
about the alarmZone that is being processed.

ideAlarm possible shared helper functions

alarmZoneNormal(domoticz, alarmZone):
-------------------------------------
 - Function
 - Called when an alarm zone is tripped.

The *domoticz* object is available to your script as the first argument.
The *alarmZone* argument is the alarm zone object.

Example script:

~~~~
alarmZoneNormal = function(domoticz, alarmZone)
  if alarmZone.name == 'My Home' then
    domoticz.devices('Alarm Indicator Light').switchOff()
  end
end,
~~~~

The example above assumes you have some kind of alarm indicator device that can be switched On/Off.

alarmZoneArming(domoticz, alarmZone):
-------------------------------------
  - Function
  - Called when an alarm is set to be armed and an exit delay starts.

This function will not be called if the exit delay is set to 0 seconds. The *domoticz* object
is available to your script as the first argument. The *alarmZone* argument is the alarm
zone object.

Example script:

~~~~
alarmZoneArming = function(domoticz, alarmZone)
  domoticz.notify('Zone will be armed',
    'The alarm zone ' .. alarmZone.name .. ' will be armed in '..alarmZone.exitDelay.. ' seconds.',
    domoticz.PRIORITY_LOW)
end,
~~~~

alarmZoneTripped(domoticz, alarmZone):
--------------------------------------
  - Function
  - Called when an alarm zone is tripped.

The *domoticz* object is available to your script as the first argument.
The *alarmZone* argument is the alarm zone object.

Example script:

~~~~
alarmZoneTripped = function(domoticz, alarmZone)
  -- A sensor has been tripped but there is still no alert
  -- We should inform whoever tripped the sensor so he/she can disarm the alarm
  -- before a timeout occurs and we get an alert
  -- In this example we turn on the kitchen lights if the zones name
  -- is 'My Home' but we could also let Domoticz speak a message or something.
  if alarmZone.name == 'My Home' then
    -- Let's do something here
    domoticz.devices('Kitchen Lights').switchOn()
  end
end,
~~~~

The example above assumes you have a domoticz device named 'Kitchen Lights' that can be switched On/Off.

alarmZoneError(domoticz, alarmZone):
------------------------------------
  - Function
  - Called when an error has occurred for an alarm zone.

The *domoticz* object is available to your script as the first argument.
The *alarmZone* argument is the alarm zone object.

Example script:

~~~~
alarmZoneError = function(domoticz, alarmZone)
  -- An error occurred for an alarm zone. Maybe a door was open when we tried to
  -- arm the zone. Anyway we should do something about it.
  domoticz.notify('Alarm Zone Error!',
    'There was an error for the alarm zone ' .. alarmZone.name,
    domoticz.PRIORITY_HIGH)
end,
~~~~

alarmZoneAlert(domoticz, alarmZone, testMode):
----------------------------------------------
  - Function
  - Called when an alarm zone's status alert occurs.

The *domoticz* object is available to your script as the first argument.
The *alarmZone* argument is the alarm zone object.
The *testMode* argument is a boolean telling you if test mode is activated or not.

You do not need to handle the zone's alert devices here (e.g. sirens). It's been
taken care of in the main script if you have configured those devices as "alertDevices"
in the configuration file.

Example script:

~~~~
alarmZoneAlert = function(domoticz, alarmZone, testMode)
  -- It's ALERT TIME!
  local msg = 'Intrusion detected in zone '..alarmZone.name..'. '
  for _, sensor in ipairs(alarmZone.trippedSensors(domoticz, 1)) do
    msg = msg..sensor.name..' tripped @ '..sensor.lastUpdate.raw..'. '
  end

  -- We don't have to turn On/Off the alert devices. That's handled by the main script.
  if not testMode then
    domoticz.notify('Alarm Zone Alert!',
      msg, domoticz.PRIORITY_HIGH)
  else
    domoticz.log('(TESTMODE IS ACTIVE) '..msg, domoticz.LOG_INFO)
  end
end,
~~~~

alarmArmingModeChanged(domoticz, alarmZone):
--------------------------------------------
  - Function
  - Called when an alarm zone's arming mode has changed.

The *domoticz* object is available for your scripting as the first argument.
The *alarmZone* argument is the alarm zone object.

Example script:

~~~~
alarmArmingModeChanged = function(domoticz, alarmZone)
  -- The arming mode for a zone has changed. We might want to be informed about that.
  local zoneName = alarmZone.name
  local armingMode = alarmZone.armingMode(domoticz)
  domoticz.notify('Arming mode change',
    'The new arming mode for ' .. zoneName .. ' is ' .. armingMode,
    domoticz.PRIORITY_LOW)
end,
~~~~

alarmZoneArmingWithTrippedSensors = function(domoticz, alarmZone, armingMode):
------------------------------------------------------------------------------
  - Function
  - Called when arming a zone and open sensors have been detected.

The *domoticz* object is available for your scripting as the first argument.
The *alarmZone* argument is the alarm zone object.
The *armingMode* argument is the new armingMode that is being set.

If *canArmWithOpenSensors* has been set, this is just an information message and
arming will proceed. However, if *canArmWithOpenSensors* hasn't been set, the
*alarmZoneError* function will be called subsequently and arming will not occur.

Example script:

~~~~
alarmZoneArmingWithTrippedSensors = function(domoticz, alarmZone, armingMode)
  -- Active sensors have been detected when arming.
  local msg = ''
  local isArming = true
  local trippedSensors = alarmZone.trippedSensors(domoticz, 0, armingMode, isArming)
  for _, sensor in ipairs(trippedSensors) do
    if msg ~= '' then msg = msg..' and ' end
    msg = msg..sensor.name
  end
  domoticz.helpers.speak(domoticz, msg, 1)
end,
~~~~

alarmNagOpenSensors = function(domoticz, alarmZone, nagSensors, lastValue):
---------------------------------------------------------------------------
  - Function
  - Called when your attention is needed about doors, windows etc. that have been left open for too long (e.g. "Nagging")

This function is also called when the situation has been resolved. Nagging will only
occur for sensors in disarmed zones and also for SENSOR_CLASS_B sensors when the zone
is armed home. Nagging will not occur for disabled sensors. Also, nagging will only
occur for sensors that have *armWarn* set to true.

The *domoticz* object is available for your scripting as the first argument.
The *alarmZone* argument is the alarm zone object.
The *nagSensors* argument is a table containing all the Domoticz device objects that are subject for nagging.

If *nagSensors* contains no elements, it means that a nagging situation has been resolved. (All doors have been closed, for example)
The *lastValue* argument holds the number of previously nagged about sensors.

Example script:

~~~~
  alarmNagOpenSensors = function(domoticz, alarmZone, nagSensors, lastValue)
    if alarmZone.name == 'My home' then
      if #nagSensors == 0 and lastValue > 0 then
        domoticz.log('The previously reported sections are now closed! Good work!', domoticz.LOG_INFO)
      elseif #nagSensors > 0 then
        local msg = ''
        for _, sensor in ipairs(nagSensors) do
          if msg ~= '' then msg = msg..' and ' end
          msg = msg..sensor.name
        end
        msg = 'Open sections in zone: '..alarmZone.name..'. '..msg
        domoticz.log(msg, domoticz.LOG_INFO)
      end
    end
  end,
~~~~

alarmOpenSensorsAllZones = function(domoticz, alarmZones):
----------------------------------------------------------
  - Function
  - Called whenever an alarm sensor state has changed for any alarm zone.

Provides you with a "open door count" for each zone. For examp,e, you can turn on a light
to inform you when a door or window has been opened. Disabled sensors will not be counted.

The *domoticz* object is available for your scripting as the first argument.
The *alarmZones* argument is a table holding all alarm zone objects.

Example script:

~~~~
  alarmOpenSensorsAllZones = function(domoticz, alarmZones)
    -- Toggle the big red lamp if there are any open sensors in 'My House'
    for _, alarmZone in ipairs(alarmZones) do
      if alarmZone.name == 'My House' then
        if (alarmZone.openSensorCount > 0) then
          domoticz.devices('Big Red Lamp').switchOn()
        elseif (alarmZone.openSensorCount == 0) then
          domoticz.devices('Big Red Lamp').switchOff()
        end
      end
    end
  end,
~~~~