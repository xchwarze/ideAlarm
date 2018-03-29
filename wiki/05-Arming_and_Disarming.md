Arming And Disarming
====================

There are several ways that you can arm and disarm an ideAlarm zone.

Using the Domoticz Built-in Security Panel
------------------------------------------

You can make ideAlarm sync the arming mode (Disarmed, Armed Home, and Armed Away)
with the Domoticz's built-in security panel. You set that in the configuration file.

By doing that, the ideAlarm zone will always reflect the Domoticz's built in security
panel's arming mode. Only one ideAlarm zone shall be set to sync with the Domoticz's
built-in security panel.

Using the Domoticz GUI
----------------------

You can of course use the virtual switch button that you have created for your
ideAlarm zones to switch the arming mode.

Programatically using Scripts
-----------------------------

You can make scripts in any of the Domoticz's available scripting environments.
Below are two sample dzVent scripts.

A simple dzVents script:

~~~~ lua
return {
  active = true,
  on = {
    devices = {
      'My Keyring',
    },
  },
  execute = function(domoticz, device)

    if device.name == 'My Keyring' and device.state == 'On' then
        domoticz.devices('Toggle Z1 Arm Home').switchOn()
    end

  end
}
~~~~

A bit more advanced dzVents script:

If you have a physical button device that you want to push 3 times to toggle the
arming mode. You may want to do like this to make things more obscure, for example
if you just use a lighting switch beside the entrance door for arming and disarming.
Security through obscurity. In the example below, that physical button device
is named 'Spider-Pig Button'.

~~~~ lua
return {
  active = true,
  on = {
    devices = {
      'Spider-Pig Button',
    },
  },
  data = {
    spiderPig =  {history = true, maxItems = 1, maxMinutes = 1} -- Used for counting if switch was pressed multiple times
  },
  execute = function(domoticz, device)

    if device.name == 'Spider-Pig Button' and device.state == 'On' then
      local spiderPigData = domoticz.data['spiderPig']
      if (spiderPigData.size == 0) then spiderPigData.add(0) end
      local toggleCount = spiderPigData.getLatest().data + 1
      if toggleCount >= 3 then
        domoticz.log('Spider-Pig Button has been pressed 3 times within one minute...', domoticz.LOG_INFO)
        domoticz.devices('Toggle Z1 Arm Away').switchOn()
        toggleCount = 0
      end
      spiderPigData.add(toggleCount)
    end

  end
}
~~~~