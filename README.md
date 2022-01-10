# esphome-ILI9341-color-display
My implementation of an ILI9341 3.2" TFT LCD Colour Display with an ESP32 in ESPHome.
| Display | Case |
| ------- | ---- |
|![20211218_191244](https://user-images.githubusercontent.com/14822776/146652289-75712550-9f35-47fb-b28b-b9f255a79355.jpg)|![20220110_204306](https://user-images.githubusercontent.com/14822776/148830231-92cea7b7-5aaf-461c-955b-43cfda535ac8.jpg)|


This is an easy project to get started! My design skills arent great, but this lays the foundations for anything I'd want to implement in the future. I got started from the official [ILI9341 documentation on esphome](https://esphome.io/components/display/ili9341.html). 

I read several entities from Home Assistant:
- The current date and time
- The current weather state and outside temperature
- Internal temperature
- Forecast weather for the present day
- Forecast weather for the next day.

I have also implemented a simple logic where the current day's forecast is displayed before 4pm every day, and after 4pm it displays the next day's forecast instead.

I also get a dimmer entity in HA which I can use to dim the LCD backlight or turn it off.

# What you need

- [An ESP32](https://www.banggood.com/Geekcreit-ESP32-WiFi+bluetooth-Development-Board-Ultra-Low-Power-Consumption-Dual-Cores-Pins-Unsoldered-p-1214159.html?rmmds=myorder&cur_warehouse=CZ)
- [A 3.2" ILI9341 TFT LCD display](https://www.banggood.com/3_2-Inch-8Pin-240320-TFT-LCD-Screen-SPI-Serial-Display-Screen-Module-ILI9341-p-1384914.html?rmmds=myorder&cur_warehouse=CN)
- [Dupont connectors](https://www.banggood.com/40pcs-10cm-Female-To-Female-Jumper-Cable-Dupont-Wire-p-994059.html?cur_warehouse=CN&rmmds=search)
- Micro USB Power Supply

# Make the Right Connections

Careful! Check the voltage capabilities of your display - mine could only handle 3.3V.

| Display Pin | ESP Pin|
| ----------- | ------ |
| GND | GND|
| VCC | 3V3 |
| CLK | D14 |
| MOS | D13 |
| RES | D33 |
| DC | D25 |
| BLK | D32 |
| MIS | D12 |

My display does not have a cs pin. If yours does, do connect it do a GPIO pin and modify the code accordingly.

# How to get started

- Put the font files as well as the yaml file into your esphome folder (feel free to use your own choice of fonts - make sure you update the yaml in that case)
- Make the appropriate changes to your yaml file. Substitute values for:
  - Device name
  - Static IP details. If you dont want to set a static IP, comment out this line as well as the lines for manual_ip under the wifi section!
  - Entities for an internal temperature sensor and an external temperature sensor
  - An entity for the current weather condition - I use SMHI in Sweden, you could select your local weather service
  - Entities to show the forecast low and forecast high for today and tomorrow. See forecast section in [my other post](https://github.com/tejasitraj/esphome-tiny-display) for a description, and see code below which I use to create these entities in my configuration.yaml
- Compile and upload to your hardware!

# Forecast entities

Essentially, in order to create separate specific entities for today's and tomorrow's forecast, I use template sensors in my configuration.yaml. Here's my code.

```
template:
  - sensor:
      - name: Todays Forecast High
        state: >
          {{ states.weather.smhi_home.attributes.forecast.0.temperature }}
      - name: Todays Forecast Low
        state: >
          {{ states.weather.smhi_home.attributes.forecast.0.templow }}
      - name: Todays Forecast Condition
        state: >
          {{ states.weather.smhi_home.attributes.forecast.0.condition }}
      - name: Tomorrows Forecast High
        state: >
          {{ states.weather.smhi_home.attributes.forecast.1.temperature }}
      - name: Tomorrows Forecast Low
        state: >
          {{ states.weather.smhi_home.attributes.forecast.1.templow }}
      - name: Tomorrows Forecast Condition
        state: >
          {{ states.weather.smhi_home.attributes.forecast.1.condition }}
``` 
# Case
I worked with my friend Magnus Skärström to design a case for the display and the ESP32. There is also an opening in the case for a 6mm push button, which I plan to use soon. 
I personally don't see the need to make the case unnecessarily slim, since this restricts the ability to use dupont connector cables and pin headers and forces us to solder directly on the board instead. Which could be good - but it isn't beginner friendly. 
![20220110_204300](https://user-images.githubusercontent.com/14822776/148831429-6695eb61-2b24-4fb1-9644-d7cfc2979302.jpg)
![20220110_204306](https://user-images.githubusercontent.com/14822776/148831448-10025b7e-e2c3-44b1-a7cf-08be931f21fb.jpg)

# My next steps

- Adding a physical button to allow me to turn the display on and off
- Thinking about what more fun information I could add to the display - alarm status? heating status? Alerts? It's all possible!
