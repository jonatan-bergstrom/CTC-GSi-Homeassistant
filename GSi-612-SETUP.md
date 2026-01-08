# CTC GSi 612 ESPHome Configuration Guide

## Overview
Complete ESPHome configuration for CTC GSi 612 heat pump with Modbus RTU communication over the display port (RJ12 connector).

## Hardware Setup

### Required Components
- **ESP32 NodeMCU** (or compatible ESP32 dev board)
- **RJ12 cable** (6P6C connector) - cut in half
- **5V power supply** (optional if powering from heat pump)
- Wires and soldering equipment

### RJ12 Pinout (looking at connector with tab down)
```
Pin 1: GND       → ESP32 GND
Pin 2: 5V        → ESP32 5V (or external 5V supply)
Pin 3: Not used
Pin 4: RX        → ESP32 GPIO16 (RX2)
Pin 5: Flow Ctrl → ESP32 GPIO5
Pin 6: TX        → ESP32 GPIO17 (TX2)
```

### Wiring Diagram
```
CTC Heat Pump Display Port (RJ12)
┌───────────────────┐
│  1 2 3 4 5 6      │
└───┬───┬───┬───┬───┘
    │   │   │   │   │
   GND 5V  │  RX FC TX
           │   │   │  │
          N/C  │   │  │
               │   │  │
           ┌───┴───┴──┴────┐
           │ ESP32 NodeMCU │
           │ GPIO16 GPIO5  │
           │ GPIO17        │
           └───────────────┘
```

## Software Setup

### 1. Install ESPHome
```bash
# Using pip
pip install esphome

# Or using Home Assistant Add-on (recommended)
# Install ESPHome from the Add-on Store
```

### 2. Configure Secrets
```bash
# Copy the template
cp secrets.yaml.template secrets.yaml

# Edit with your values
nano secrets.yaml
```

Generate API encryption key:
```bash
openssl rand -base64 32
```

### 3. Validate Configuration
```bash
esphome config ctc-gsi-612-complete.yaml
```

### 4. Initial Flash (USB)
```bash
# Connect ESP32 via USB
esphome run ctc-gsi-612-complete.yaml
```

### 5. Subsequent Updates (OTA)
After initial flash, updates can be done over WiFi:
```bash
esphome run ctc-gsi-612-complete.yaml
```

## Modbus Communication Details

### Protocol Settings
- **Protocol**: Modbus RTU
- **Baud Rate**: 9600
- **Parity**: EVEN
- **Stop Bits**: 1
- **Slave Address**: 0x01
- **Flow Control**: GPIO5 (RS485 direction control)

### Register Map Summary
Based on "CTC modbus parameter list.pdf" (2012 version):

#### Temperature Sensors (Read-Only)
- **600-610**: DHW and tank temperatures
- **630-640**: Heat pump refrigerant temperatures
- **606**: Outdoor temperature
- **607**: Primary flow temperature

#### Pressure Sensors (Read-Only)
- **637**: High pressure
- **638**: Low pressure

#### Status Registers (Read-Only)
- **550**: System status bits (alarms, heating on, etc.)
- **620**: Output relay status (pumps, valves, immersion heaters)
- **650**: Heat pump relay status
- **656-659**: Alarm registers

#### Statistics (Read-Only)
- **290-291**: Total operating hours (low/high)
- **663-664**: Compressor operating hours (low/high)
- **665**: Compressor runtime last 24h
- **666**: Compressor starts last 24h

#### Setpoints (Read/Write)
- **203**: Room 1 setpoint temperature
- **208-209**: Max/min primary flow temperature
- **212-213**: Heating curve slope/offset
- **238**: Max immersion heater power
- **240**: Main fuse size

#### Control Registers (Write)
- **207**: DHW mode (Economy/Normal/Comfort)
- **232**: Compressor enable/block
- **242**: Tariff control enable

## Home Assistant Integration

### Automatic Discovery
Once ESPHome is running and connected to your network, it will automatically appear in Home Assistant under:
**Settings → Devices & Services → ESPHome**

### Entity Categories

#### Climate Control
- Room temperatures (1-2)
- Primary flow temperature
- Heating curve slope/offset
- Room setpoints
- Max/min flow temperatures

#### Hot Water
- Tank temperatures (upper/middle/lower/DHW)
- DHW mode (Economy/Normal/Comfort)
- Extra DHW time
- DHW valve status

#### Heat Pump Status
- Compressor running
- Brine pump running
- Brine in/out temperatures
- Flow in/out temperatures
- High/low pressures
- Discharge/suction temperatures
- Superheat
- Expansion valve position

#### Power & Current
- Current L1/L2/L3
- Heat capacity (kW)
- Max immersion heater power
- Immersion heater status (per phase)

#### Statistics
- Total operating hours
- Compressor operating hours
- Runtime last 24h
- Starts last 24h
- Calculated COP (efficiency)

#### Alarms
- Alarm active (main indicator)
- Specific alarms (pump overload, high pressure, low brine flow, etc.)

#### System Controls
- Compressor enable/disable
- Tariff control
- Vacation mode (days)
- Room sensor enable
- Heating circuit 2 enable

## Usage Examples

### 1. Monitor System Health
Create a Home Assistant dashboard card:
```yaml
type: entities
title: Heat Pump Health
entities:
  - entity: binary_sensor.ctc_gsi_612_alarm_active
  - entity: text_sensor.ctc_gsi_612_system_status
  - entity: binary_sensor.ctc_gsi_612_compressor_running
  - entity: sensor.ctc_gsi_612_compressor_cop
  - entity: sensor.ctc_gsi_612_high_pressure
  - entity: sensor.ctc_gsi_612_low_pressure
```

### 2. Solar Integration (Future)
Use the exposed controls for smart operation:
```yaml
automation:
  - alias: "Boost DHW with Solar Surplus"
    trigger:
      - platform: numeric_state
        entity_id: sensor.solar_power_surplus
        above: 2000  # 2kW surplus
    action:
      - service: number.set_value
        target:
          entity_id: number.ctc_gsi_612_extra_dhw_time
        data:
          value: 2  # 2 hours extra DHW
```

### 3. Tariff-Based Control
Block expensive operation during peak hours:
```yaml
automation:
  - alias: "Block Compressor During Peak Tariff"
    trigger:
      - platform: time
        at: "17:00:00"  # Peak starts
    action:
      - service: switch.turn_off
        target:
          entity_id: switch.ctc_gsi_612_compressor_enable

  - alias: "Enable Compressor After Peak"
    trigger:
      - platform: time
        at: "21:00:00"  # Peak ends
    action:
      - service: switch.turn_on
        target:
          entity_id: switch.ctc_gsi_612_compressor_enable
```

### 4. Efficiency Monitoring
Track heat pump performance:
```yaml
# Template sensor in configuration.yaml
template:
  - sensor:
      - name: "Heat Pump Efficiency Trend"
        unit_of_measurement: "COP"
        state: >
          {{ states('sensor.ctc_gsi_612_compressor_cop') | float }}
        availability: >
          {{ states('sensor.ctc_gsi_612_compressor_cop') not in ['unknown', 'unavailable'] }}
```

## Troubleshooting

### No Communication
1. **Check wiring**: Verify RX/TX are not swapped
2. **Check flow control**: GPIO5 must be connected
3. **Check modbus address**: Default is 0x01
4. **Check baud rate**: Must be 9600 with EVEN parity
5. **View logs**: `esphome logs ctc-gsi-612-complete.yaml`

### Incorrect Values
1. **Check register addresses**: Different firmware may have different registers
2. **Check data type**: S_WORD vs U_WORD matters for signed/unsigned
3. **Check multiplication factor**: Most temps are ×0.1, pressures ×0.1 bar
4. **Monitor raw values**: Enable DEBUG logging in logger section

### Connection Drops
1. **Flow control timing**: Increase `send_wait_time` in modbus config
2. **Power supply**: Ensure stable 5V supply to ESP32
3. **Cable quality**: Use shielded cable for longer runs
4. **Update interval**: Reduce `update_interval` if too aggressive

### ESP32 Crashes
1. **Memory issues**: Disable some sensors if memory constrained
2. **Watchdog timeout**: Increase modbus timeout values
3. **WiFi issues**: Check signal strength and router compatibility

## Advanced Configuration

### Add Custom Sensors
To add more sensors from the register map:
```yaml
sensor:
  - platform: modbus_controller
    modbus_controller_id: ctc_gsi
    name: "Your Custom Sensor"
    id: custom_sensor
    address: XXX  # Register number from PDF
    register_type: read
    value_type: S_WORD  # or U_WORD
    unit_of_measurement: "°C"
    accuracy_decimals: 1
    filters:
      - multiply: 0.1  # if needed
```

### Add Write Controls
For registers marked R/W in the PDF:
```yaml
number:
  - platform: modbus_controller
    modbus_controller_id: ctc_gsi
    name: "Your Setting"
    id: your_setting
    address: XXX
    value_type: S_WORD
    min_value: 0
    max_value: 100
    step: 1
    mode: box
    multiply: 10  # if register stores value×10
```

### Performance Tuning
Adjust update intervals based on your needs:
```yaml
modbus_controller:
  - id: ctc_gsi
    update_interval: 5s  # Faster updates (default 10s)
```

Or set individual sensor update rates:
```yaml
sensor:
  - platform: modbus_controller
    # ... sensor config ...
    update_interval: 60s  # Override for this sensor only
```

## Register Map Verification

**Important**: The register addresses in this configuration are based on the "CTC modbus parameter list.pdf" (2012 version). Your specific model may have:
- Different register addresses
- Additional registers
- Different data formats

To verify your registers:
1. Enable DEBUG logging
2. Monitor successful reads
3. Cross-reference with actual display values
4. Adjust addresses as needed

## Security Considerations

1. **Network Security**: Place ESP32 on isolated IoT VLAN
2. **API Encryption**: Always use encrypted API communication
3. **OTA Password**: Use strong OTA password
4. **Firewall Rules**: Block external access to ESP32
5. **Regular Updates**: Keep ESPHome firmware updated

## Future Enhancements

Planned features for solar/battery integration:
- [ ] Smart DHW heating based on solar forecast
- [ ] Battery charge state integration
- [ ] Dynamic tariff pricing integration
- [ ] Predictive heating based on weather
- [ ] Advanced COP calculations
- [ ] Energy cost tracking
- [ ] Automatic defrost optimization

## Support & Resources

- **ESPHome Docs**: https://esphome.io/
- **Modbus Component**: https://esphome.io/components/modbus_controller.html
- **Home Assistant**: https://www.home-assistant.io/
- **CTC Manual**: Check with CTC for latest documentation

## License

This configuration is provided as-is for educational and personal use.

## Contributing

Found an issue or want to add features?
- Verify register addresses with your heat pump
- Test thoroughly before committing changes
- Document any model-specific differences

---

**Last Updated**: 2026-01-08
**Tested With**: CTC GSi 612, ESPHome 2024.x, Home Assistant 2024.x
