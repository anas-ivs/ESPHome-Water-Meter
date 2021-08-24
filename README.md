# ESPHome-Water-Meter
Having embarked on SESCO (Electric) Meter node to measure my house electrical consumption, this project is similarly on mission to measure and automate another important utility consumed by the house - Water (supplied here by LAKU in Sarawak) using Home Assistant &amp; ESP Home.
![visitors](https://visitor-badge.glitch.me/badge?page_id=anas-ivs.esphome-watermeterpage.visitor-badge)

[Development](#development) |  [Hookup Diagram](#wiring) | [Bill of Materials](#BoM) | [Code](#code) | [Automations](#automations) | [References](#references) |

## <a name="development">Development</a>

### Phase 1 : Water Flow Rate measurement, May-June 2021

I started off with a cheap and easy to buy YF-S201 Hall Effect Water Meter flow sensor. Having not doing any research beforehand - the basic idea was since the meter was giving pulse outputs - applying similar `pulse_counter` feature as implemented on the Power Meter skids would give me my water flow rate readings per min/seconds and summing it up would give total water volume consumed.

Hence Phase 1a was deployed but came with challenges:

![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/actual/yf-s201.jpg)

1. 1/2" connection didn't fit well with poly pipe couplers with minor drip leak observed.  Reapplying thick layers of PTFE tape didn't help either so applied PVC glue, let it cure and the drip leaks eventually stopped.
2. Flow rate pulse is characterized as `Frequency (Hz) = 7.5 * Flow rate (L/min)` . Implemented this in ESPHome but was struggling to see acceptable values - mostly over and beyond the meter faceplate flow rate of `1-30 L/min`. Despite having learnt and worked on Field Instrumentations 10 years ago - later did I realized this water turbine meter is undersized hence the overrange readings! Durh. 

Hence Phase 1b was sanctioned to resize the meter:

![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/actual/yf-dn40.jpg)

1. Triggered happily the 'checkout-now' when a DN40 turbine meter recommendations showed up in Shop** . 
2. Only realized upon arrival DN40 - 1 1/2" inches is not a normal household size relative to current mains sized at DN25 - 1"! 
3. This time around - I was conscious to achieve better accuracy, metering [straight line requirements](https://koboldusa.com/blog/straight-piping-requirements-for-flow-measurement/) must at least be demonstrated. But having to already default on the requirements with DN40 to DN25 adapter immediately upstream and downstream of the meters - I went with the approach - try first lah, revisit later (only if! where to find DN40 polypipes and couplers in town?).
4. Meters installed horizontally at ground level.

Also included a 2 channel relay; reserved for future phase 4 to (re)deploy and upgrade water irrigation system replacing the now defunct (for more than 2 years) timer based water irrigation tap controller. 

### Phase 2 : Water Pressure measurement, August 2021

AliExpre** was reading my search and purchase history - smartly showed me some pressure transducers product recommendations. I research through finding if 4-20mA based transducers similar to ones used in my line of work would be better instead of  voltage based alternatives. 

4-20mA solutions mostly came in to also supply power at 12vdc or more, and a current measurement mechanism to read back output current from transmitters. Solutions are available but too costly and the plugin board may need more tinkering to work with ESPHome existing libraries. Hence settled for this [5V based 0.5-4.5Vdc output , 0-174 psi transmitter](https://www.aliexpress.com/item/32656389610.html?spm=a2g0s.9042311.0.0.b5d94c4dM5VH00). 

![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/actual/PressureTx-Household.jpg)*

Some other notes from this phase:

1. Pressure Tx came in 1/4" - Searched for adapters at local hardware shops but yet again not a common household size except for drinking water filters connections. Ended up shipping from Semenanjung from Shopp**.

2. NodeMCU ESP8266 comes with only 1 ADC channel and reading range of 0-3.3V; more than what the transmitter would output at upto 4.5V. Since I needed more Analogs channels I could swap for ESP32 with voltage divider circuit to bring it down to 3.3V; or use a analog to digital multiplexer - ADC1115. Went for the ADC1115 route which despite an additional board, IMO is simpler and yields better accuracy.

3. I had no other means to verify what my water mains or pump output pressure was or had been delivering. So I ordered a manual pressure gauge and did a bench test - to map out voltage reading to measured gauge pressure and coded in ESPhome the curve. 

   The transmitters performed as specs - produced a straight line curve; though only then I realized my water pressures were no near to 60-80% range of the transmitters at max 12 bar/174 psi vs pump output at 6 bar. Again - oklah, for measuring water pressure only - not as [IPF tripping initator](https://control.com/textbook/process-safety-and-instrumentation/safety-instrumented-functions-and-systems/) or reading production critical Compressor Discharge Pressures (CDP) / Trunkline Export pressures!
   
   ![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/actual/pressureTx-benchtest.jpg)

### Phase 3 : Filter Backwash automation

This is yet to commence but whats gets me motivated is to find the best mechanism to drive this sand filter 3 way multiport valve; requiring step turns at 0-90-180 angles. 

![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/actual/Inkedwater-filter-three-way-valve-draw.jpg)

Currently exploring a few ideas:

- 3d printing adapter to current valve handle - drive rotation by coupling to servo with the right torque value. 3d model for adapter can try to learn and scan using XboxOne camera. 
- Explore linear actuators? Current automatic window openers driven by chain can bend at angle - but can it still bend at 180? Want to explore
- Ingress protection would need to built in as well.

Ruled out:

- Off the shelf actuators - too costly (RM500++) and is timer based - defeats the purpose to have it 'IOT' connected.
- Valve couplers - Have 2 pcs ordered last year, not yet installed anywhere in the house but limited to 90degree turns and on/off mechanism with limit switch as end stoppers - No rotary encoders to do partial turns. 

### Phase 4 : Garden Irrigation automation

Basic concept is to drive irrigation by 12vdc valves - Not a priority for now but would want to see later if rework can be done to convert existing single zone irrgation (one master valve irrgates front and backyard) to multiple zones (at least three) - but this will require digging up and laying new pipes.

## <a name="wiring">Hookup Diagram </a>

### EasyEDA Connection diagram

**This would be my first drawing using EasyEDA - yet to master this software beyond drawing simple hookup diagrams. For illustration only.

![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/Electronic%20Schematics%20-%20Visual.png)

### Visual connection Diagram

**Drawn using handy old Powerpoint. Component images from the internet and are not mine.

![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/Wiring%20Schematics%20-%20Visual.png)



## <a name="BoM">Bill of Materials </a>

A combination of local sellers and international - mostly via Shop** and A**Express and also some local hardware stores too!. Links are for sharing and verify, proceed at own risk for seller latest validity/reputation.

Hardware

| #    | Item                                                 | Unit Price, RM                      | Quantity     | Total Price, excl Shipping | Remark and Link                                              |
| ---- | ---------------------------------------------------- | ----------------------------------- | ------------ | -------------------------- | ------------------------------------------------------------ |
| 1    | Turbine Water Meter                                  | 56.20                               | 1 pcs        | 56.20                      | [DN40 1.5 Turbine Meter (YF-DN40)](https://shopee.com.my/Dn40-1.5-Inch-Turbine-Flowmeter-Water-Flow-Sensor-Flow-Meter-Flow-Rate-Control-5~150L-Min-i.182354128.9231014650) Flow rate : 5~200L/min <br />Flow Pulse:  F(Hz)=(0.45xQ) +/-3%    <br />Q=L/min 1L water= 27Pulse<br /><br /><br /> |
| 2    | Fittings and Poly couplers for Turbine meter         | ~RM10                               | 2 each       | RM20                       | Take of your water main line size! My incoming mains poly pipe are at DN25 while Turbine Meter above is DN40!!<br /><br />Had to search hardware shops around down and landed one shop that was able to provide me with 1 1/2" to 3/4" fitting but in galvanized metal. Pair this with DN25 to 3/4" poly couplers. |
| 3    | Wiring + Weather Protector                           | ~RM0.80                             | +/- 10metres | RM10                       | For wiring - with Alarm panel Konnected project - I had plenty to spare so mostly re-use. These alarm panel comes in 4 core which is enough for transducers. <br /><br /><br />Since these cables are also not meant for sun/rain/heat - I covered put these cables in 7mm corrugated PVC cables from local electrical shop. This is [sample](https://shopee.com.my/1-Meter-Wire-Conduit-Split-Wire-Loom-Sleeve-Tube-Polyethylene-Black-Heat-Resistant-i.1374275.6233810382?adsid=0&campaignid=0&position=-1) for reference. |
| 4    | Pressure sensors Transducers                         | RM28.10                             | 3 pcs        | RM84.30                    | [EBOWAN DC 5V G1/4 Pressure Sensor Transmitter Pressure Transducer 1.2 MPa 174 PSI For Water Gas Oil](https://www.aliexpress.com/item/32656389610.html?spm=a2g0s.9042311.0.0.b5d94c4dM5VH00)<br /><br />Working Voltage: 5VDC<br/>Output Voltage: 0.5-4.5 VDC<br/>Sensor material: Carbon steel alloy<br/>Working Pressure Range: 0-1.2 MPa<br /><br /><br />Pressure range is 0-1.2MPa/0-12Bar/0-174 Psi with voltage output of 0.5v to 4.5v (Linear). More than enough for household at 12 bar but note that connection is 1/4" |
| 5    | Fittings and Poly couplers for pressure transmitters | RM4.50 (Fittings) + RM9.00 couplers | 3 sets       | RM40                       | This time another search looking at local hardware stores and couldnt locate any 1/4" to 3/4" or 1/2" couplers that I can fit to DN25 Female Poly Tee.<br /><br /><br />So search online and landed on this [SUS304 1/4" to 1" fitting.](https://shopee.com.my/SUS304-STAINLESS-STEEL-PIPING-FITTINGS-PIPE-FITTING-REDUCING-BUSH-WITH-BSP-THREAD-(1-4-to-1-)-i.92100121.7206904649) |
|      | Total Hardware                                       |                                     |              | RM210.50                   | Estimate lah                                                 |

Electronics

| #    | Item                                                       | Unit Price, RM                      | Quantity | Total Price, excl Shipping | Remark and Link                                              |
| ---- | ---------------------------------------------------------- | ----------------------------------- | -------- | -------------------------- | ------------------------------------------------------------ |
| 1    | ESP8286/ESP32 with baseboard                               | RM 16.20+ RM 56.20                  | 1 set    | 72.40                      | ESP8286 or ESP32 choice is yours. <br /><br />My curent setup uses [NodeMCU Lolin 8286](https://shopee.com.my/NodeMCU-Lolin-V3-ESP-12E-WiFi-ESP8266-Internet-Module-Development-Board-IOT-i.132528683.2134763370?adsid=0&campaignid=0&position=-1). <br />Make sure to get baseboard [like this](https://shopee.com.my/Nodemcu-Lolin-Baseboard-WIFI-Development-Board-ESP8266-Serial-Port-i.132528683.2279370073?position=5) which makes power supply input, prototyping and assembling easier. |
| 2    | PSU                                                        | RM45                                | 1 unit   | RM45.00                    | I went for a compact 240vac to 12vdc PSU converter purchased from local electrical shop. If not mistaken rated for 3A. Reason for 12vdc instead of 5vdc was considering future valve actuators that I may put in. <br /><br />Sample online [here](https://shopee.com.my/Mean-Well-LRS-35-12-35Watt-12VDC-RS-25-24-RS-25-12-Power-Supply-25W-12VDC-24VDC-MW-PSU-2-YEARS-WARRANTY-EX-STOCK-i.38331129.3720198864?position=18) |
| 3    | ADC converter - ADS1115 16-bit Analog to Digital Converter | RM23.80                             | 1 unit   | RM23.80                    | For Pressure Transmitters analog to digital converter. Be it for ESP8286 (comes with only 1 ADC channel) or ESP32 (multiple channel available) - voltage output range (0.5-4.5v) from pressure transmitters still would have required voltage divider circuit.<br />Plus ADS1115 provides additional 4 ADC channels for ESP8266 via I2C.<br /><br />Purchase from [online here](https://shopee.com.my/ADS1115-16-bit-Analog-to-Digital-Converter-ADC-CJMCU-ADS1115-i.132528683.6917873642) |
| 4    | Junction Box                                               | RM15.00                             | 1        | RM15.00                    | Local hardware store - see your own requirements and sizing. |
| 5    | Fittings and Poly couplers for pressure transmitters       | RM4.50 (Fittings) + RM9.00 couplers | 3 sets   | RM40                       | This time another search looking at local hardware stores and couldnt locate any 1/4" to 3/4" or 1/2" couplers that I can fit to DN25 Female Poly Tee.<br /><br /><br />So search online and landed on this [SUS304 1/4" to 1" fitting.](https://shopee.com.my/SUS304-STAINLESS-STEEL-PIPING-FITTINGS-PIPE-FITTING-REDUCING-BUSH-WITH-BSP-THREAD-(1-4-to-1-)-i.92100121.7206904649) |
|      | Total Electronics                                          |                                     |          | RM196.20                   | Buy local electrical store where possible.                   |

Other consumables - terminal blocks, dupont jumper wires (male/female), [PCB Spacer](https://shopee.com.my/100Pcs-HC-5-3mm-Nylon-Plastic-Stick-On-PCB-Spacer-Standoff-Locking-Snap-In-Posts-Fixed-Clips-Adhesive-Kit-i.190324344.7008938135)

## <a name="code">Code </a>

Download code from repo [here](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/diy_watermeter.yml).

```yaml
substitutions:
  name: diy-watermeter
  friendly_name: Diy Watermeter
  
esphome:
  name: diy-watermeter
  platform: ESP8266
  board: nodemcuv2

wifi:
  ssid: !secret wifi
  password: !secret wifi_pass

    
  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Diy Watermeter Fallback Hotspot"
    password: !secret ap_pass

captive_portal:

# esp8266 gpio layout
# D0
# D1 - I2C - ADS1115 SCL / Former pulse counter
# D2 - I2C - ADS1115 SDA
  # ADS1115
  # Channel A0 - Incoming
  # Channel A1 - After Filter (Tank)
  # Channel A2 - Household
  # Channel A3 - Spare
# D3 
# D4
# D5 - Water Pulse Counter / Former Relay
# D6 - Relay
# D7 - Relay
# D8
# RX
# TX
# A0

i2c:
  sda: D2
  scl: D1
  scan: true
  id: bus_a
  
ads1115:
  - address: 0x48
  
sensor:
  - platform: ads1115
    multiplexer: 'A0_GND'
    gain: 6.144 
    name: "A0 Voltage - Incoming Pressure Transmitter"
    device_class: voltage
    state_class: "measurement"
    update_interval: 30s
    
  - platform: ads1115
    multiplexer: 'A0_GND'
    gain: 6.144 
    name: "Main Water pressure - Bar"
    id: main_water_supply_bar
    device_class: pressure
    state_class: "measurement"
    icon: "mdi:gauge"
    accuracy_decimals: 2
    filters:
      - calibrate_linear:
            - 0.633 -> 0.34  
            - 0.862 -> 1.03
            - 1.11 -> 1.72
            - 1.47 -> 2.75
            - 1.63 -> 3.47
            - 2.02 -> 4.48
            - 2.01 -> 4.68
    unit_of_measurement: bar
    update_interval: 30s    
    
  - platform: ads1115
    multiplexer: 'A0_GND'
    gain: 6.144 
    name: "Main Water pressure - Psi"
    id: main_water_supply_psi
    device_class: pressure
    state_class: "measurement"
    icon: "mdi:gauge"
    accuracy_decimals: 2
    filters:
      - calibrate_linear:
            - 0.633 -> 5.0  
            - 0.862 -> 15.0
            - 1.11 -> 25.0
            - 1.47 -> 40.0
            - 1.63 -> 50.0
            - 2.02 -> 65.0
            - 2.01 -> 68.0
    unit_of_measurement: psi
    update_interval: 30s    
    
  - platform: ads1115
    multiplexer: 'A1_GND'
    gain: 6.144 
    name: "A1 Voltage - Tank Transmitter"
    device_class: voltage
    state_class: "measurement"
    id: a1_voltage
    update_interval: 30s    
    
    
  - platform: ads1115
    multiplexer: 'A1_GND'
    gain: 6.144 
    name: "Tank Water supply pressure - Bar"
    id: tank_water_supply_bar
    device_class: pressure
    state_class: "measurement"
    icon: "mdi:gauge"
    accuracy_decimals: 2
    filters:
      - calibrate_linear:
            - 0.633 -> 0.34  
            - 0.862 -> 1.03
            - 1.11 -> 1.72
            - 1.47 -> 2.75
            - 1.63 -> 3.47
            - 2.02 -> 4.48
            - 2.01 -> 4.68
    unit_of_measurement: bar
    update_interval: 30s    
    
  - platform: ads1115
    multiplexer: 'A1_GND'
    gain: 6.144 
    name: "Tank Water supply pressure - Psi"
    id: tank_water_supply_psi
    device_class: pressure
    state_class: "measurement"
    icon: "mdi:gauge"
    accuracy_decimals: 2
    filters:
      - calibrate_linear:
            - 0.633 -> 5.0  
            - 0.862 -> 15.0
            - 1.11 -> 25.0
            - 1.47 -> 40.0
            - 1.63 -> 50.0
            - 2.02 -> 65.0
            - 2.01 -> 68.0
    unit_of_measurement: psi
    update_interval: 30s    
    
    
  - platform: ads1115
    multiplexer: 'A2_GND'
    gain: 6.144 
    name: "A2 Voltage - Household Transmitter"
    device_class: voltage
    state_class: "measurement"
    id: a2_voltage
    update_interval: 30s
    
  - platform: ads1115
    multiplexer: 'A2_GND'
    gain: 6.144 
    name: "Household supply water pressure - Bar"
    id: household_water_supply_bar
    device_class: pressure
    state_class: "measurement"
    icon: "mdi:gauge"
    accuracy_decimals: 2
    filters:
      - calibrate_linear:
            - 0.633 -> 0.34  
            - 0.862 -> 1.03
            - 1.11 -> 1.72
            - 1.47 -> 2.75
            - 1.63 -> 3.47
            - 2.02 -> 4.48
            - 2.01 -> 4.68
    unit_of_measurement: bar
    update_interval: 30s  
    
  - platform: ads1115
    multiplexer: 'A2_GND'
    gain: 6.144 
    name: "Household supply water pressure - Psi"
    id: household_water_supply_psi
    device_class: pressure
    state_class: "measurement"
    icon: "mdi:gauge"
    accuracy_decimals: 2
    filters:
      - calibrate_linear:
            - 0.633 -> 5.0  
            - 0.862 -> 15.0
            - 1.11 -> 25.0
            - 1.47 -> 40.0
            - 1.63 -> 50.0
            - 2.02 -> 65.0
            - 2.01 -> 68.0
    unit_of_measurement: psi
    update_interval: 30s    

  - platform: ads1115
    multiplexer: 'A3_GND'
    gain: 6.144 
    name: "A3 - Voltage Ref"
    device_class: voltage
    state_class: "measurement"
    id: a3_voltage
    update_interval: 30s
    

  - platform: template
    name: "Water Filter DeltaP - bar"
    device_class: pressure
    state_class: "measurement"
    lambda: return ( id(main_water_supply_bar).state - id(tank_water_supply_bar).state );
    unit_of_measurement: bar
    update_interval: 30s    
    
  - platform: template
    name: "Water Filter DeltaP - psi"
    device_class: pressure
    state_class: "measurement"
    lambda: return ( id(main_water_supply_psi).state - id(tank_water_supply_psi).state );
    unit_of_measurement: psi
    update_interval: 30s    

  - platform: pulse_counter
    pin: D5
    unit_of_measurement: 'L/min'
    accuracy_decimals: 1
    update_interval: 60s
    name: 'Water Meter House'
    filters:
    - lambda: return (x / 27.0);  
    
    total:
      unit_of_measurement: 'L'
      name: 'Water Meter Total'
      accuracy_decimals: 1
      filters:
      - lambda: return (x / 27.0);

  - platform: wifi_signal
    name: "${friendly_name} Wifi "
    update_interval: 60s

switch:
  - platform: gpio
    pin: D6
    name: "Backyard Sprinkler Valve (NC)"  
    id: backyardsprinkler_valve    

  - platform: gpio
    pin: D7
    name: "Water Main Natural Flow to House Valve (NO)"  
    id: watermain_naturalflow_valve 

  - platform: restart
    name: "${friendly_name} Restart"
    

text_sensor:
  - platform: version
    name: ${friendly_name} ESPHome Version
    

# Enable logging
logger:

web_server:
  port: 80

# Enable Home Assistant API
api:
  reboot_timeout : 0s
    
ota:


```





## <a name="automations">Lovelace and Automations</a>

![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/lovelace-water%20consumption.PNG)

![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/lovelace-water%20consumption-weekly.PNG)

![](https://raw.githubusercontent.com/anas-ivs/ESPHome-Water-Meter/main/images/lovelace.PNG)

WIP. No automations yet as still in data gathering. Some early ideas to be implemented in Node Red:

1. Alert if inlet pressure goes to 0 to indicate water supply cutoff. Evaluate only if flow meter is 0 (not flowing).
2. Perform household minute leak check during quiet hours. Record pressure, and compare drop after interval. Abort test if flow meter register reading. 
3. Backwash - evaluate filter dP above defined threshold and evaluate only during no flow conditions. Store and compare max DP recorded over days/weeks to inform increased dP

## <a name="references">References</a>


1. [Home Assistant Forum - Using ESPHome to build water flow rate meter](https://community.home-assistant.io/t/using-esphome-to-build-a-water-flow-rate-meter/119380/21)
