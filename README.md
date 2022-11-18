# VRCLovenseConnect
VRCLovenseConnect is a .NET implementation of the Lovense Connect API and Buttplug.io to synchronize **any connected toy** with an avatar through OSC messages.

# NEW! Prefabs for an easy DPS-compatible setup available [here](https://github.com/PingerSurprise/VrcLovenseConnect/releases/download/v0.4.5-beta/VRCLC_Prefabs.unitypackage)!
Just drop both VRCLC_Penetrator prefabs in your penetrator object, and both VRCLC_Orifice prefabs in your orifice object(s), adjust the radius to your penetrator's length and your avatar is ready! More details in the DPS section.

## What does it do exactly?
With the power of **OSC**, your avatar sends values between 0.0 and 1.0 to this program through UDP. This value is converted and transferred to either a Buttplug.io compatible toy through Bluetooth, or to a phone with the Lovense Connect app within the same network through HTTP, which will transfer commands to each connected toys.

It has been tested to be **accurate**, **lightweight** and **fast**. OSC is so fast indeed that I had to set a limit of processed messages to have less delay.

## Requirements
- A bluetooth dongle connected to your PC **OR** Lovense Connect for Android or iOS (the PC version requires a specific Lovense Bluetooth dongle).

# Toy Setup
There are currently two protocols available for VRCLovenseConnect: "Lovense" and "Buttplug".

When your toys are connected, simply launch the program after loading your avatar.

## Buttplug.io
Simply connect your toys to your PC through a cable or Bluetooth. Lovense toys should work with basic features in this protocol too.

You can test the connectivity of your toys with Intiface Desktop.

## Lovense Connect
Certain Lovense features such as the contraction for Max 2 are only controllable through Lovense Connect.

Open the Lovense Connect app on your phone or PC, then select the green shield icon to reveal a URL (ex: https://192-168-2-19.lovense.club:30010/GetToys).

In the config.json file next to the executable, copy this URL to the "address" field.

> *NOTE*: If you cannot connect with the provided address, try with the HTTP protocol, the 20010 port and a direct IP address instead (ex: http://192.168.2.19:20010/GetToys).

## config.json
Next to the executable should be a config file named "config.json". Open it with any text editor to change its values.

There are two parts, common settings and the "toys" settings. 

### Common settings
Common settings will affect all toys at once.

- **oscPort**: The port to listen OSC messages on. Default is 9001.
- **address**: Only for Lovense protocol. The address provided by the Lovense Connect app on your phone (not Lovense Remote).
- **scanTime**: Only for Buttplug protocol. The time to scan for a toy in seconds. Default is 5.
- **limit**: The limit of received messages to process. Raise the value if you experience some serious delay (refresh rate will be affected). Default is 10.

> NOTE: You may experience more response delay the more toys you have connected. In that case, raise the "limit" parameter to trade responsiveness with refresh rate.

### Toys settings
"Toys" settings will affect one toy at a time.

When a toy is detected, its name and protocol will be saved in an empty slot. You can then change its settings in the config.json file.

> NOTE: Changing the settings won't take effect until the program has been restarted. If a new toy is added, you will have to close the program, change the new toy's settings, then relaunch the program.

By default, 5 toys can be managed by VRCLovenseConnect. If you need more toys to be detected, just copy another toy's settings with the curly brackets and leave the name blank. Make sure to enclose each toy settings between curly brackets and separate them with a comma.

Parameters can share the same value. For example, you can set the same parameter name for all commands and all toys' features will activate at the same time with one Avatar Parameter. Everything is entirely customizable.

- **name**: The name of the toy. Automatically filled on detection.
- **protocol**: The protocol to use for toy controls (Lovense or Buttplug). No need to set it up if this toy has already been detected.
- **vibrateParameter**: The Avatar Parameter to synchronize with for vibration commands.
- **vibrateIntensity**: The intensity for vibrations (0.0 to 1.0, "Constant" Contacts only).
- **pumpParameter**: The Avatar Parameter to synchronize with for pumping/linear commands.
- **pumpIntensity**: The intensity for pumping (0.0 to 1.0, "Constant" Contacts only).
- **rotateParameter**: The Avatar Parameter to synchronize with for rotation commands.
- **rotateIntensity**: The intensity for rotations (0.0 to 1.0, "Constant" Contacts only).

# Avatar Setup
## Interaction Types
There are two modes of interaction for VRCLovenseConnect, named "Proximity" and "Constant".

- In "Proximity" mode, the intensity will be proportionate to the distance between the border of a Contact Sender and the center of a Contact Receiver, within its area of effect.
- In "Constant" mode, just having a Contact Sender touch a Contact Receiver will activate your toy, with an intensity set by the config.json file.

### "Proximity" Mode (Recommended)
Your avatar requires a spherical Contact Receiver with the Receiver Type set as "Proximity".

Set it up to interact with any Contact Sender you want, whether it's a standard (hands, head...) or a custom one (for examples, see "Recommended Contacts Setup" in the DPS section further below).

This Contact Receiver has to be "Local Only" and generate a parameter with the same name as in the config.json file ("LovenseHaptics" by default). Reminder that parameters are case-sensitive.

> *NOTE*: Setting the Contact as "Local Only" makes it so that the parameter will not be synced online and saves bandwidth.
> VRCLovenseConnect doesn't require online syncing to function, but if you want to repurpose this specific Contact for an animation, set "Local Only" off.
> Not all Contacts using the same parameter need to be "Local Only" either, only change the ones that will play an animation.

### "Constant" Mode
> **WARNING**: In "Constant" mode, disabling the Contact Receiver while it's being touched will *not* stop the toy because no OSC message will be sent to update the toy (even with a Parameter Driver). Make sure that no Contact Sender is touching your Contact Receiver before toggling it off.

The setup is not very different from "Proximity" mode. Just change these settings:

- The Contact Receiver must have the Receiver Type set as "Constant".
- Instead of a float, the value of the Contact will be a boolean. Take note of this if you want to repurpose this parameter in your animation controllers.

## Dynamic Penetration System
### Contacts Setup
If you use Dynamic Penetration System by Raliv, for penetrators, Contacts should be at the base, and the radius should have the same length as set in the material settings. Since DPS detection is also spherical, this setup will have 1:1 accuracy.

For orifices that you want to set up with VRCLovenseConnect, you can place Contacts on "Orifice" objects in your avatar, set the center as deep inside the orifices as you want, and adjust the radius to be right at the entrance. Remember, Proximity Contact Receivers have to be spherical (Constant Contact Receiver can have any shape).

### Recommended Contacts Setup
For a penetrator:
- one Contact Receiver with an "Orifice" custom tag, and "Allow Others" enabled only. You can optionally add standard tags as well.
- one Contact Sender with a "Penetrator" tag.
- (Optional) one Contact Receiver with an "OrificeSelf" custom tag, and "Allow Self" enabled only. You can optionally add standard tags as well. This prevents interfering with orifices on your own avatar that are too close to its own penetrator.
- (Optional) one Contact Sender with a "PenetratorSelf" tag. This prevents interfering with orifices on your own avatar that are too close to its own penetrator.

For each orifice on which you want toy interactions enabled:
- one Contact Receiver with a "Penetrator" custom tag, and "Allow Others" enabled only. You can optionally add standard tags as well.
- one Contact Sender with an "Orifice" tag (can have a different shape than the Receiver).
- (Optional) one Contact Receiver with a "PenetratorSelf" custom tag, and "Allow Self" enabled only. You can optionally add standard tags as well. This prevents interfering with a penetrator on your own avatar that is too close to its own orifices.
- (Optional) one Contact Sender with an "OrificeSelf" tag. This prevents interfering with a penetrator on your own avatar that is too close to its own orifices.

This setup will make sure that you and others can control your toys without any interference. Tags can be changed to be shared only to a few people for a more private use, kind of like a password.

# What's next?
More improvements on responsiveness.

# Resources
Dynamic Penetration System: https://raliv.gumroad.com/l/lwthuB

Intiface Desktop: https://intiface.com/desktop/

Buttplug.io C#: https://github.com/buttplugio/buttplug-rs-ffi/tree/master/csharp

LovenseConnectCSharp: https://github.com/MistressPlague/LovenseConnectCSharp
