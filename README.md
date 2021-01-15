# La Marzocco Home Assistant Integration

![Validation And Formatting](https://github.com/rccoleman/lamarzocco/workflows/Validation%20And%20Formatting/badge.svg)
![Validation And Formatting](https://github.com/rccoleman/lamarzocco/workflows/Validation%20And%20Formatting/badge.svg?branch=dev)
[![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg)](https://github.com/custom-components/hacs)

## Overview

This is a prototype integration for recent La Marzocco espresso machines that use Wifi to connect to the cloud and can be controlled via the La Marzocco mobile app.  This capability was rolled out in late 2019, and La Marzocco supposedly offers a retrofit kit to add it to earlier models.

Based on the investigation from Plonx on the Home Assistant forum [here](https://community.home-assistant.io/t/la-marzocco-gs-3-linea-mini-support/203581), I built an integration that makes configuration/machine status available to Home Assistant and allows the user to turn the machine to "on" or "standby".

Unfortunately, two very long and hard-to-access pieces of information (client_id and client_secret) are required to retrieve the initial token and encryption key for the local API.  I wrote a Python script to use with `mitmproxy` to get this information and you can find instructions [here](https://github.com/rccoleman/lmdirect/blob/master/Credentials.md).

## Installation

### HACS

If you've installed [HACS](https://hacs.xyz), this integration is in the default list and you can simply search for "La Marzocco" and install it that way.

1. Launch the HACS panel from the left sidebar
2. Click "Integrations`
3. Search for "La Marzocco" and select it
4. Click "Install" on card that appears

### Manual

If you don't have HACS installed or would prefer to install manually.

1. Create a `config/custom_comoponents` directory if it doesn't already exist
2. Clone this repo and move `lamarzocco` into `config/custom_components`.  Your directory tree should look like `config/custom_components/lamarzocco/...files...`

#### Restart Home Assistant

## Configuration

### Discovery

Home Assistant should automatically discover your machine on your local network via Zeroconf.  You'll get a notification in Lovelace that it has discovered a device, and you should see a "Discovered" box in Configuration->Integrations like this:

![](https://github.com/rccoleman/lamarzocco/blob/master/images/Discovered_Integration.png)

Clicking "Configure" brings you to this:

![](https://github.com/rccoleman/lamarzocco/blob/master/images/Config_Flow_Discovered.png)

Fill in the `client_id`, `client_secret`, `username`, and `password` as requested and hit "submit.  The integration will attempt to connect to the cloud server and your local machine to ensure that everything is correct and let you correct it if not.

### Manual

You can also add the integration manually.

1. Navigate to Configuration->Integrations
2. Hit the "+ Add New Integration" button in the lower-right
3. Search for "La Marzocco" and select it
4. You'll be presented with a dialog box like this:

![](https://github.com/rccoleman/lamarzocco/blob/master/images/Config_Flow_Manual.png)

5. Fill in the info
6. Hit "Submit"

#### Configured Integration

Regardless of how you configured the integration, you should see this in Configuration->Integrations:

![](https://github.com/rccoleman/lamarzocco/blob/master/images/Configured_Integration.png)

## Usage

In Dev->States, you should see 5 new entities:
* 3 sensors named `sensor.<machine_name>_coffee_temp`, `sensor.<machine_name>_steam_temp`, and `sensor.<machine_name>_total_drinks`
* 3 switches named `switch.<machine_name>_main`, `switch.<machine_name>_auto_on_off`, `switch.<machine_name>_prebrew`

The integration also exposes several services:
* `set_temp` - Set the temperature of the coffee or steam boilers based on the entity_id
* `auto_on_off_enable` - Enable/disable auto on/off for specific day
* `set_auto_on_off_hours` - Set the hours of the day for auto on/off hours for a specific day
* `set_dose` - Set the coffee dose in pulses (~0.5ml) for a specific key for the GS/3 AV.  Not applicable for the GS/3 MP or Linea Mini
* `set_dose_hot_water` - Set the hot water dose in seconds for the GS/3 AV and MP.  Not applicable for the Linea Mini.
* `set_prebrew_times` - Set the prebrew on/off times in seconds for the GS/3 AV and Linea Mini.  Not applicable for the GS/3 MP.

> **_NOTE:_** The machine won't allow more than one device to connect at once, so you may need to wait to allow the mobile app to connect while the integration is running.  The integration only maintains the connection while it's sending or receiving information and polls every 30s, so you should still be able to use the mobile app.

If you have any questions or find any issues, either file them here or post to the thread on the Home Assistant forum [here](https://community.home-assistant.io/t/la-marzocco-gs-3-linea-mini-support/203581).
