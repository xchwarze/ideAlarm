Installation
============

Prerequisites
-------------

  * Domoticz v3.8837 or later

Optional: Create a Dummy Hardware Device
----------------------------------------

You may optionally want to create a Domoticz dummy hardware device only for
the ideAlarm virtual Text Devices and virtual Switch Devices that you are going
to add later. It's totally up to you if you wish to do that or if you'd like to
use an existing.

Create Virtual Text Devices
---------------------------

For each alarm zone that you wish to define, create two virtual text devices. One
text device is used to display the alarm zone's "Arming Mode" and the other is
used to display the alam zone's "Status".

You might want to keep it very simple in the beginning and just name your first
alarm zone's text devices "Z1 Arming Mode" and "Z1 Status". If you plan to add
a second alarm zone, just name them "Z2 Arming Mode" and "Z2 Status", etc.
(Later you can rename those virtual text devices to whatever you wish).

Create Virtual Switch Devices
-----------------------------

Each alarm zone will need two virtual switch devices for toggling the arming modes.

The first switch will be used to toggle between "Armed Home" and "Disarmed". The second
switch will be used toggle between "Armed Away" and "Disarmed".

For your first alarm zone, you can name those switches "Toggle Z1 Arm Home" and "Toggle Z1 Arm Away".

**IMPORTANT**: Now edit the virtual switches you just created and set **Off Delay:** to 2 seconds.
You can also change the Switch Icon to "Alarm" if you wish.

Configure the Built-in Security Panel
-------------------------------------

If you haven't already defined a Security Panel device in your system (look among switches if
you are not sure), you can add one now by specifying a PIN code in the settings followed by arming
the security panel (Setup->More options->Security Panel). It should then be visible under devices,
where you can add the switch. Name it "Security Panel" for example.

Create the Configuration File
-----------------------------

Download the example config file and save the file in your dzVents script folder, e.g.
*/path/to/domoticz/scripts/dzVents/scripts/* using the name *ideAlarmConfig.lua*.

Example linux command on a Raspberry Pi logged in as user pi. (Do not use the sudo command):

~~~~
wget -nc -O ~/domoticz/scripts/dzVents/scripts/ideAlarmConfig.lua https://github.com/xchwarze/ideAlarm/tree/master/examples/ideAlarmConfig.lua
~~~~

Edit the Configuration File
---------------------------

Make your changes to the configuration file. See [Configuration](./Configuration.md).

Copy and paste your configuration file into the form on codepad
to verify that it has the correct LUA syntax. Select "Lua" and mark
your paste as "Private", so it will not be public. Save it.

Download the event helpers file
-------------------------------

Download the example helpers file and save the file in your dzVents script folder, e.g.
*/path/to/domoticz/scripts/dzVents/scripts/* using the name *ideAlarmHelpers.lua*.

Example linux command on a Raspberry Pi logged in as user pi. (Do not use the sudo command):

~~~~
wget -nc -O ~/domoticz/scripts/dzVents/scripts/ideAlarmHelpers.lua https://github.com/xchwarze/ideAlarm/tree/master/examples/ideAlarmHelpers.lua
~~~~

Download the ideAlarm module
----------------------------

Download The ideAlarm module file and save the file in your dzVents script folder, e.g.
*/path/to/domoticz/scripts/dzVents/scripts/* using the name *ideAlarmModule.lua*.

Example linux command on a Raspberry Pi logged in as user pi. (Do not use the sudo command):

~~~~
wget -O ~/domoticz/scripts/dzVents/scripts/ideAlarmModule.lua https://github.com/xchwarze/ideAlarm/tree/master/scripts/ideAlarmModule.lua
~~~~

Download ideAlarm.lua
---------------------

Download ideAlarm dzVents script and save the file in your dzVents script folder, e.g.
*/path/to/domoticz/scripts/dzVents/scripts/* using the name *ideAlarm.lua*.

Example linux command on a Raspberry Pi logged in as user pi. (Do not use the sudo command):

~~~~
wget -O ~/domoticz/scripts/dzVents/scripts/ideAlarm.lua https://github.com/xchwarze/ideAlarm/tree/master/scripts/ideAlarm.lua
~~~~

ideAlarm Configuration
----------------------

Proceed with the [Configuration](./Configuration.md).
