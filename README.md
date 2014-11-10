# Tidepool Uploader

These notes were updated on Sunday, November 9, 2014. 

This is a Chrome App that acts as an uploader client for Tidepool. It is intended to allow you to plug devices into the USB port and automatically load the data stored on it up to the Tidepool cloud. 

WARNING! THIS SOURCE CODE IS UNDER ACTIVE DEVELOPMENT AND IS KNOWN TO BE INCOMPLETE AND WITH ERRORS. IT IS ACTIVELY CHANGING. THIS CODE SHOULD NOT BE USED FOR COMMERCIAL MEDICAL SYSTEMS OR FOR ANY PURPOSE OTHER THAN ONGOING DEVELOPMENT AND IMPROVEMENT OF THIS CODE.

## Current to-do list:

This is roughly in priority order:
* Use the pwdSimulator for Asante and Omnipod
* Genericize UI -- use a template to support different devices, and bind all the elements (* The progress bar percentage should be stepped every N entries read from the device (especially with Asante) because it can take a long time to finish a section.
* Call displaySelectedDevices after save button clicked in settings so you don't have to wait for the scan routine.
such as uploadButtons) by their device name. 
* Dexcom detect needs to actually do a detect (and return serial number) -- right now it's just a dummy.
* Remove excess code that's no longer in use
* Clean up the code in index.js
* Save the serial port info for the port names found for a given device so we don't have to pattern match once we've found it (or include a helper in the settings page)
* Settings page is ugugugly
* Remove auto-scan
* Save device info (last download, serial number, etc) in local storage and yell if it doesn't agree the next time.

## About:

### A simple Chrome App
* Has an HTML page, and css files and a UI
* Has a manifest that asks for appropriate permissions on installation (perhaps someday the permissions request will be dynamic based on your devices)

### Can talk to the Tidepool Platform
* Displays a UI for you to enter your username and password
* Will log you in to your account on the Tidepool platform (and has a selector for local/dev/staging/prod -- this will go away someday or move to the settings). 
* Gets a token after login allowing uploads
* Can upload data to the platform

### Has code to manage various USB devices
* It can enumerate the USB devices it knows about. So far, they are:
  * Dexcom G4 CGM
  * Asante SNAP insulin pump
  * Insulet Omnipod insulin pump
  * There is working code for a OneTouch Mini blood glucose meter, but that code is currently disabled in manifest.json because it needs to share a cable with the Asante pump and that sharing isn't working well. We're probably going to move to a configuration-based model instead of device detection.

### Can manage serial communications
* Knows how to talk to serial devices using the Chrome serial API and has a useful amount of intelligence about how to communicate in packets and the like. It turns out that serial protocols from several manufacturers are relatively similar if you squint a bit.
* Can enumerate serial ports and make a reasonable guess as to which one belongs to which device based on the port name on a Mac. Probably needs further work on Windows, but at least the port patterns are found in settings so you can override them.
* Has some utilities for building and disassembling byte-oriented packets of data and calculating CRCs.

### Can handle selection and upload of a data file from a block mode device
* It can detect insertion of a block mode device, but you must press "Choose File" to bring up the file selection dialog (this is a security measure imposed by Chrome). 
* So far, the Insulet OmniPod is the only block mode device supported.

### Is starting to know how to talk to a Dexcom G4
* It has the ability to get firmware data, manufacturing data, and to query pages of EGV data
* It can interpret that data well enough to post it to Tidepool as CGM records
* Has a collection of tools for managing Dexcom communications

### Has code to talk to an Asante SNAP pump
* We hope that this now works reliably if there is no other serial device using the same cable. We still need to resolve that issue.

### Can read a data file from an Insulet Omnipod pump
* This is to a first-order approximation (it doesn't do everything yet, but it handles scheduled basals, boluses, settings, and smbg readings).

### Has a start at downloading a data file from CareLink and uploading it to Tidepool
* This is work in progress (based on server-side work we've already done). More soon.

## What it's missing

* Adequate code documentation and testing
* A better model to allow us to load drivers dynamically
* Full support for most drivers
* More drivers for more devices

## How to set it up

1. Clone this repository.
1. Run `npm install`
1. Run `./node_modules/.bin/webpack --progress -d`
1. Open Chrome. Go to chrome://extensions and turn on Developer mode (checkbox on the top line). 
1. Click "Load Unpacked Extension". 
1. Choose the directory you checked out above and click OK.
1. To run it, you can choose "Launch" from the chrome://extensions page. You can also run it from the Chrome App Launcher, which Chrome may install for you whether you want it or not.
1. If you're developing, you may find that the only way it runs properly is to hit "Reload" after each change to the source. You will definitely need to reload any time you change the manifest.

## How to use it

* Run the app
* Log in
* Plug your devices in

### Dexcom/Asante
* Press the "Upload" button; all the connected devices should upload in an arbitrary sequence.

### Insulet
* Plug in the device and start the app (in either order)
* Log in
* Press the "Choose File" button
* Navigate to the device (which on the pump I have shows up as "NO NAME")
* Select the most recent .ibf file on the device (there should be only one)
* Press "Open"


Note that Blip doesn't like it much if you don't have at least 24 hours worth of data, although it will work with only CGM data.
