# ESPHome-based Air Conditioner #

Replace your window air conditioner manual controls with a relay board, connected to an ESP8266-based microcontroller running [ESPHome](https://esphome.io/).

## Prerequisites ##
This project assumes that you have Home Assistant running locally, with the ESPHome project installed and running. The author recommends a Raspberry Pi runing [HassIO](https://www.home-assistant.io/hassio/) with the [ESPHome add-on](https://esphome.io/) installed.

### Hardware ###
This firmware has been developed and tested with the following hardware:
* Wemos D1 Mini R2 microcontroller (other ESP8266 boards may also work)
* SSD1306 I<sup>2</sup>C OLED display, 128x64
* HTU21D I<sup>2</sup>C temperature and humidity sensor
* BH1750 I<sup>2</sup>C light sensor (optional)
* Generic 2 or 3 relay module, __active low!__ (meaning it will activate the relay when the GPIO is at 0V)
  * For units with dual fan speed, you will need 3 relays to control compressor, fan state, and fan speed
  * For units with only compressor and fan, a dual relay module will suffice.
* 8-LED WS2812B ring module
* Rotary Encoder

### Installation ###
For this document purpose, we will assume the device ID is set to `airconditioner1` in your setup.

#### Home Assistant ####
This firmware is designed to run independently of Home Assistant, but with strong integration using the native ESPHome API. There are two basic control input elements expected by the firmware, for setting the desired temperature and the operation mode.

You will need to add the following configuration.
```yaml
input_number:
  airconditioner1_temp:
    name: Air Conditioner Temp
    min: 19                       # Modify these values as appropriate for your desired operation.
    max: 27
    step: 0.5

input_select:
  airconditioner1_mode:
    name: Air Conditioner Mode
    options:                      # These are the default operation modes available
      - "off"
      - "auto"
      - "cool"
      - "fan_only"
    initial: "off"
    icon: mdi:snowflake
```

#### ESP8266 ####
Download or clone the repository, and copy the files into your `/config/esphome` directory on your [Home Assistant](https://www.home-assistant.io/) instance. You can optionally exclude some of the files in this repository that are not required for operation (e.g. `README.md`).

You will need to make the following connections:

|Wemos D1 Mini|SSD1306|HTU21D|BH1750|WS2812B|Relay1 (Fan Enable)|Relay2 (Compressor)|Relay3 (Fan Speed)|Relay VCC|Relay GND|
|:---:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| 3V3 | VCC | VCC | VCC |||||||
| 5V ||||VCC||||VCC||
| D1 | SDA | SDA | SDA |||||
| D2 | SCL | SCL | SCL |||||
| D3 ||||D IN||||
| D5 |||||IN|||
| D6 ||||||IN||
| D7 |||||||IN|
| GND | GND | GND | GND | GND |||||GND|



You will also need to create a `secrets.yaml` file inside your `/config/esphome` directory, with the following definitions:
```yaml
ssid: {{your_network_ssid}}
wifi_password: {{wifi_password}}
```

Once you have the configurations set up for both Home Assistant and ESPHome, you can use the ESP Home interface to upload to the device.
