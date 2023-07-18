# Home-Assistant-Laundry-Monitor
Home Assistant Laundry monitor for washers and dryers that don't have "smart/IOT" features.

This custom monitor utilizes an energy/power meter, a magnetic door contact sensor, and a vibration sensor(SW-420). The monitor reports the laundry status through Home Assistant and sends phone notification reminders to move clothes from the washer to the dryer.

  * Power Meter: Monitors washer machine status (running or off)  
  * Magnetic door contact sensor: This is for checking that the clothes get unloaded and moved to the dryer. Sends a phone notification reminder every X minutes, waits to send a notification if dryer is running  
  * Vibration Sensor: Monitors dryer status (running or off)  

v1.0  
Created: on July 14 2023  
Author: bklen

![sensor_done](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/52bdf8a4-4f78-4793-9c68-6ee79638ea57)
![sensor_done2](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/12e7431e-cd7e-4978-a6af-0a6e73ff5494)
![sensor_done3-2](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/9d8fe9eb-7430-4a79-959b-f2f27ddbd3f1)

## Dependencies
### Home Assistant
  * Setup: https://www.home-assistant.io/installation/
#### Required Home Assistant Add-ons
  * [ESPHome](https://esphome.io/guides/getting_started_hassio.html)
     * Tool for flashing custom firmware onto an esp32 and integrating it into Home Assistant
  * [Node-RED](https://community.home-assistant.io/t/home-assistant-community-add-on-node-red/55023)
     * Tool for creating automations (visual tool)
     * Planning to re-implement automation using [AppDaemon](https://appdaemon.readthedocs.io/en/latest/HASS_TUTORIAL.html) (python based)
#### Required HW + Home Assistant Add-ons if using a zigbee power meter
  * This is optional, as you can use a power meter with Wi-Fi
  * [Zigbee2MQTT](https://www.zigbee2mqtt.io/)
     * Requires a [Zigbee Gateway](https://a.co/d/egiW8Ci)
     * [Installation Guide](https://youtu.be/4y_dDgo0i2g)
#### Recommended Home Assistant Add-Ons
  * [Studio Code Server](https://github.com/hassio-addons/addon-vscode)
    * Allows you to edit your Home Assistant configuration directly from your web browser, directly from within the Home Assistant frontend.

## Parts List
#### Parts
  * 1x [NodeMCU ESP32](https://a.co/d/aapvXlG)
     * [Pinout](https://esphome.io/devices/nodemcu_esp32.html)
  * 1x [Zigbee power meter](https://a.co/d/cHj3YuQ) *Requires zigbee gateway
     * Wi-Fi option: [Wi-Fi power meter](https://a.co/d/7lOBCGK)
  * 1x [Magnetic Switch Door Contact Sensor](https://a.co/d/dohbyF1)
  * 1x [Vibration Sensor(SW-420)](https://a.co/d/ieqYwL2)
  * [JST-PH 2.0 mm Connectors](https://a.co/d/cSBHDaF)
  * [28 AWG Silicone Stranded Wire](https://a.co/d/01jhN7x)
  * [Heat Shrink Tubing](https://a.co/d/fKoglEz)
  * [Perfboard](https://a.co/d/fmNVSKt)
  * [2.54mm Headers](https://a.co/d/e0YEANM)
  * [Micro USB Wall Charger 5V 2A](https://a.co/d/495GQlT)
     * Alternatively you can power the esp32 by providing power to the 3.3V(regulated) or 5V(unregulated 5V - 12V) + GND Pin
        * CAUTION: powering the 3.3V pin bypasses the voltage regulator. DO NOT exceed the 3.3V limit!
        * NEVER power an esp32 with a combination of those options at the same time.  
          For example: do not power your ESP32 dev kit via the 5V pin using a 10V input, while at the same time having the module connected to your computer via USB. This will damage your module, and potentially even your computer.
  * OPTIONAL: 1x [ESP32 Project Box](https://a.co/d/aEPeZNm)
  * OPTIONAL: 1x [Vibration Sensor Project Box](https://a.co/d/2jSt2Jr)
  * OPTIONAL: [Command Strips](https://a.co/d/0DbQcTY)  
#### Tools/misc.
  * [Wire Crimper - Pad-11](https://a.co/d/80Gwc3Q) (JST-PH connector)
     * [JST Connector Info](https://www.mattmillman.com/info/crimpconnectors/common-jst-connector-types)
     * [Budget option - IWS-2820M](https://a.co/d/70fK3cf)
  * [Wire Stripper - Hakko CHP CSP-30-1](https://a.co/d/fWNSyn9)
  * [Heat Gun](https://a.co/d/gOvbMbD)
  * [solder Iron - PINECIL](https://pine64.com/product/pinecil-smart-mini-portable-soldering-iron/)
     * Recommended:
        * [Soldering Short Tip Set (Fine)](https://pine64.com/product/pinecil-soldering-short-tip-set-fine/)
        * [T12‑BC3 Soldering Tip](https://a.co/d/h2QJXb6)
        * [SILICONE POWER CHARGING CABLE](https://pine64.com/product/usb-type-c-to-usb-type-c-silicone-power-charging-cable-1-5-meter-length/)
        * [65W GaN USB-C Wall Charger with Power Delivery PD](https://www.amazon.com/gp/product/B087MFLLCR/ref=ppx_yo_dt_b_asin_title_o01_s00?ie=UTF8&psc=1)
     * [Reliable alternative: - Weller Wlc100 40-Watt](https://a.co/d/bisxCsw)
  * [Solder](https://a.co/d/h9MQqTI)
  * [Flux](https://a.co/d/9jHg3QS)
  * [BreadBoard](https://a.co/d/3P9cGXa)
  * [Jumper Wires](https://a.co/d/16FHj63)

## Step 1: Wire Prototype + Flash ESP32 with ESPHome
[ESP32 Basic Pinout](https://github.com/AchimPieters/esp32-homekit-camera/blob/master/Images/ESP32-38%20PIN-DEVBOARD.png) (38 Pin)  
[ESP32 Detailed Pinout](https://esphome.io/devices/nodemcu_esp32.html)
  1. Connect the Vibration Sensor(SW-420) to the ESP32  
     Vibration Sensor(SW-420) Vcc -> ESP32 3.3V pin  
     Vibration Sensor(SW-420) GND -> ESP32 GND pin  
     Vibration Sensor(SW-420) DO -> ESP32 GPIO32  
  2. Connect the Magnetic Door Contact Sensor to the ESP32  
     Contact sensor wire -> ESP32 GND pin  
     Contact sensor wire -> ESP32 GPIO22  
![20230712_225639](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/e86c7b3e-3c73-406e-9261-af3bb18d98dc)

  3. Compile and upload laundry-room-monitor.yaml to esp32 using ESPHome
  ```YAML
  esphome:
    name: laundry-room-monitor
    friendly_name: laundry_room_monitor
  
  esp32:
    board: nodemcu-32s
    framework:
      type: arduino
  
  # Enable logging
  logger:
  
  # Enable Home Assistant API
  api:
    encryption:
      key: "api-password"
  
  ota:
    password: "ota-password"
  
  wifi:
    ssid: !secret wifi_ssid
    password: !secret wifi_password

   # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "network-id"
    password: "network-password"

  captive_portal:
  web_server:
    port: 80

  # Sensors that the ESPhome unit is capable of reporting
  sensor:
    - platform: wifi_signal
      name: "laundry_room_monitor WiFi Signal"
      update_interval: 60s
    - platform: uptime
      name: "laundry_room_monitor Uptime"
      update_interval: 60s
  
  # Enables the door contact sensor
  binary_sensor:  
    - platform: gpio  
      name: "washer machine door"  
      pin:
        number: GPIO22
        mode: INPUT_PULLUP
      device_class: door
    - platform: gpio  
      name: "dryer vibration"  
      pin:
        number: GPIO32
        mode: INPUT_PULLDOWN
      device_class: running
      filters:
       - delayed_on: 10ms
       - delayed_off: 2000ms
  ```
  ![esphome_node](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/533e1936-4d9f-4461-a475-84365501d405)

  4. After flashing and adding the node to ESPHome, verify that the connected sensors change/update the states correctly.
  ![esphome_node2](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/3144b766-90d3-413b-987c-52208d9101fc)
  ![dash-edit-1](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/7217d878-c956-4222-b44a-c5e24e4e3b22)

## Step 2: Add Power/Energy Meter to Home Assistant
  1. Configure/Add power meter to Home Assistant  
  Zigbee Example:
  ![zigbee](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/d9b53256-dcdf-4b81-9cd4-62760f90b5d5)
  2. With the power meter added, we will use it to create a binary sensor [Template](https://www.home-assistant.io/integrations/template/).  
     The created binary sensor entity has 2 states: "Running" if power is > 5W, and "Not running" when power <= 5W. This will allow us to track the washer status on our dashboard, rather than the washer power usage data.
     ![dash-edit-3](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/f9fab48d-72b0-4341-924a-0276199f3210)
     Edit your configuration.yaml to add the binary sensor template.
     ``` YAML
     template:
      - binary_sensor:
          - name: Washer Status
            device_class: running
            state: "{{ states('sensor.washing_machine_zigbee_power') > '5'}}"
            icon: "mdi:washing-machine"
      ```

## Step 3: Create Automation with Node-RED
![node-red-l](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/a737e53e-ebae-4b7a-832a-7b1b2280adde)
```
[{"id":"1d70e5a938eac4ba","type":"tab","label":"Laundry Monitor","disabled":false,"info":"","env":[]},{"id":"bdea01a8e01c833a","type":"server-state-changed","z":"1d70e5a938eac4ba","name":"Washer Started","server":"dbe576a9.8a5ed8","version":4,"exposeToHomeAssistant":false,"haConfig":[{"property":"name","value":""},{"property":"icon","value":""}],"entityidfilter":"sensor.washing_machine_zigbee_power","entityidfiltertype":"exact","outputinitially":false,"state_type":"num","haltifstate":"5","halt_if_type":"num","halt_if_compare":"gt","outputs":2,"output_only_on_state_change":true,"for":"0","forType":"num","forUnits":"minutes","ignorePrevStateNull":false,"ignorePrevStateUnknown":false,"ignorePrevStateUnavailable":false,"ignoreCurrentStateUnknown":false,"ignoreCurrentStateUnavailable":false,"outputProperties":[{"property":"payload","propertyType":"msg","value":"","valueType":"entityState"},{"property":"data","propertyType":"msg","value":"","valueType":"eventData"},{"property":"topic","propertyType":"msg","value":"","valueType":"triggerId"}],"x":100,"y":80,"wires":[["dcc0d6672afc28e8"],[]]},{"id":"dcc0d6672afc28e8","type":"ha-wait-until","z":"1d70e5a938eac4ba","name":"Washer Ended","server":"dbe576a9.8a5ed8","version":2,"outputs":1,"entityId":"sensor.washing_machine_zigbee_power","entityIdFilterType":"exact","property":"state","comparator":"is","value":"0","valueType":"str","timeout":"0","timeoutType":"num","timeoutUnits":"seconds","checkCurrentState":true,"blockInputOverrides":true,"outputProperties":[],"entityLocation":"data","entityLocationType":"none","x":280,"y":80,"wires":[["bc88915e90ba0442"]]},{"id":"113f8b5e8318e20a","type":"ha-wait-until","z":"1d70e5a938eac4ba","name":"Washer Door Opened? (unloaded)","server":"dbe576a9.8a5ed8","version":2,"outputs":2,"entityId":"binary_sensor.laundry_room_monitor_washer_machine_door","entityIdFilterType":"exact","property":"state","comparator":"is","value":"on","valueType":"str","timeout":"10","timeoutType":"num","timeoutUnits":"minutes","checkCurrentState":true,"blockInputOverrides":true,"outputProperties":[],"entityLocation":"data","entityLocationType":"none","x":1420,"y":160,"wires":[[],["bc88915e90ba0442"]]},{"id":"12440e92e99ab36b","type":"api-call-service","z":"1d70e5a938eac4ba","name":"move clothes to dryer phone notification (Brandon)","server":"dbe576a9.8a5ed8","version":5,"debugenabled":false,"domain":"notify","service":"mobile_app_sm_g991u","areaId":[],"deviceId":[],"entityId":[],"data":"{\"message\":\"move clothes to dryer!\"}","dataType":"json","mergeContext":"","mustacheAltTags":false,"outputProperties":[],"queue":"none","x":1050,"y":100,"wires":[["113f8b5e8318e20a"]]},{"id":"bc88915e90ba0442","type":"api-current-state","z":"1d70e5a938eac4ba","name":"Dryer Running?","server":"dbe576a9.8a5ed8","version":3,"outputs":2,"halt_if":"on","halt_if_type":"str","halt_if_compare":"is","entity_id":"binary_sensor.laundry_room_monitor_dryer_vibration","state_type":"str","blockInputOverrides":false,"outputProperties":[{"property":"payload","propertyType":"msg","value":"","valueType":"entityState"},{"property":"data","propertyType":"msg","value":"","valueType":"entity"}],"for":"0","forType":"num","forUnits":"minutes","override_topic":false,"state_location":"payload","override_payload":"msg","entity_location":"data","override_data":"msg","x":460,"y":100,"wires":[["72406a2e2da9b2e6"],["12440e92e99ab36b"]]},{"id":"72406a2e2da9b2e6","type":"ha-wait-until","z":"1d70e5a938eac4ba","name":"Dryer Ended","server":"dbe576a9.8a5ed8","version":2,"outputs":1,"entityId":"binary_sensor.laundry_room_monitor_dryer_vibration","entityIdFilterType":"exact","property":"state","comparator":"is","value":"off","valueType":"str","timeout":"0","timeoutType":"num","timeoutUnits":"seconds","checkCurrentState":true,"blockInputOverrides":true,"outputProperties":[],"entityLocation":"data","entityLocationType":"none","x":630,"y":40,"wires":[["12440e92e99ab36b"]]},{"id":"dbe576a9.8a5ed8","type":"server","name":"Home Assistant","addon":true}]
```

## Step 4: Assemble Device
  1. Cut the perfboard to fit into the project box.  
     Cut the 2.54mm Headers to fit the ESP32
  ![step1](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/141f17d1-c9a5-4592-8843-09d700fbb5e4)
  ![step2](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/2c69a12f-e469-401a-ae8b-2123fc1424bb)
  2. Solder the headers to the perfboard
  ![step3](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/3a15494c-87e5-453c-b9b1-27350056f611)
  3. Solder on the JST-PH connectors and complete the circuit with solder bridges
  ![step4](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/cb652003-02d4-4bd0-ae53-565ecac1181d)
  ![step4-2](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/45d9163b-e367-4f25-b27b-be65a87ac7b5)
  4. Cut 5x 28 AWG stranded wires. These will be the wires that attach to the vibration sensor and magnetic door contact sensor.  
     Crimp the wires and attach the JST-PH connectors.
     ![crimp_final1](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/75aff8b2-d64f-40d1-8ab0-73b025eda494)
     ![crimp_final2](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/4b245bcc-dafe-49bc-a6ab-46cc884be2be)
  5. **Before** soldering on vibration sensor:  
     Slide on heat shrink tubing over the wires:
       * 1 tube for each individual wire(x3)
       * OPTIONAL: small tubes over all 3 wires
     
     Solder on vibration sensor.
     ![vibration-1](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/589da7ab-21d0-4f99-b41e-bdbc44acdccb)
     ![vibration-2](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/5488f151-8508-497b-ac7f-d92fc7d690d1)
  6. Repeat the process for the magnetic door contact sensor
     ![contact-sens](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/24ee4f82-b336-4ef5-8e3c-d0f753e06b03)
  7. Optional: Use black nail polish to dim/cover red power  LED
     * Note: the red power LED on ESP32 devboards are hard-wired and can't be disabled
     * [ESP32 Schematic](https://dl.espressif.com/dl/schematics/esp32_devkitc_v4-sch.pdf) <- red LED is connected directly to the Power rail
     ![black_nail](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/870891e1-6595-4bc5-acd8-32849b35b015)
  8. Finish assembling the device!  
     Cut the project boxes to fit the wires.  
     Attach the vibration sensor to the box with a command strip.
     ![finish-1](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/6463027e-fb6d-447b-83db-bd3002579c2b)
     ![finish2](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/11509ec4-c6de-4b9d-9fa4-e9d1bd496c9d)
     ![sensor_done3-2](https://github.com/bklen/Home-Assistant-Laundry-Monitor/assets/6707864/9d8fe9eb-7430-4a79-959b-f2f27ddbd3f1)









