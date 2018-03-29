Then What?
==========

After Configuration
-------------------

You have successfully finished the configuration of ideAlarm and it's time to do some
testing and customize things so that they work the way you want. You can see
no errors originating from ideAlarm in the Domoticz log file, right?

Create a Simple Test Script
---------------------------

Let's make a simple dzVents script just to see check that everything works. You
can use Domoticz internal script editor or use an external script, it doesn't
matter. Name it whatever you like.

~~~~ lua
return {
  active = true,
  logging = {
    level = domoticz.LOG_INFO,
    marker = "TEST"
  },
  on = {
    devices = {
      'My Test Button',
    },
  },
  execute = function(domoticz, device)
    if device.state == 'On' then
      local alarm = require "ideAlarmModule"
      domoticz.log(alarm.statusAll(domoticz))
    end
  end
}
~~~~

Now create a Domoticz virtual Switch device and name it My Test Button. Then edit
the device and set the **Off Delay** at 2 seconds.

When you switch the *My Test Button* on, you should see a nice listing of all your alarm zones.

How do you arm and disarm your ideAlarm system? If you prefer to use the Domoticz
built in security panel to arm and disarm ideAlarm zones there is an option in the
configuration file to do that.

Otherwise some users will have their own ideas about what shall arm (Arm Home, and Arm Away)
and disarm their ideAlarm zones. You already have two virtual switches (unless you used
physical ones) that you can use to toggle the arming modes. While testing you might use
the Domoticz GUI to switch between different arming modes but you can of course do much
better than that. To do that, you will need to do some scripting. You'd like to have automation
(not just remote control) right? You can use any of the scripting environments that Domoticz
provides to do that. We'll give you a few script ideas below:

Create Custom ideAlarm Event Helpers
------------------------------------

Until now your newly created alarm system doesn't really do much. You have to set up the
ideAlarm Event Helpers That's where the fun part starts! You might want to have spoken messages,
SMS sent, other kind of notifications or lights turned on on certain ideAlarm events.
So go ahead and make your own ideAlarm Event Helpers.
