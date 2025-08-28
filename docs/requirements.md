# IoT Moisture Controller - Requirements Document

## Project Overview

The IoT Moisture Controller is a low-power, solar-powered device designed to monitor environmental conditions (humidity, temperature, and soil moisture) and provide automated control capabilities through relay outputs. The device operates on a cycled schedule to maximize battery life while maintaining reliable monitoring and communication.

## Hardware Requirements

### Core Components
- **Microcontroller**: ESP32-WROOM-32 module
  - WiFi connectivity required
  - Deep sleep capability for power management
  - Sufficient GPIO pins for sensors and outputs

### Sensors
- **Environmental Sensor**: DHT11
  - Measures ambient humidity and temperature
  - 3.3V/5V compatible
  - Digital output interface
- **Soil Moisture Sensor**: TBD
  - Must be compatible with ESP32 analog/digital inputs
  - Corrosion-resistant for long-term soil deployment
  - Low power consumption

### Power System
- **Solar Panel**: Small form factor
  - Sufficient capacity to maintain battery charge under normal conditions
  - Weather-resistant design
- **Battery**: Rechargeable lithium-ion/LiPo
  - Capacity sufficient for multi-day operation without solar charging
  - TBD v nominal cells with appropriate voltage regulation
- **Power Management**: Battery charging circuit with voltage regulation

### Output Controls
- **Switched Outputs**: 2x relays, mosfets, or transistors of choice.
  - Switching capacity: up to 2A per relay
  - Isolated control from microcontroller
  - Suitable for 12V/24V load switching

### Output Valves
- **Water valve**: 2x water valves to be controlled from the above outputs
  - Example valve https://www.digikey.com/en/products/detail/olimex-ltd/WATER-VALVE-6-5MM-12VDC/21661974

## Software Requirements

### Core Functionality
- **Sleep Management**: 
  - Deep sleep mode between measurement cycles
  - Configurable wake intervals
  - Power-optimized wake/sleep transitions

- **Sensor Data Collection**:
  - Read DHT11 temperature and humidity
  - Read soil moisture sensor data
  - Data validation and error handling
  - Timestamp data collection events

- **Communication**:
  - WiFi connection management
  - MQTT client implementation
  - Publish sensor data to configured MQTT broker
  - Subscribe to command topics for remote control
  - Connection retry and error handling

- **Remote Control**:
  - Receive and process relay control commands (Energize relay for n minutes)
  - Command validation and safety checks
  - Status feedback to MQTT broker

### Configuration Parameters
- WiFi credentials (SSID, password)
- MQTT broker settings (host, port, credentials, topics)
- Sleep duration/wake intervals
- Sensor calibration parameters
- Relay control permissions and safety limits

## Operational Requirements

### Power Management
- **Sleep Current**: <100µA during deep sleep
- **Active Current**: Minimize active time to <30 seconds per cycle
- **Solar Charging**: Maintain battery charge under typical sunlight conditions
- **Battery Life**: Minimum 3 days operation without solar input

### Communication
- **WiFi Range**: Operate within typical home/garden WiFi coverage
- **MQTT Reliability**: Handle intermittent connectivity gracefully
- **Data Frequency**: Configurable reporting intervals (default: hourly)
- **Command Response**: Process commands within next wake cycle

### Environmental
- **Operating Temperature**: -10°C to +50°C
- **Humidity**: 0-95% RH (non-condensing)
- **Weather Protection**: IP65 or better enclosure rating
- **Installation**: Suitable for outdoor garden/greenhouse deployment

## Data Interface

### MQTT Topics Structure
```
device/{device_id}/sensors/temperature
device/{device_id}/sensors/humidity
device/{device_id}/sensors/soil_moisture
device/{device_id}/status/battery
device/{device_id}/status/wifi_rssi
device/{device_id}/commands/relay1
device/{device_id}/commands/relay2
device/{device_id}/status/relay1
device/{device_id}/status/relay2
```

### Data Format
- **Sensor Data**: JSON format with timestamp, value, and units
- **Status Data**: JSON format with device health information
- **Commands**: JSON format with action, parameters, and validation

## Safety and Reliability

### Electrical Safety
- Overcurrent protection for output circuits
- ESD protection for sensor inputs

### Software Safety
- Watchdog timer implementation
- Command validation and bounds checking
- Fail-safe relay states on communication loss
- Error logging and recovery mechanisms

### Data Integrity
- Sensor data validation and outlier detection
- Command acknowledgment and status reporting
- Configuration backup and recovery

## Future Considerations

- Over-the-air (OTA) firmware update capability (would be nice)
- Additional sensor inputs (pH, light levels, etc.)
- Local data logging to SD card
- Web interface for local configuration
- Integration with home automation systems (MQTT)