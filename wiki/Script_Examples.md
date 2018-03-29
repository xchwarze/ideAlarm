Script Examples
===============

You have already seen some basic example scripts in the Arming and Disarming part.
Those were all scripts that interacted with ideAlarm using the virtual switch devices
and the virtual text devices that you've configured for each zone. Such scripts
can easily be made with any of the Domoticz's scripting environments.

There are also some script examples for Custom helper functions for various alarm events.

The scripts found on this page though are all depending on dzVents using the
ideAlarm API.

Requiring the ideAlarm module
-----------------------------

Normally you should require the ideAlarm module at the beginning of the execute
function. Like this:

~~~~
return {
  active = true,
  on = {
    devices = {'Olle i skogen'},
  },
  execute = function(domoticz, device, info)
    local alarm = require "ideAlarmModule"
    -- Some more code
  end
}
~~~~

There is an exception to this rule. That is if you need to trigger the script on the
devices that have been defined as trigger devices (and you don't want to hard code
their names). In such a case, look at this example.

Presence Detected Auto Arming
-----------------------------

In case that your Domoticz system have presence detection, e.g. knows when/if someone
is at home, you could make a script for auto arming:

~~~~
return {
  active = true,
  },
  on = {
    devices = {
      'Someone at home',
    },
  },
  execute = function(domoticz, device)
    local alarm = require "ideAlarmModule"
    if (device.name == 'Someone at home' and device.state == 'On') then
      alarm.zones('My Home').disArmZone(domoticz) -- This will disarm the zone "My Home"
    elseif (device.name == 'Someone at home' and device.state == 'Off') then
      alarm.zones('My Home').armZone(domoticz, domoticz.SECURITY_ARMEDAWAY) -- This will  the zone "My Home" to "Armed Away" after the default exit delay
    end

  end
}
~~~~

Test Alerts Regularly
---------------------

It's a very good practice to regularly check that your sirens works. You can easily
make a script for taking care of that routine:

~~~~
return {
  active = true,
  on = {
    ['timer'] = {
      'at 03:44 on tue'
    }
  },
  execute = function(domoticz, device)
    local alarm = require "ideAlarmModule"
    alarm.testAlert(domoticz)
  end
}
~~~~

Triggering on the Arming Mode Change of an Alarm Zone
-----------------------------------------------------

This is an exception. Here we require the ideAlarm module at the very top of your
script because we need to trigger the script on the devices that have been defined
as trigger devices. As you see on the following script the ideAlarm module is required
at the top of the script file (instead of requiring it in the execute function). Doing
so will probably require a little bit more processing time from the machine running
Domoticz, so avoid it if possible and you will save a couple of 1/100 parts of a second.

~~~~
local alarm = require "ideAlarmModule"

return {
  active = true,
  },
  on = {
    devices = {
      alarm.zones('My Home').armingModeTextDevID
    },
  },
  execute = function(domoticz, device)

    -- Handle the Alarm Status Indicator
    if (device.id == alarm.zones('My Home').armingModeTextDevID) then
      if device.state == domoticz.SECURITY_DISARMED then
        domoticz.devices('Alarm Status Indicator').switchOn() -- Light off
      else
        domoticz.devices('Alarm Status Indicator').switchOff() -- Red light on
      end
    end

  end
}
~~~~

Auto Arm Home at Sunset and Auto Disarm at Sunrise
--------------------------------------------------

(This is probably not a good idea, but for the sake of the example, here it goes!)

~~~~
return {
  active = true,
  on = {
    timer = {
      'at sunset',
      'at sunrise'
    }
  },

  execute = function(domoticz, _, triggerInfo)
    local alarm = require "ideAlarmModule"

    if ((triggerInfo.trigger == 'at sunset')
    and (alarm.zones('My Home').armingMode(domoticz) ~= domoticz.SECURITY_ARMED_HOME)
    and (alarm.zones('My Home').armingMode(domoticz) ~= domoticz.SECURITY_ARMED_AWAY)) then
      alarm.zones('My Home').armZone(domoticz, domoticz.SECURITY_ARM_HOME)
    elseif ((triggerInfo.trigger == 'at sunrise')
    and (alarm.zones('My Home').armingMode(domoticz) ~= domoticz.SECURITY_DISARMED)) then
      alarm.zones('My Home').disArmZone(domoticz)
    end
  end
}
~~~~

Disarm All Zones
----------------

There might be occasions when you have no time to think and fast need to disarm
all zones. This will also fast and effectively turn off any sirens because the
alarm zones states also will change to "Normal" when the arming mode changes.

~~~~
return {
  active = true,
  on = {
    devices = {
      'DISARM ALL ZONES' -- You'd need a switch named like this for this example to work
    }
  },
  execute = function(domoticz, device)
    if device.state == 'On' then
      local alarm = require "ideAlarmModule"
      -- domoticz.helpers.speak(domoticz, 'Disarming all zones', 'canSpeakAtNight')
      for i=1, alarm.qtyAlarmZones() do
        alarm.zones(i).disArmZone(domoticz)
      end
    end
  end
}
~~~~

As you can see there is a line that's commented out with a dzvents helper function.
If you don't have such a function you might want to consider adding one so that you
can get spoken messages when you need them. (Explaining how to do that is beyond
the scope of this Wiki)

Synchronize Arming Mode for Multiple Alarm Zones
------------------------------------------------

If you have several alarm zones you can synchronize the arming mode between them.
In the example below we synchronize the arming mode for 'Another Zone' with the
arming mode for 'My Home':

~~~~
local alarm = require "ideAlarmModule"

return {
  active = true,
  on = {
    devices = {
      alarm.zones('My Home').armingModeDevIdx
    }
  },

  execute = function(domoticz, device)
    local newArmingMode = alarm.zones('My Home').armingMode(domoticz)
    if (alarm.zones('Another Zone').armingMode(domoticz) ~= newArmingMode) then
      alarm.zones('Another Zone').armZone(domoticz, newArmingMode)
    end
  end
}
~~~~

In the script above we load the ideAlarmModule module so that we can trigger on
the Arming Mode text device that we have defined in the configuration file. Doing
it this way, we don't have to "hard code" the device's idx or name in the script.
The script first checks if the arming mode for the two alarm zones differ. (This
is not a necessary check though, but we keep it here in the example)

If the Arming Modes differ, the zone.armZone function is used to set the arming
Mode for 'Another Zone'.