
If upgrading from previous versions Below are important instructions if you are upgrading ideAlarm from a previous version. If you make a new installation you can ignore what follows.

PLEASE MAKE SURE THAT YOU GO THROUGH ALL STEPS BELOW WHERE IT SAYS "BREAKING CHANGE", DON'T SKIP ANY VERSION

Version 2.4.0

    Various changes made to support dzVents 2.4.0. ideAlarm 2.4.0 now requires Domoticz v3.8837+

Version 2.1.1

    Added support for naming the Domoticz Security Panel differently than "Security Panel". If you wish to use this capability, have a look at the example configuration file (Look for SECURITY_PANEL_NAME) and insert that line into your own configuration file. If you intend to use the default name "Security Panel", you don't need to do anything.

Version 2.1.0

    BREAKING CHANGE: dzVents version 2.3.0 or higher is now required.

    BREAKING CHANGE: Action is required if upgrading from a previous version. Move the 3 files ideAlarmConfig.lua, ideAlarmhelpers.lua, ideAlarmModule.lua from your dzVents modules folder, E.g. /path/to/domoticz/scripts/dzVents/modules/ to your dzVents script folder, E.g. /path/to/domoticz/scripts/dzVents/scripts/ After doing that, there shall be no ideAlarm related files left in the modules folder. If your modules folder is empty, it won't be needed any longer and you may delete it but be careful.

    BREAKING CHANGE: Action is required if upgrading from a previous version. If you have made any custom lua scripts that's using the ideAlarm API you won't need to alter the package.path any longer before doing require "ideAlarmModule"

    The 2 files ideAlarmConfig.lua, ideAlarmhelpers.lua have updated comment blocks (the leading few lines of comments in each file.) Have a look at the examples files at the first 7 lines what the comments now should look like. Make sure that the changes you make to your files only involves these comments.

Version 2.0.2

    Removed hard coding of local protocol, IP and port.

Version 2.0.1

    Fixed an issue where nagging occured to often.

Version 2.0.0

    BREAKING CHANGE Action is required if upgrading from a previous version. The configuration file has 2 new variables and another old variable has changed name. Therefore you should make a change in your file ideAlarmConfig.lua (located in the modules folder) Please add the variables _C.NAG_SCRIPT_TRIGGER_INTERVAL and _C.NAG_INTERVAL_MINUTES as you can see in the configuration file example. Then for every sensor that you have defined, there is a variable named nagTimeoutSecs. Rename all of those to nagTimeoutMins and change the value to 5.
    Two new custom optional helper functions can now be used. You don't have to define them, but if you wish to use them, the examples can be seen at the very end of The example custom event helper. The new functions are named alarmNagOpenSensors and alarmOpenSensorsAllZones. You can read about them in the Wiki.

Version 1.1.0

    Added the new zone state alarm.ZS_ARMING. A new ideAlarm custom helper function (alarmZoneArming) can now be used.

Version 1.0.3

    Logic improvements.
    BREAKING CHANGE Action is required if upgrading from a previous version. The custom event helper function alarmZoneArmingWithTrippedSensors has an additional argument and the logic in the example file has changed. Therefore you should make a change in your file ideAlarmHelpers.lua (located in the modules folder). Please have a look at the function alarmZoneArmingWithTrippedSensors in the The example helpers file what it should look like.

Version 1.0.2

    Minor fixes.

Version 1.0.1

    Minor fixes.

Version 1.0.0

    Initial release.

