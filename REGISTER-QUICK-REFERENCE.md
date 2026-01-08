# CTC GSi Modbus Register Quick Reference

## Register Map Summary

### Temperature Sensors (S16, ×0.1°C) - READ ONLY

| Register | Description | Range |
|----------|-------------|-------|
| 600 | DHW Temperature | -40 to +100°C |
| 601 | Upper Tank Temperature | -40 to +100°C |
| 602 | Middle Tank Temperature | -40 to +100°C |
| 603 | Lower Tank Temperature | -40 to +100°C |
| 604 | Condenser Temperature Out | -40 to +100°C |
| 605 | Condenser Temperature In | -40 to +100°C |
| 606 | Outdoor Temperature | -40 to +50°C |
| 607 | Heating Circuit 1: Primary Flow | -40 to +100°C |
| 609 | Room Sensor 1 Temperature | -10 to +50°C |
| 610 | Room Sensor 2 Temperature | -10 to +50°C |
| 630 | HP Brine Out Temperature | -40 to +50°C |
| 631 | HP Brine In Temperature | -40 to +50°C |
| 632 | HP Flow In Temperature | -40 to +100°C |
| 634 | HP Flow Out Temperature | -40 to +100°C |
| 635 | HP Discharge Temperature | -40 to +150°C |
| 636 | HP Suction Gas Temperature | -40 to +100°C |
| 639 | Calculated Condensing Temp | -40 to +100°C |
| 640 | Calculated Evaporating Temp | -40 to +50°C |

### Pressure Sensors (S16, ×0.1 bar) - READ ONLY

| Register | Description | Range |
|----------|-------------|-------|
| 637 | High Pressure | 0-40 bar |
| 638 | Low Pressure | 0-20 bar |

### Status & Control (S16) - READ ONLY

| Register | Description | Values |
|----------|-------------|--------|
| 550 | Status Bits | Bitmask (see below) |
| 620 | Output Relays | Bitmask (see below) |
| 650 | HP Relays | Bitmask (see below) |
| 656 | Alarm 1 | Bitmask (see below) |
| 657 | Alarm 2 | Bitmask (see below) |
| 658 | Alarm 3 | Bitmask (see below) |
| 659 | Alarm 4 | Bitmask (see below) |
| 571 | Work Mode | 0=HP Upper, 1=HP Lower, 2=Add Heat, 3=HP+Add |

### Performance Metrics - READ ONLY

| Register | Description | Unit/Factor |
|----------|-------------|-------------|
| 641 | Superheat | S16 ×0.1°C |
| 642 | Expansion Valve Position | S16 ×0.1% |
| 644 | Heat Quantity Counter | S16 ×0.1 kW |
| 645 | Soft Start Current | S16 ×0.1 A |
| 647 | Compressor Start Delay | S16 minutes |
| 648 | Charge Pump Value | S16 ×0.1% |

### Current Monitoring (U16, ×0.1A) - READ ONLY

| Register | Description |
|----------|-------------|
| 612 | Current L1 |
| 613 | Current L2 |
| 614 | Current L3 |

### Runtime Statistics - READ ONLY

| Register | Description | Unit |
|----------|-------------|------|
| 290 | Total Hours Low | U16 hours |
| 291 | Total Hours High | U16 ×1000 hours |
| 663 | Compressor Hours High | U16 ×1000 hours |
| 664 | Compressor Hours Low | U16 hours |
| 665 | Compressor Time/24h | U16 minutes |
| 666 | Compressor Starts/24h | U16 count |

### Setpoints (S16, ×0.1) - READ/WRITE

| Register | Description | Range | Unit |
|----------|-------------|-------|------|
| 203 | Room 1 Setpoint | 50-300 | 0.1°C |
| 204 | Room 2 Setpoint | 50-300 | 0.1°C |
| 205 | Vacation Days | 0-300 | days |
| 206 | Extra DHW Time | 0-20 | 0.5h |
| 207 | DHW Level | 0-2 | 0=Eco,1=Norm,2=Comf |
| 208 | HC1 Max Primary Flow | 300-800 | 0.1°C |
| 209 | HC1 Min Primary Flow | 140-650 | 0.1°C |
| 210 | HC1 Heating Off Temp | 100-300 | 0.1°C |
| 212 | HC1 Inclination | 250-850 | 0.1 |
| 213 | HC1 Adjustment | -200-200 | 0.1°C |
| 214 | HC1 Decrease Primary Flow | -400-0 | 0.1°C |
| 215 | HC1 Decrease Room Temp | -400-0 | 0.1°C |
| 238 | Immersion Heater Max Power | 0-90 | 0.1kW |
| 240 | Main Fuse Size | 100-350 | 0.1A |

### Control Switches (BOOL) - READ/WRITE

| Register | Bit | Description | Values |
|----------|-----|-------------|--------|
| 216 | 0 | HC1 Room Sensor Enable | 0=No, 1=Yes |
| 232 | - | Compressor Enable | 0=Blocked, 1=Enabled |
| 242 | - | Tariff Control | 0=Off, 1=On |
| 247 | - | Heating Circuit 2 | 0=Off, 1=On |

### Setpoints (READ ONLY Calculated)

| Register | Description | Unit |
|----------|-------------|------|
| 559 | HC1 Primary Flow Setpoint | S16 ×0.1°C |
| 561 | Lower Tank HP Stop Temp | S16 ×0.1°C |
| 562 | Upper Tank HP Stop Temp | S16 ×0.1°C |
| 563 | Lower Tank Setpoint | S16 ×0.1°C |
| 564 | Upper Tank Setpoint | S16 ×0.1°C |

### System Info - READ ONLY

| Register | Description |
|----------|-------------|
| 691 | Program Version Month/Day (U16) |
| 692 | Program Version Year (U16) |

## Bitmask Definitions

### Register 550 - Status Bits

| Bit | Description |
|-----|-------------|
| 0 | Alarm Active |
| 1 | High Current Consumption |
| 3 | Shut Off |
| 4 | Extra DHW Active |
| 5 | HC1 Heating On |
| 6 | HC2 Heating On |
| 8 | Lower Tank: Heat Pump |
| 9 | Upper Tank: Heat Pump |
| 10 | Tariff: Immersion Heater Blocked |
| 11 | Tariff: Heat Pump Blocked |

### Register 620 - Output Relays

| Bit | Description |
|-----|-------------|
| 0 | Radiator Pump 1 |
| 1 | Radiator Pump 2 |
| 2 | Mixing Valve 1: Open |
| 3 | Mixing Valve 1: Close |
| 4 | Mixing Valve 2: Open |
| 5 | Mixing Valve 2: Close |
| 6 | DHW Valve |
| 7 | Immersion Heater Low L1 |
| 8 | Immersion Heater High L1 |
| 9 | Immersion Heater Low L2 |
| 10 | Immersion Heater High L2 |
| 11 | Immersion Heater High L3 |
| 12 | Immersion Heater Low L3 |
| 13 | Immersion Heater 3kW |
| 14 | Immersion Heater 6kW |

### Register 650 - Heat Pump Relays

| Bit | Description |
|-----|-------------|
| 0 | Compressor On/Off |
| 3 | Brine Pump On/Off |

### Register 656 - Alarm 1

| Bit | Description |
|-----|-------------|
| 3 | Pump Overload |
| 4 | System Pump Overload |
| 5 | Compressor Overload |
| 7 | High Pressure |
| 12 | Low Brine Flow |
| 13 | Low Brine Temperature |

### Register 657 - Alarm 2 (Sensor Alarms)

| Bit | Description |
|-----|-------------|
| 0 | Sensor Brine Out |
| 1 | Sensor Brine In |
| 3 | Sensor Heat Pump In |
| 5 | Sensor Outdoor |
| 6 | Sensor Heat Pump Out |
| 8 | Sensor Discharge |
| 9 | Sensor Suction Gas |
| 10 | Sensor High Pressure |
| 11 | Sensor Low Pressure |
| 12 | Fan |

### Register 658 - Alarm 3

| Bit | Description |
|-----|-------------|
| 0 | Compressor Inverter |
| 13 | EVO Off |

### Register 659 - Alarm 4

| Bit | Description |
|-----|-------------|
| 0 | Compressor High Current |
| 1 | Compressor Low Current |
| 2 | Phase 1 Missing |
| 3 | Phase 2 Missing |
| 4 | Phase 3 Missing |
| 5 | Phase Sequence Error |
| 6 | Communication Error Softstarter |

## Common Read Sequences

### Basic Monitoring (10 registers)
```
600-607: Temperatures (DHW, tanks, outdoor, flow)
637-638: Pressures
550: Status
```

### Heat Pump Health (8 registers)
```
630-640: HP temperatures (brine, flow, discharge, suction, calc temps)
637-638: Pressures
641: Superheat
650: HP relays
656-659: Alarms
```

### Full System Status (20 registers)
```
600-610: All temperatures
612-614: Currents
620: Output relays
630-648: HP detailed status
650: HP relays
656-659: All alarms
```

## Write Operations

### Before Writing
1. Read current value first
2. Verify value is within valid range
3. Check register is marked R/W in manual
4. Use correct data type (S16 vs U16)
5. Apply multiplication factor if needed

### Write Example (Set Room Temp to 21°C)
```
Register: 203
Value: 21.0°C
Modbus Value: 210 (21.0 × 10)
Data Type: S_WORD (signed 16-bit)
Function Code: 0x06 (Write Single Register)
```

### Critical Safety Registers
**Do NOT write to these without understanding:**
- 232: Compressor enable (can damage system if used incorrectly)
- 238: Max immersion heater power (electrical safety)
- 240: Main fuse size (system protection)

## Modbus Function Codes

| Code | Name | Usage |
|------|------|-------|
| 0x03 | Read Holding Registers | Read any register (R or R/W) |
| 0x06 | Write Single Register | Write one register |
| 0x10 | Write Multiple Registers | Write multiple registers |

## Data Types

| Type | Description | Range | Bytes |
|------|-------------|-------|-------|
| S16/S_WORD | Signed 16-bit | -32768 to +32767 | 2 |
| U16/U_WORD | Unsigned 16-bit | 0 to 65535 | 2 |
| BOOL | Boolean bit | 0 or 1 | - |

## Multiplication Factors

| Type | Factor | Example |
|------|--------|---------|
| Temperature | 0.1°C | Reg=215 → 21.5°C |
| Pressure | 0.1 bar | Reg=85 → 8.5 bar |
| Current | 0.1 A | Reg=150 → 15.0 A |
| Power | 0.1 kW | Reg=30 → 3.0 kW |
| Percentage | 0.1% | Reg=850 → 85.0% |
| Time | varies | See register description |

## Notes

1. **Register numbers are in decimal** (not hex)
2. **All multi-byte values are big-endian**
3. **Update rate**: Max 1 request per second recommended
4. **Timeout**: 200ms between requests recommended
5. **Address offset**: Some tools use offset 0, others use 40001 base

---

**Source**: CTC modbus parameter list.pdf (2012 version)
**Verified**: CTC GSi 612, Firmware 20120712+
