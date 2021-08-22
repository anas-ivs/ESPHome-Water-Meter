# ESPHome-Water-Meter
Having embarked on SESCO (Electric) Meter node to measure my house electrical consumption, this project is embarked to measure and automate another important utility in the house - Water (LAKU) using Home Assistant &amp; ESP Home.
![visitors](https://visitor-badge.glitch.me/badge?page_id=anas-ivs.esphome-watermeter.visitor-badge)

| [Bill of Materials](#BoM) | [Hookup Diagram](#wiring) | [Code](#code) | [Automations](#Automations) |

## <a name="BoM">Bill of Materials </a>

A combination of local sellers and international - mostly via Shop** and A**Express and also some local hardware stores. Links are for sharing and proceed at own risk for seller latest validity/reputation.

Hardware

| #    | Item                                                 | Unit Price, RM                      | Quantity     | Total Price, excl Shipping | Remark and Link                                              |
| ---- | ---------------------------------------------------- | ----------------------------------- | ------------ | -------------------------- | ------------------------------------------------------------ |
| 1    | Turbine Water Meter                                  | 56.20                               | 1 pcs        | 56.20                      | [DN40 1.5 Turbine Meter (YF-DN40)](https://shopee.com.my/Dn40-1.5-Inch-Turbine-Flowmeter-Water-Flow-Sensor-Flow-Meter-Flow-Rate-Control-5~150L-Min-i.182354128.9231014650) Flow rate : 5~200L/min <br />Flow Pulse:  F(Hz)=(0.45xQ) +/-3%    <br />Q=L/min 1L water= 27Pulse<br /><br /><br /> |
| 2    | Fittings and Poly couplers for Turbine meter         | ~RM10                               | 2 each       | RM20                       | Take of your water main line size! My incoming mains poly pipe are at DN25 while Turbine Meter above is DN40!!<br /><br />Had to search hardware shops around down and landed one shop that was able to provide me with 1 1/2" to 3/4" fitting but in galvanized metal. Pair this with DN25 to 3/4" poly couplers. |
| 3    | Wiring + Weather Protector                           | ~RM0.80                             | +/- 10metres | RM10                       | For wiring - with Alarm panel Konnected project - I had plenty to spare so mostly re-use. These alarm panel comes in 4 core which is enough for transducers. <br /><br /><br />Since these cables are also not meant for sun/rain/heat - I covered put these cables in 7mm corrugated PVC cables from local electrical shop. This is [sample](https://shopee.com.my/1-Meter-Wire-Conduit-Split-Wire-Loom-Sleeve-Tube-Polyethylene-Black-Heat-Resistant-i.1374275.6233810382?adsid=0&campaignid=0&position=-1) for reference. |
| 4    | Pressure sensors Transducers                         | RM28.10                             | 3 pcs        | RM84.30                    | [EBOWAN DC 5V G1/4 Pressure Sensor Transmitter Pressure Transducer 1.2 MPa 174 PSI For Water Gas Oil](https://www.aliexpress.com/item/32656389610.html?spm=a2g0s.9042311.0.0.b5d94c4dM5VH00)<br /><br />Working Voltage: 5VDC<br/>Output Voltage: 0.5-4.5 VDC<br/>Sensor material: Carbon steel alloy<br/>Working Current: ≤10 mA<br/>Working Pressure Range: 0-1.2 MPa<br /><br /><br />Pressure range is 0-1.2MPa/0-12Bar/0-174 Psi with voltage output of 0.5v to 4.5v (Linear). More than enough for household at 12 bar but note that connection is 1/4" |
| 5    | Fittings and Poly couplers for pressure transmitters | RM4.50 (Fittings) + RM9.00 couplers | 3 sets       | RM40                       | This time another search looking at local hardware stores and couldnt locate any 1/4" to 3/4" or 1/2" couplers that I can fit to DN25 Female Poly Tee.<br /><br /><br />So search online and landed on this [SUS304 1/4" to 1" fitting.](https://shopee.com.my/SUS304-STAINLESS-STEEL-PIPING-FITTINGS-PIPE-FITTING-REDUCING-BUSH-WITH-BSP-THREAD-(1-4-to-1-)-i.92100121.7206904649) |
|      |                                                      |                                     |              |                            |                                                              |

Electronics

| #    | Item                                                       | Unit Price, RM                      | Quantity | Total Price, excl Shipping | Remark and Link                                              |
| ---- | ---------------------------------------------------------- | ----------------------------------- | -------- | -------------------------- | ------------------------------------------------------------ |
| 1    | ESP8286/ESP32 with baseboard                               | RM 16.20+ RM 56.20                  | 1 set    | 72.40                      | ESP8286 or ESP32 choice is yours. <br /><br />My curent setup uses [NodeMCU Lolin 8286](https://shopee.com.my/NodeMCU-Lolin-V3-ESP-12E-WiFi-ESP8266-Internet-Module-Development-Board-IOT-i.132528683.2134763370?adsid=0&campaignid=0&position=-1). <br />Make sure to get baseboard [like this](https://shopee.com.my/Nodemcu-Lolin-Baseboard-WIFI-Development-Board-ESP8266-Serial-Port-i.132528683.2279370073?position=5) which makes power supply input, prototyping and assembling easier. |
| 2    | PSU                                                        | RM45                                | 1 unit   | RM4.00                     | I went for a compact 240vac to 12vdc PSU converter purchased from local electrical shop. If not mistaken rated for 3A. Reason for 12vdc instead of 5vdc was considering future valve actuators that I may put in. <br /><br />Sample online [here](https://shopee.com.my/Mean-Well-LRS-35-12-35Watt-12VDC-RS-25-24-RS-25-12-Power-Supply-25W-12VDC-24VDC-MW-PSU-2-YEARS-WARRANTY-EX-STOCK-i.38331129.3720198864?position=18) |
| 3    | ADC converter - ADS1115 16-bit Analog to Digital Converter | RM23.80                             | 1 unit   | RM23.80                    | For Pressure Transmitters analog to digital converter. Be it for ESP8286 (comes with only 1 ADC channel) or ESP32 (multiple channel available) - voltage output range (0.5-4.5v) from pressure transmitters still would have required voltage divider circuit.<br />Plus ADS1115 provides additional 4 ADC channels for ESP8266 via I2C.<br /><br />Purchase from [online here](https://shopee.com.my/ADS1115-16-bit-Analog-to-Digital-Converter-ADC-CJMCU-ADS1115-i.132528683.6917873642) |
| 4    | Junction Box                                               | RM15.00                             | 1        | RM15.00                    | Local hardware store - see your own requirements and sizing. |
| 5    | Fittings and Poly couplers for pressure transmitters       | RM4.50 (Fittings) + RM9.00 couplers | 3 sets   | RM40                       | This time another search looking at local hardware stores and couldnt locate any 1/4" to 3/4" or 1/2" couplers that I can fit to DN25 Female Poly Tee.<br /><br /><br />So search online and landed on this [SUS304 1/4" to 1" fitting.](https://shopee.com.my/SUS304-STAINLESS-STEEL-PIPING-FITTINGS-PIPE-FITTING-REDUCING-BUSH-WITH-BSP-THREAD-(1-4-to-1-)-i.92100121.7206904649) |
|      |                                                            |                                     |          |                            |                                                              |



Other consumables - terminal blocks, dupont jumper wires (male/female), [PCB Spacer](https://shopee.com.my/100Pcs-HC-5-3mm-Nylon-Plastic-Stick-On-PCB-Spacer-Standoff-Locking-Snap-In-Posts-Fixed-Clips-Adhesive-Kit-i.190324344.7008938135)





## <a name="wiring">Hookup Diagram </a>

## <a name="code">Code </a>

## <a name="Automations">Automations</a>
