# DeusExMachinaII: The Vacation Plugin

## Introduction ##

DeusExMachina is a plugin for the MiOS home automation operating system used on MiCasaVerde Vera gateway/controllers.
It takes over your house while you're away on vacation by creating a ghost that moves from room to room, turning on and off lights.
Simply specify the lights you want to have controlled by the plugin, specify a "Lights Out" time when lights will begin to
turn off, and come sundown DeusExMachina will take over.

**IMPORTANT:** This plugin is no longer distributed/updated in the Vera App Marketplace, which as of this writing has been down for months and is apparently not fully functional and well-supported by Ezlo these days. Updates will be offered exclusively through the plugin's [Github repository](https://github.com/toggledbits/DeusExMachina).

## History ##

DeusExMachina was originally written and published in 2012 by Andy Lintner (beowulfe), and maintained by Andy through the 1.x versions. In May 2016, Andy turned the project over to Patrick Rigney (toggledbits here on Github, rigpapa in the MCV/MiOS world) for ongoing support (version 2.0 onward). At this point, the plugin became known as Deus Ex Machina II, or just DEMII.

## How It Works ##

When Deus Ex Machina II (DEMII) is enabled, it first waits until two conditions are satisfied: the current time is at or after sunset, and the "house mode" is one of the selected modes in which DEMII is configured by the user to be active. If both conditions are met, DEMII enters a cycle of turning a set of user-selected lights on and off at random intervals. This continues until the user-configured "lights out" time, at which point DEMII begins its shutdown cycle, in which any of the user-selected lights that are on are turned off at random intervals until all lights are off. DEMII then waits until the next day's sunset.

For more information, see Additional Documentation below.

## Reporting Bugs/Enhancement Requests ##

Bug reports and enhancement requests are welcome! Please use the "Issues" link for the repository to open a new bug report or make an enhancement request.

## License ##

DeusExMachina is offered under GPL (the GNU Public License).

## Additional Documentation ##

### Installation ###

**IMPORTANT:** This plugin is no longer distributed/updated in the Vera App Marketplace, which as of this writing has been down for months and is apparently not well supported within Ezlo these days. Updates will be offered exclusively through the plugin's [Github repository](https://github.com/toggledbits/DeusExMachina).

1. Download the latest release package in ZIP format: [release packages](https://github.com/toggledbits/DeusExMachina/releases)
2. Unzip the downloaded archive to a location on your local system (remember where!).
3. Open the Vera UI in your browser.
4. Go to *Apps > Develop apps > Luup files*
5. Go the folder container the unzipped files; select all of the files as a group, and drag them as a group to the *Upload* button in the Vera UI.
6. Wait for the upload to complete and your Vera to reload Luup.
7. [Hard refresh](https://www.howtogeek.com/672607/how-to-hard-refresh-your-web-browser-to-bypass-your-cache/) your browser.

**If and only if** you are installing this plugin for the first time, perform the following additional steps to create the DeusExMachina II master device:

1. Go to *Apps > Develop apps > Create device* in the Vera UI.
2. For *Description*, copy-paste: `DeusExMachina II`
2. For *Upnp Device Filename*, copy-paste: `D_DeusExMachinaII1.xml`
4. For *Upnp Implementation Filename*, copy-paste: `I_DeusExMachinaII1.xml`
5. Select a room if you wish.
6. Press *Create device*.
7. Go to *Apps > Develop apps > Test Luup code (Lua)*
8. Enter and run `luup.reload()`
9. Wait until Luup reloads and then hard-refresh your browser again (see last step above).

### Simple Configuration ###

Deus Ex Machina's "Configure" tab gives you a set of simple controls to control the behavior of your vacation haunt.

#### Active Period ####

By default, you may specify a start time and an end time, during which DEMII will run (cycle lights). If the
start time is blank, the current day's sunset time will be used.

If the `Manual Activation` checkbox is checked, then no automatic schedule for DEMII is run, and it must be started
and stopped by using the Activate and Deactivate actions (e.g. from a scene, PLEG, or Lua).

#### House Modes ####

The next group of controls is the House Modes in which DEMII should be active when enabled. If no house mode is selected,
DEMII will operate in _any_ house mode.

#### Controlled Devices ####

Next is a set of checkboxes for each of the devices you'd like DEMII to control.
Selecting the devices to be controlled is a simple matter of clicking the check boxes. Because the operating cycle of
the plug-in is random, any controlled device may be turned on and off several times during the cycling period (between sunset and Lights Out time).
Dimming devices can be set to any level by setting the slider that appears to the right of the device name.
Non-dimming devices are simply turned on and off (no dimmer slider is shown for these devices).

> Note: all devices are listed that implement the SwitchPower1 and Dimming1 services. This leads to some oddities,
> like some motion sensors and thermostats being listed. It may not be entirely obvious (or standard) what a thermostat, for example,
> might do when you try to turn it off and on like a light, so be careful selecting these devices.

The "Max On Time" field can be used (optionally) to control the maximum time a light should be turned on. For example,
it may not appear natural for DEMII to leave a bathroom/WC or hallway light on for 30 minutes, which it could easily do
with its default behavior and schedule. Setting a maximum time on will cause DEMII to manage those lights to a shorter
schedule explicitly.

#### Scene Control ####

The next group of settings allows you to use scenes with DEMII.
Scenes must be specified in pairs, with
one being the "on" scene and the other being an "off" scene. This not only allows more patterned use of lights, but also gives the user
the ability to handle device-specific capabilities that would be difficult to implement in DEMII. For example, while DEMII can
turn Philips Hue lights on and off (to dimming levels, even), it cannot control their color because there's no UI for that in
DEMII. But a scene could be used to control that light or a group of lights, with their color, as an alternative to direct control by DEMII.

Both scenes and individual devices (from the device list above) can be used simultaneously.

#### Maximum "On" Targets ####

This value sets the limit on the number of targets (devices or scenes) that DEMII can have "on" simultaneously.
If 0, there is no limit. If you have DEMII controlling a large number of devices, it's probably not a bad idea to
set this value to some reasonable limit.

#### Final Scene ####

DEMII allows a "final scene" to run when DEMII is explicitly disabled, stops due to the house mode changing to an "inactive" mode (one in which DEMII is not supposed to run), or turns off the last light after the "lights out" time. This could be used for any purpose. I personally use it to make sure a whole-house off is run, but you could use it to ensure your alarm system is armed, or your garage door is closed, etc.

The scene can differentiate between DEMII being disabled and DEMII just going to sleep by checking the `Target` variable in service `urn:upnp-org:serviceId:SwitchPower1`. If the value is "0", then DEMII is being disabled. Otherwise, DEMII is going to sleep. The following code snippet, added as scene Lua, will allow the scene to only run when DEMII is being disabled:

```
local val = luup.variable_get("urn:upnp-org:serviceId:SwitchPower1", "Target", pluginDeviceId)
if val == "0" then
    -- Disabling, so return true (scene execution continues).
    return true
else
    -- Not disabling, just going to sleep. Returning false stops scene execution.
    return false
end
```

As of version 2.8, there is special handling of the final scene for when the house mode changes to an inactive mode. If a scene
with the name of the final scene plus the prior house mode can be found, it will be run in preference to the specified scene.
That is, if your final scene is called "DeusEnd", but there's a scene called "DeusEndAway", then it will be run when DEMII deactivates
due to the house mode changing to something other than "away." Otherwise, the "DeusEnd" scene will run.

### Control of DEMII by Scenes and Lua ###

As of version 2.8, there are two ways to control DEMII's operation with scenes, PLEG, Lua, etc.:

1. Set the active period to "Manual Activation" as described above, and use the `Activate` and `Deactivate` actions to start and stop cycling, respectively. When deactivating with this method, lights will be progressively turned off to simulate the bedtime "lights out" behavior DEMII uses with automatic scheduling. These actions are part of the `urn:toggledbits.com:serviceId:DeusExMachinaII1` service, and take no parameters.
1. Use the `urn:upnp-org:serviceId:SwitchPower1` service `SetTarget` action to turn DEMII on and off as you would any switch. This enables and disables DEMII. Using this method to start and stop cycling, however, is deprecated in favor of the above message.

Note that for cycling to occur, DEMII must either (a) be enabled with automatic timing on and a valid configured active period, or (b) be enabled, and with manual activation configured, in which case cycling will not begin until the `Activate` action is run, and not stop until the `Deactivate` action is run.

Here's an example of how to send the `Activate` action to DEMII:

`luup.call_action("urn:toggledbits-com:serviceId:DeusExMachinaII1", "Activate", {}, _deviceNumber_)`

### Triggers ###

DEMII signals changes to its enabled/disabled state and changes to its internal operating mode.
These can be used as triggers for scenes or notifications. DEMII's operating modes are:

* Standby - DEMII is disabled (this is equivalent to the "device is disabled" state event);

* Ready - DEMII is enabled and waiting for the next sunset (and house mode, if applicable);

* Cycling - DEMII is cycling lights, that is, it is enabled, in the period between sunset and the set "lights out" time, and correct house mode (if applicable);

* Shut-off - DEMII is enabled and shutting off lights, having reached the "lights out" time.

When disabled, DEMII is always in Standby mode. When enabled, DEMII enters the Ready mode, then transitions to Cycling mode at sunset, then Shut-off mode at the "lights out" time,
and then when all lights have been shut off, returns to the Ready mode waiting for the next day's sunset. The transition between Ready, Cycling, and Shut-off continues until DEMII
is disabled (at which point it goes to Standby).

It should be noted that DEMII can enter Cycling or Shut-off mode immediately, without passing through Ready, if it is enabled after sunset or after the "lights out" time,
respectively. DEMII will also transition into or out of Standby mode immediately and from any other mode when disabled or enabled, respectively.

As of v2.8, DEMII also supports a binary `Active` trigger, which is 0 whenever DEMII is not cycling lights for any reason (i.e. disabled, outside of configured active period, or not activated in manual activation mode), and 1 otherwise.

### Cycle Timing ###

DEMII's cycle timing is controlled by a set of state variables. By default, DEMII's random cycling of lights occurs at randomly selected intervals between 300 seconds (5 minutes) and 1800 seconds (30 minutes), as determined by the `MinCycleDelay` and `MaxCycleDelay` variables. You may change these values to customize the cycling time for your application.

When DEMII is in its "lights out" (shut-off) mode, it uses a different set of shorter (by default) cycle times, to more closely imitate actual human behavior. The random interval for lights-out is between 60 seconds and 300 seconds (5 minutes), as determined by `MinOffDelay` and `MaxOffDelay`. These intervals could be kept short, particularly if DEMII is controlling a large number of lights.

### Action Hook ###

As of 2.10, DEMII supports an "action hook"--a function that is called prior to a target being turned on or off. This is by request from a user who wishes to mute his camera motion sensing prior to lights changing, so that the change would not cause snapshots/recording.

There are two ways to implement the action hook:
1. Using a scene that is run before the target is changed: the state variable `PreactionScene` (in service `urn:toggledbits-com:serviceId:DeusExMachinaII1`) stores the scene number (or blank/zero for no scene/hook).
2. Using Lua code in a file called `/etc/cmh-ludl/DEMIIAction.lua` that returns a function. The function may accept two arguments: the target, and the state. The target is a numeric device number or string scene reference ("S"+scene number); the state is *true* or *false* depending on whether the target is being turned on or off, respectively (scenes are only turned on, so state will always be *true* for scenes).

```
-- Sample /etc/cmh-ludl/DEMIIAction.lua
return function( target, state )
    luup.log("DEMII action hook: DEMII is about to turn " .. tostring(target) .. ( state and " on" or " off" ))
end
```

### Troubleshooting ###

If you're not sure what DEMII is going, the easiest way to see is to go into the Settings interface for the plugin.
There is a text field to the right of the on/off switch in that interface that will tell you what DEMII is currently
doing when enabled (it's blank when DEMII is disabled).

If DEMII isn't behaving as expected, post a message in the MCV forums
[in this thread](http://forum.micasaverde.com/index.php/topic,54246.0.html)
or open up an issue in the
[GitHub repository](https://github.com/toggledbits/DeusExMachina/issues).

Please don't just say "DEMII isn't working for me." I can't tell you how long your piece of string is without seeing
_your_ piece of string. Give me details of what you are doing, how you are configured, and what behavior you observe.
Screen shots help. In many cases, log output may be needed.

#### Test Mode and Log Output ####

If I'm troubleshooting a problem with you, I may ask you to enable test mode, run DEMII a bit, and send me the log output. Here's how you do that:

1. Go into the settings for the DEMII device, and click the "Advanced" tab.
1. Click on the "Variables" tab.
1. Set the "TestMode" variable to 1 (just change the field and hit the TAB key). If the variable doesn't exist, you'll need to create it using the "New Service" tab, which requires you to enter the service ID _exactly_ as shown here (use copy/paste if possible): `urn:toggledbits-com:serviceId:DeusExMachinaII1`
1. If requested, set the TestSunset value to whatever I ask you (this allows the sunset time to be overriden so we don't have to wait for real sunset to see what DEMII is doing).
1. After operating for a while, I'll ask you to email me your log file (`/etc/cmh/LuaUPnP.log` on your Vera). This will require you
to log in to your Vera directly with ssh, or use the Vera's native "write log to USB drive" function, or use one of the many
log capture scripts that's available.
1. Don't forget to turn TestMode off (0) when finished.

Above all, I ask that you please be patient. You probably already know that it can be frustrating at times to figure out
what's going on in your Vera's head. It's no different for developers--it can be a challenging development environment
when the Vera is sitting in front of you, and moreso when dealing with someone else's Vera at a distance.

## FAQ ##

<dl>
    <dt>My lights aren't cycling at sunset. Why?</dt>
    <dd>The most common reasons that lights don't start cycling at midnight are:
        <ol>
            <li>The time and location on your Vera are not set correctly. Go into Settings > Location on your
                Vera and make sure everything is correct for the Vera's physical location. Remember that in
                the western hemisphere (North, Central & South America, principally) your longitude will
                be a negative number. If you are below the equator, latitude will be negative. If you're not
                sure what your latitude/longitude are, use a site like <a href="http://mygeoposition.com">MyGeoPosition.com</a>.
                If you make any changes to your time or location configuration, restart your Vera.</li>
            <li>You're not waiting long enough. DEMII doesn't instantly jump into action at sunset, it employs its
                configured cycle delays as well, so cycling will usually begin sometime after sunset, up to the
                configured maximum cycle delay (30 minutes by default).</li>
            <li>Your house mode isn't "active." If you've configured DEMII to operate only in certain house modes,
                make sure you're in one of those modes, otherwise DEMII will just sit, even though it's enabled.</li>
        </ol>
    </dd>

    <dt>I made configuration changes, but when I go back into configuration, they seem to be back to the old
        settings.</dt>
    <dd>Refresh your browser or flush your browser cache. On most browsers, you do this by using the F5 key, or
        Ctrl-F5, or Command + R or Option + R on Macs.</dd>

    <dt>What happens if DEMII is enabled afer sunset? Does it wait until the next day to start running?</dt>
    <dd>No. If DEMII is enabled during its active period (between sunset and the configured "lights out" time,
        it will begin cycling the configured devices and scenes. If you enable DEMII after "lights-out," it will
        wait until the next sunset.</dd>

    <dt>What's the difference between House Mode and Enabled/Disabled? Can I just use House Mode to enable and disable DEMII?</dt>
    <dd>The enabled/disabled state of DEMII is the "big red button" for its operation. If you configure DEMII to only run in certain
        house modes, then you can theoretically leave DEMII enabled all the time, as it will only operate (cycle lights) when a
        selected house mode is active. But, some people don't use House Modes for various reasons, so having a master switch
        for DEMII is necessary.</dd>

    <dt>I have a feature request. Will you implement it?</dt>
    <dd>Absolutely definitely maybe. I'm willing to listen to what you want to do. But, keep in mind, nobody's getting rich writing Vera
        plugins, and I do other things that put food on my table. And, what seems like a good idea to you may be just that: a good idea for
        the way <em>you</em> want to use it. The more generally applicable your request is, the higher the likelihood that I'll entertain it. What
        I don't want to do is over-complicate this plug-in so it begins to rival PLEG for size and weight (no disrespect intended there at
        all--I'm a huge PLEG fan and use it extensively, but, dang). DEMII really has a simple job: make lights go on and off to cast a serious
        shadow of doubt in the mind of some knucklehead who might be thinking your house is empty and ripe for his picking. In any case,
        the best way to give me feature requests is to open up an issue (if you have a list, one issue per feature, please) in the
        <a href="https://github.com/toggledbits/DeusExMachina/issues">GitHub repository</a>.
        Second best is sending me a message via the MCV forums (I'm user `rigpapa`).</dd>
</dl>
