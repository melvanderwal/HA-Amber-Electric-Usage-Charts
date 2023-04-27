# Home Assistant - Amber Electric Usage Charts
### Usage charts in Home Assistant for Amber Electric pricing

This is used in Home Assistant with the Amber Electric integration to estimate usage in kilowatt hours and dollars. Example charts using the [ApexCharts Card HACS frontend integration](https://github.com/RomRider/apexcharts-card) are included.


_Amber-Styled Charts_  
![image](https://user-images.githubusercontent.com/25993713/234527548-f8f815b3-44d5-4b35-ae4d-9a8f6c6230fd.png)

_Combined Daily kWh and $ Chart_  
![image](https://user-images.githubusercontent.com/25993713/234527306-a64b5b76-bb85-4292-b86b-bdffc9ee9bf2.png)

*Prerequisites:* 
* The [Amber Electric Home Assistant integration](https://www.home-assistant.io/integrations/amberelectric).
* A Home Assistant inverter integration which provides import and export power (or a power sensor from which import and export can be derived).
  * Mine is the [GoodWe integration](https://www.home-assistant.io/integrations/goodwe/).
* The [ApexCharts Card HACS frontend integration](https://github.com/RomRider/apexcharts-card).
  * This integration is not required if you choose to build your own charts with a different integration.

Please note that the values will not match exactly with Amber's reporting, as:
* The power values reported by your inverter are unlikely to exactly match what is recorded by your smart meter and sent to Amber
* Any Home Assistant downtime will not have logging of power or prices
* The clock on your HA computer may not be synced with Amber's clock, so the 30 minute period may be shifted slightly

For example, my inverter typically reports exported power about 3% lower than what is received by Amber. In the `inverter_import_power` and `inverter_export_power` template sensors there is a `correctionFactor` variable which helps to compensate for this - I have it set to 1.03.

_Sensor Flow_  
need a new diagram

To get it running, the only changes you should need to make are in the template sensors.
* Implement and test the template sensors first, prior to adding the configuration for the Reimann Sum and Utility Meter sensors.
  * Do whatever is required to have `inverter_import_power` and `inverter_export_power` return the power as positive units in kW.
    * Start with a `correctionFactor` value of 1.
  * Change the Amber sensor ids in `amber_import_cost` and `amber_export_profit` to match your Amber integration.
* Add the Reimann Sum sensors.
  * They won't exist until you restart Home Assistant.
  * The Riemann Sum sensors won't start logging data until non-zero data is coming from your template sensors. Give it some time.
* Add the Utility Meter sensors.
  * They won't exist until you restart Home Assistant.
* Add chart cards to a dashboard.
  * The provided charts are only intended as examples - build your own to suit your needs.
* Over a few days, note the difference between what your inverter has reported in kW to what is reported in Amber's app.
  * In the power template sensors, `correctionFactor` is a multiplier to adjust the kW of your inverter sensor to be closer to what is reported by your smart meter to Amber.  Adjust the value accordingly.

Notes:  
* The Import Cost and Export Profit Riemann Sum and Utility Meter sensors have a unit of $h (similar to how kW becomes kWh). If this is too annoying, simply create a template sensor that produces the output value with a different unit.
* If you look at the built-in HA state graph for the 30 minute utility meter, it will be smoothed and not represent the data very well.  Click the _Show more_ link to see it resetting to zero every 30 minutes.
* I use folders for my configuration, so the code is organised that way - you may have to restructure to work with how your configuration.yaml is set up.
  * I could not get the utility meters to work with a folder (i.e., `utility_meter: !include_dir_merge_list utility_meter`), hence the single included file.
  * My configuration.yaml setup for these files:
    ```
    template: !include_dir_merge_list template    
    sensor: !include_dir_merge_list sensor    
    utility_meter: !include utility-meters.yaml
    ```
* If you wish to have a different duration (e.g. hourly or weekly), it's simple enough to add a different utility meter.
