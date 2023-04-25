# Home Assistant - Amber Electric Usage Charts
### Usage charts in Home Assistant for Amber Electric pricing

This code is used in Home Assistant with the Amber Electric integration to estimate usage in kilowatt hours and dollars. Example charts using the [ApexCharts Card HACS frontend integration](https://github.com/RomRider/apexcharts-card) are included.

![image](https://user-images.githubusercontent.com/25993713/234173654-b3e60742-90cc-4252-ad6d-4c55a3100b57.png)

Please note that the values will not match exactly with Amber's reporting, as:
* The power values reported by your inverter are unlikely to exactly match what is recorded by your smart meter and sent to Amber
* Any Home Assistant downtime will not have logging of power or prices
* The way this calculates cost is probably slightly different than how it works in Amber's system

My inverter typically reports exported power about 3% lower than what is received by Amber. In the inverter_import_power and inverter_export_power template sensors there is a `correctionFactor` variable which helps to compensate for this - I have it set to 1.03.

Prerequisites:
* The [Amber Electric Home Assistant integration](https://www.home-assistant.io/integrations/amberelectric)
* A Home Assistant inverter integration which provides import and export power (or a power sensor from which import and export can be derived)
* The [ApexCharts Card HACS frontend integration](https://github.com/RomRider/apexcharts-card)
  * This integration is not required if you choose to build your own charts with a different integration.

This is how it works:

![image](https://user-images.githubusercontent.com/25993713/234170440-2f414771-dc8d-45ce-92bf-cfaa200222e4.png)

To get it running, the only changes you should need to make are in the template sensors.
* Do whatever is required to have inverter_import_power and inverter_export_power return the power in kW.
  * Start with a correctionFactor value of 1. Note the difference in percent between what this reports and what Amber reports, and adjust the value accordingly.
* Change the Amber sensor ids in amber_import_cost and amber_export_cost to match your Amber integration.

Notes: 
* The Riemann Sum sensors take a little while to start logging data, and won't start until non-zero data is coming from your template sensors. Give it some time.
* The Import Cost and Export Profit utility meters have a unit of $h (similar to kWh). If this is too annoying, simply create a template sensor that produces the value with a different unit.
* I use folders for my configuration, so the code is organised that way - you may have to restructure to work with your configuration.yaml.
  * I could not get the utility meters to work with a folder (i.e., `utility_meter: !include_dir_merge_list utility_meter`), hence the single file.
* If you wish to have a different duration (e.g. hourly or weekly), it's simple enough to add a different utility meter.
  * If you make changes to the utility meters, they won't take effect until you restart Home Assistant.
