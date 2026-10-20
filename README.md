# ShrikeSense
### Cloud-Connected Environmental Telemetry Prototype for Smart Agriculture

**AWS IoT Core | Amazon DynamoDB | Amazon CloudWatch | ESP32-S3 | MPU6050 | BMP280**

## Overview

ShrikeSense is an IoT telemetry prototype demonstrating secure sensor-data acquisition, edge processing, cloud storage, and cloud-based visualization using Amazon Web Services.

Originally developed as an embedded flight-monitoring system, the project was extended into a reusable AWS-connected sensing framework relevant to agricultural environmental monitoring.

The implemented prototype demonstrates the underlying telemetry infrastructure. Agriculture-specific functions such as soil moisture collection, weather-based irrigation decisions, and SMS notifications are proposed extensions.

## Project Objectives

- Interface sensors with an ESP32-S3 development board.
- Process sensor measurements locally.
- Transmit telemetry securely using MQTT over TLS.
- Store timestamped readings in Amazon DynamoDB.
- Query historical telemetry records.
- Visualize measurements using Amazon CloudWatch.
- Maintain local sensor processing when internet connectivity is unavailable.
- Establish a foundation for agricultural decision-support applications.

## Hardware Components

| Component | Purpose |
|---|---|
| Vicharak Shrike-Fi (ESP32-S3) | Main microcontroller and Wi-Fi connectivity |
| MPU6050 / GY-521 | Acceleration and angular velocity sensing |
| BMP280 | Barometric pressure and relative altitude sensing |
| Breadboard and jumper wires | Sensor interconnections |
| USB-C cable | Power and firmware programming |

### Sensor Connections

Both sensors communicate over I²C.

| Connection | ESP32-S3 |
|---|---|
| SDA | GPIO17 |
| SCL | GPIO18 |
| Sensor ground | GND |
| Sensor power | Compatible 3.3 V supply |

MPU6050 address: `0x68`

BMP280 address: `0x76`

## Implemented AWS Architecture

**Shrike-Fi ESP32-S3 → Wi-Fi → AWS IoT Core → AWS IoT Rule → DynamoDB + CloudWatch**

### AWS IoT Core
Receives sensor telemetry through authenticated MQTT/TLS communication.

MQTT topic: `shrike/flight/telemetry`

### Amazon DynamoDB
Stores sensor readings with device identification and cloud-generated timestamps.

Table: `ShrikeFlightTelemetry`

Partition key: `device_id`

Sort key: `received_at_ms`

### Amazon CloudWatch
Records telemetry and supports historical visualization through Logs Insights.

Log group: `ShrikeFlightLogs`

Dashboard: `ShrikeFlightDashboard`

### AWS IoT Rules
The `ShrikeTelemetryToDynamoDB` rule forwards incoming telemetry to DynamoDB and CloudWatch Logs.

## Telemetry Fields

The integrated firmware publishes:

- Device identification and flight state
- G-force (`g`)
- Relative altitude (`altitude_m`)
- Maximum altitude (`max_altitude_m`)
- Vertical velocity (`vertical_velocity_mps`)
- Three-axis gyroscope measurements (`gyro_dps`)
- Flight duration and peak G-force
- Device uptime (`sample_ms`)

The AWS IoT Rule adds `device_id` and `received_at_ms`. The latter is the cloud-side reception timestamp.

## Results and Validation

The following capabilities have been demonstrated:

- Successful ESP32-S3 Wi-Fi connectivity
- Secure communication with AWS IoT Core
- Real hardware telemetry received through MQTT
- Changing sensor values during physical movement
- Continued local sensor processing without Wi-Fi
- Persistent records written to DynamoDB
- Successful historical data queries
- CloudWatch log ingestion and visualization
- Three saved graphs: altitude, G-force, and vertical velocity versus time

The original embedded flight-computer firmware previously demonstrated the six-state sequence:

**IDLE → LAUNCH → ASCENT → APOGEE → DESCENT → LANDED**

The complete state sequence and automatic reconnection behavior have not yet been revalidated in the final AWS-integrated firmware.

## Smart Agriculture Extension

The existing cloud telemetry pipeline can be adapted to agricultural monitoring.

The proposed extension includes:

1. Collecting soil moisture data through a suitable sensor or API.
2. Retrieving seven-day local weather forecasts using OpenWeatherMap or Tomorrow.io.
3. Processing sensor and forecast information using AWS Lambda.
4. Exposing application functionality through Amazon API Gateway.
5. Storing environmental measurements in DynamoDB.
6. Sending irrigation alerts through Amazon SNS when defined decision conditions are satisfied.

**Implementation boundary:** These agricultural features are proposed and are not part of the currently demonstrated system.

## Setup Overview

1. Install Arduino IDE and ESP32 board support.
2. Connect the MPU6050 and BMP280 to the Shrike-Fi board.
3. Install the firmware's required Arduino libraries.
4. Configure local Wi-Fi and AWS IoT credentials in a private `secrets.h` file.
5. Upload the firmware to the ESP32-S3.
6. Configure an AWS IoT Thing, X.509 certificate, and appropriate IoT policy.
7. Configure the MQTT rule, DynamoDB table, and CloudWatch log group.
8. Verify telemetry through the MQTT test client and cloud services.

AWS resources may incur usage charges.

## Security

AWS certificates, private keys, Wi-Fi passwords, and `secrets.h` must never be committed to the public repository.

Use appropriately restricted IoT policies and IAM permissions.

## Conclusion

ShrikeSense demonstrates an operational hardware-to-cloud telemetry pipeline combining embedded sensor processing with secure AWS communication, persistent data storage, and graphical monitoring.

The prototype provides a foundation for a more complete agricultural environmental monitoring system incorporating soil moisture sensing, weather forecasts, and automated irrigation notifications.

## Project Status

**Working cloud-connected IoT telemetry prototype. Agricultural monitoring and alerting extensions are proposed for future implementation.**

Developed as an academic CAD project.
