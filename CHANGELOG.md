# DeusExMachinaII Change Log

**IMPORTANT:** This plugin is no longer distributed/updated in the Vera App Marketplace, which as of this writing has been down for months and is apparently not well supported within Ezlo these days. Updates will be offered exclusively through the plugin's [Github repository](https://github.com/toggledbits/DeusExMachina). Please see the README file in the Github repository for installation instructions.

## Version 2.11 (22333, development)

* Make sure `Active` flag tracks state correctly for non-active states.
* Update docs for new install procedure (no Vera App Marketplace).
* Code cleanup.

## Version 2.10 (released)

* Support DeusActivity.log file, enabled by setting `MaxLogSize` to a non-zero value (units are kilobytes; logs larger than this size are "rotated" through a single LZO archive).
* Clear device state cache at the start of cycling, to ensure up-to-date state; no bug associated with this, it's just a noticed omission.
* Enhancement: implement PreactionScene (state variable) and DEMIIAction.lua hook. The latter (file), if it exists, is loaded and run; the code is expected to return a function that accepts two arguments: target and state. Before DEMII turns a target on or off, it will call this function. This is the result of a request by @rafale77 for a "hook" to allow him to mute his camera motion sensors before lights change.
* Handle any unknown device as binary light rather than erroring-out for that device; this is more flexible, and creates the opportunity for immediate manual configuration of unrecognized devices, without the need to wait for code changes.
* Clean up device list in configuration, and improve device type detection.
* Add details for controlled device to status report JSON, and provide link to report on configuration tab.

## Version 2.9 (released)

* Fix an error in internal state tracking cache update when brightness or maxtime specified on device.
* Fix initialization of new instances to avoid overwriting needed value and causing later error.
* Upgrade detection of AltUI so we don't falsely detect when bridged (on "real" device triggers AltUI feature registration).
* Use Reactor's HMT approach for super-responsive house mode tracking (Reactor does not need to be installed).

## Version 2.8 (released) ##

* Make on/off state of dimming devices more deterministic (follows DelayLight issue #14).
* Issue #28: display slider values on sliders; updates during slide (feature request).
* Ability to disable DEMII's automatic timing and issue Activate and Deactivate actions to control cycling. On deactivation, lights are turned off randomly on shorter intervals, in the same way DEMII does with automatic timing previously. Issue #26
* Support for per-house-mode final scenes. This is a bit hackish, because I don't think it's going to be a widely used feature, but if so, I'll improve it. It works by adding the name of the house mode to the final scene--if a scene by that name exists, it is run in preference to the specified final scene (e.g. if the final scene is called "DeusEnd", then when the house mode changes from Away to Home, DEMII will try to find a scene called "DeusEndAway" to run, and runs it if found, or runs "DeusEnd" otherwise. Issue #23

## Version 2.7 (released) ##

* Bug fix only, fixes issue #25 from a fix attempt in 2.6 to address certain Fibaro and Qubino dimmers. That change was removed and we're back in business. The fix caused DEMII to turn lights on at full brightness rather than the prescribed dimming level inconsistently but with high frequency.

## Version 2.6 (released) ##

* Bug fix release only; addresses issue with creating new scene pairs in configuration UI.

## Version 2.5 (released) ##

* Incorporates fixed starting time to override sunrise start. If start time and lights-out time are equal, runs continuously (useful for control by scenes or scripting). Allow maximum "on" time to be specified for lights (simulate hallway access or WC use).

## Version 2.4 (released) ##

* Controls more devices (including the much-requested Philips Hue, and should also handle X-10, ZigBee, etc., as long as they are known to MiOS and can be controlled either by Status or LoadLevel (issues/enhancements 5 and 12);
* Allows you to set dimming level for dimmers (issue/enhancement 10);
* Support for running scenes--DEMII allows you to specify scene pairs (one scene acts as the "on" scene, the other the "off" scene);
* Support for a "final" scene--a scene that is run when DEMII goes idle after all other devices have been shut off;
* Support to limit the number of targets DEMII will allow turned "on" simultaneously (issue 15);
* DEMII itself is now enabled/disabled using the standard SwitchPower1 service SetTarget action (the old SetEnabled action in the plug-in service still works and will remain until version 3.0 is released).
* Fixes many issues around house modes, in particular, handling house mode changes while Deus is in its "active" time period (issue 16);
* More graceful handling of devices or scenes deleted from the system (but still appearing in DEMII's configuration)--they are now removed from config and the party continues;
* Fixes a possible loss of persistent state information if Luup crashes or the controller reboots during operation, that would sometimes leave DEMII disabled when it was enabled prior to the crash.

## Version 2.3 (released) ##

* adds support for house modes; fixes an issue with incorrect operation if DEMII is installed but Lights Out time is never changed
* fixes numerous other issues (see tagged issues).
