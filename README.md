# Maestro PCB v1.1
The Maestro is a ESP32-based automation board with built-in ethernet, IO, and peripherals built to ensure your project is automated quickly and easily. With x4 inputs, x4 outputs, x1 Relay, you have everything you need in one package. Intended for use with the ESPHome platform. 
### Basic Information
* ESP32-WROOM-32E with BLE/WiFi
* RTL8201F Ethernet
* USB C Debugging and Programming
* Barrel Jack 5.5mm Power input 9-24v
* 8 Pin JST expansion port with 5V 1A protected power output and 6 ESP32 pins.
* XL9535 Port expander on board to control on board outputs without using limited ESP32 pins
* 8 XL9535 3.3v IOs available on internal 8 pin JST XH header.
* Button on IO0
* x4 Opto-isolated inputs supporting 5-24v sinking or sourcing
* x4 N-channel sinking 5-24v 2A protected outputs, one of which supports PWM.
* x1 Relay Dry Contact output
### Quick Start Code Examples
Examples for ESPHome. Tested with version 2024.10
#### Basic Hardware Example
```
IA-Maestro-Hardware.yaml
```
#### NFC Puzzle Example
```
IA-NFC8-Ethernet.yaml
```
Information about the puzzle hardware can be found in the NFC8 section. 

### Provided Library Yamls
In the Libraries folder there are Yaml files to quickly set up most of the hardware and provide example. These can be included in your Yaml via packages syntax. 

Example: 
```
packages:
  pcb_base: !include libraries/IA-PCBv1.1.yaml #this includes the hardware available on the PCB without ethernet, use this if you plan to use wifi.
  pcb_base: !include libraries/IA-PCBv1.1-ethernet.yaml #this includes ethernett hardware, and will automatically include package listed above. Do not include both.
  nfc_base: !include libraries/IA-NFC8.yaml #this includes most of the logic used for the NFC puzzle.
  analog_base: !include libraries/IA-PCBv1.1-Analog-Inputs.yaml #this makes inputs measure voltage instead of binary inputs. This is not accurate.
  player_base: !include libraries/IA-DFplayer.yaml #sets up the df player 
```

# Detailed Hardware Specifications
## USB
USB C with USB Serial converter to allow easy programming of the ESP32. ESP32 serial pins are also available on an internal header to allow programming with an external programmer. USB power is limited to 5W (5V 1A) so it is recommended to disconnect external accessories for programming if they may exceed this power limit. 
## Power
On board DC-DC converters mean the Maestro can be powered from 9-24v DC via the Barrel jack. Typical power consumption without accessories is 2-3W. Maximum power consumption is less than 10w. There is a PCB mount 1.25A fuse as well as a polarity diode on the positive power input. Protected 5V at 1A max is available on the 8pin expansion JST connector.

The Board can also be powered via the USB C port, however this is limited to 5W total (5V/1A). USB power input has an Ideal diode which allows both the DC Input and USB to be connected simultaneously.

**The N Channel outputs, DC input, and USB port share a common ground. For this reason it is recommended to use a seperate isolated power supply to power the Maestro from the one used to power the outputs to avoid common mode ground issues.** 
## Onboard port expander
Due to the limited pins on the ESP32 there is an onboard I2C XL9535 port expander for controlling outputs without using ESP32 pins. 
## Expansion Port
The 8 Pin JST Expansion Port is intended for attaching I2C, SPI, etc sensors and accessories to the Maestro. The 5V power is on by default, but can be turned off via the onboard XL9535 port expander. The 5V 1A power is short circuit protected and its status can be monitored via the onboard port expander. There are 6 ESP32 GPIO pins available, which are ESD protected. ESP32 pins are for 3.3v logic, there is no level shifting. 
## Optoisolated Inputs
There are 4 inputs available which are optoisolated, they can accept 5-24v and all 4 inputs share 1 common ground or positive. 
## N-Channel Outputs
There are 4 N channel 2A max outputs.. Output 4 is controlled directly by the ESP32 to allow the use of PWM, the other 3 outputs are controlled via the port expander and can only be turned on and off. The outputs are provided by ZXMS6005DG protected N Channel mosfets and have external fuses for extra protection. 
**The N Channel outputs, DC input, and USB port share a common ground. For this reason it is recommended to use a seperate isolated power supply to power the Maestro to avoid common mode ground issues.** 
## Relay Output
There is a 3A SPDT Dry Contact relay output available, **this must be fused externally if not used for contact closure signalling.** It is intended for switching low voltage loads or contact colusure to control other systems. 

# Pinouts and configuration
## Inputs
Inputs read high when not active and low when active.
| Input | Pin | Code ID | 
| ------------- | ------------- | ------------- | 
| Input 1 | GPIO34  | input1 | 
| Input 2 | GPIO35  | input2 | 
| Input 3 | GPIO36  | input3 | 
| Input 4 | GPIO39  | input4 | 
## Outputs
| Output | Pin | Code ID | 
| ------------- | ------------- | ------------- | 
| Relay Output | XL9535 IO0 | output_relay | 
| Output 1 | XL9535 IO1 | output1 | 
| Output 2 | XL9535 IO2 | output2 | 
| Output 3 | XL9535 IO3 | output3 | 
| Output 4 | GPIO2 | output4_pwm | 
## Internal XL9535 Port Expander
Pins available on the internal connector are free to use but do not have the additional ESD protections as the pins on the main Expansion Connector. All GPIO is 3.3v. 
```
i2c:
  - id: i2c_internal
    sda: GPIO5
    scl: GPIO16

xl9535:
  - id: xl9535_internal
    i2c_id: i2c_internal
    address: 0x20
```
| XL9535 | Pin | Code ID | 
| ------------- | ------------- | ------------- | 
| IO0 | Relay Output | output_relay | 
| IO1 | Output 1 |  output1 | 
| IO2 | Output 2 | output2 | 
| IO3 | Output 3 | output3 | 
| IO4 | Output LED1 | output_led1 | 
| IO5 | Output LED2 | output_led2 | 
| IO6 | Output 5V Expansion | output_5v_disable | 
| IO7 | Input 5V Expansion |  | 
| I10 | Internal Connector |  | 
| I11 | Internal Connector |  | 
| I12 | Internal Connector |  | 
| I13 | Internal Connector |  | 
| I14 | Internal Connector |  | 
| I15 | Internal Connector |  | 
| I16 | Internal Connector |  | 
| I17 | Internal Connector |  | 


## Ethernet
```
Ethernet:
  type: RTL8201
  mdc_pin: GPIO23
  mdio_pin: GPIO18
  clk_mode: GPIO17_OUT
  phy_addr: 0
```
## Expansion Port
| Connector | Pin | Additional |
| ------------- | ------------- | ------------- | 
| Pin 1 | Ground|  | 
| Pin 2 | 5V| Protected 1A | 
| Pin 3 | GPIO32| SPI SS | 
| Pin 4 | GPIO15| SPI MOSI | 
| Pin 5 | GPIO13| SPI MISO | 
| Pin 6 | GPIO33| SPI SCK | 
| Pin 7 | GPIO14| I2C SDA | 
| Pin 8 | GPIO4|  I2C SCL | 

# Accessories

## NFC8 Expansion board for NFC Puzzle
The NFC Puzzle allows up to 8 PN532 NFC readers to be connected via SPI to the Maestros Expansion port. This is accomplished by having a PCF8574 I2C Port Expander enabling the Maestro to control each of the 8 PN532 CS pins. There is a onboard 3.3v Regulator for the port expander. 


Note that the pin order is reversed compared to the port on the Maestro. This allows the use of standard JST cables. 
| Connector | Pin | Additional |
| ------------- | ------------- | ------------- | 
| Pin 8 | Ground|  | 
| Pin 7 | 5V| Protected 1A | 
| Pin 6 | GPIO32| Unused | 
| Pin 5 | GPIO15| SPI MOSI | 
| Pin 4 | GPIO13| SPI MISO | 
| Pin 3 | GPIO33| SPI SCK | 
| Pin 2 | GPIO14| I2C SDA | 
| Pin 1 | GPIO4|  I2C SCL | 
```
pcf8574:
  - id: 'pcf8574_hub'
    i2c_id: i2c_bus
    address: 0x3E
    pcf8575: false
spi:
  clk_pin: GPIO33
  mosi_pin: GPIO15
  miso_pin: GPIO13
i2c:
  - id: i2c_bus
    sda: GPIO14
    scl: GPIO4
    scan: true
    frequency: 100kHz
```

## DF Player Expansion Board
The DF Player Board allows you to connect a DF Player to the Expansion port to include audio effects. As the DF Player uses only 1 pin to communicate with the Maestro, the remaining pins can be daisy chained to another device such as the NFC8 Puzzle. 


Input
| Connector | Pin | Additional |
| ------------- | ------------- | ------------- | 
| Pin 8 | Ground|  | 
| Pin 7 | 5V| Protected 1A | 
| Pin 6 | GPIO32| Df Player RX | 
| Pin 5 | GPIO15| Unused | 
| Pin 4 | GPIO13| Unused | 
| Pin 3 | GPIO33| Unused | 
| Pin 2 | GPIO14| Unused | 
| Pin 1 | GPIO4|  Unused | 

Output 
| Connector | Pin | Additional |
| ------------- | ------------- | ------------- | 
| Pin 1 | Ground|  | 
| Pin 2 | 5V| Protected 1A | 
| Pin 3 | N/C | Not Connected| 
| Pin 4 | GPIO15| Unused | 
| Pin 5 | GPIO13| Unused | 
| Pin 6 | GPIO33| Unused | 
| Pin 7 | GPIO14| Unused | 
| Pin 8 | GPIO4 | Unused | 

```
uart:
  tx_pin: 32
  baud_rate: 9600
dfplayer:
```
