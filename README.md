# Home Assistant integration for SenseME fans

[![Github version](https://img.shields.io/github/v/release/mikelawrence/senseme-hacs)](https://github.com/mikelawrence/senseme-hacs/releases/latest) [![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg)](https://hacs.xyz/)

The Haiku with SenseME fan is a WiFi connected fan and optional light from Big Ass Fans. This Home Assistant integration provides control of these fans and light. The occupancy sensor will also monitored.

Now [aiosenseme](https://pypi.org/project/aiosenseme/) is used as the underlying library. It is asynchronous and fits well with Home Assistant. There are several key new features like automatic fan discovery and push updates. It keeps a socket open to each fan added to Home Assistant for push updates and commands like turn fan on. The single socket approach seems to cause fewer issues with loss of connection or the fan going dumb for while.

* Requires Home Assistant 0.110.0 or greater
* This integration is currently NOT compatible with the [i6 fan](https://www.bigassfans.com/fans/i6/).

## Installation

### HACS

If you have HACS installed on Home Assistant then just search integrations for **SenseME** and install.

### Manual

Copy the custom_components folder of this repository to your config folder and restart Home Assistant.

## Configuration using Home Assistant UI

* Go to **Configuration -> Integrations**.
* Click on the **+** in the bottom right corner to add a new integration.
* Search and select **SenseME** integration from the list.
* Click the **Submit** button on the popup window. The SenseME component will automatically detect SenseME fans on the network and setup a fan, light (if installed) and an occupancy sensor.

## Using the SenseME integration

The SenseME integration supports speed, direction and whoosh (in HA it's called oscillate) for fans. If your fan has a light installed it will automatically be detected and added to Home Assistant.

If the Fan has an occupancy sensor it is also added to Home Assistant but I have not looked at how well it performs. As far as I can tell there are no settings/adjustments for this sensor so what you see is what you get.

The Haiku App from Big Ass Fans supports grouping fans into rooms. Changing any fan in a room changes all fans in that room including those in Home Assistant. Keep in mind the lights installed in fans follow the same room grouping.

## SenseME integration options

Whoosh as oscillate can now be enabled/disabled as a configuration option across all fans. This option can be set when first adding the integration to Home Assistant or changed at any time by going to **Configuration -> Integrations -> SenseME** and selecting the gear icon at the top right.

Direction is also enabled/disabled as a configuration option across all fans.

## Breaking Changes

From v2.0.0 on this integration is configured via the Home Assistant frontend only and will no longer allow configuration via YAML. You need to remove the ```senseme:``` section from your configuration file the eliminate the error that pops up each time you restart.

## Issues

* This integration is currently NOT compatible with the [i6 fan](https://www.bigassfans.com/fans/i6/).
* Unknown models will produce a warning 'Discovered unknown SenseME device model' in Home Assistant. If you get this warning post an issue on [GitHub](https://github.com/mikelawrence/senseme-hacs/issues) with the model detected and I'll add that model to stop the warning.
* Sometimes SenseME fans just don't respond to discovery packets. It happens enough for me to mention it here. You will have to keep trying to add the SenseME integration until at least one fan is detected. From there all fans will be eventually detected by the periodic discovery built-in to the integration. If your fans are grayed out when you restart Home Assistant it just means the initial discovery didn't detect them but again the periodic discovery will eventually detect them.
* SenseME fans will occasionally drop the connection to Home Assistant. The integration will detect this and automatically reconnect. If it was the fan that dropped the connection this integration will usually reconnect within a minute. I'm not sure why but if the fan is powered off it can take a long time to detect the lost connection and that time varies based on what platform Home Assistant Core is running. On my development platform (Windows Subsystem for Linux running Ubuntu) a powered off fan is detected in a couple of minutes. Home Assistant running on a Raspberry Pi takes upwards of 25 minutes to detect lost connections.

## Debugging

To aid in debugging you can add the following to your configuration.yaml file. Be sure not to duplicate the ```logger:``` section.

```yaml
logger:
  default: warning
  logs:
    custom_components.senseme: debug
    aiosenseme: debug
    aiosenseme.discovery: debug
```
