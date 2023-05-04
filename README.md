# NZAsgn1
# SAM-D21-DA1 Family MCU I2C/SPI Shared 5-pin Connector

This document describes the pinout configuration for the shared 5-pin connector for I2C and SPI communication using the SAM-D21-DA1 Family MCU.

## Pinout Configuration

1. VCC
2. GND
3. Data/Signal Pin 1: PA08 (SDA/MOSI)
4. Data/Signal Pin 2: PA09 (SCL/SCK)
5. Data/Signal Pin 3: PA10 (MISO/SS)

## Reasoning for Pinout Selection

I2C protocol requires two lines: SDA (Serial Data) and SCL (Serial Clock). SPI protocol requires three lines: MOSI (Master Out Slave In), MISO (Master In Slave Out), and SCK (Serial Clock). In addition, SPI also requires an SS (Slave Select) line for each slave device. In this case, since we have a single slave device, we can use a single SS line.

By referring to the SAM-D21-DA1 Family datasheet, we can utilize the SERCOM (Serial Communication Interface) module for both I2C and SPI communication. The SERCOM module can be configured for I2C, SPI, or UART operation, which will allow us to switch between I2C and SPI during runtime.

## Relevant Information in the Datasheet

1. **Page 29**: Section 6.1 (I/O Multiplexing and Considerations) shows that the SERCOM module can be used for I2C and SPI, and the peripheral functions can be multiplexed on different pins.
2. **Page 31**: Table 6-1 (Pinout) provides the pin numbers and their alternate functions.
3. **Page 671**: Section 27.6.2.1 (I/O Lines) describes the possible SERCOM pins for I2C and SPI.
4. **Page 671-672**: Table 27-7 (SERCOM0-7 I/O Lines) gives a mapping of the SERCOM pins to the I2C and SPI lines.

Based on Table 27-7, we can use SERCOM0 and its alternate function (ALT C) to configure the pins PA08, PA09, and PA10 for both I2C and SPI. PA08 can be used for SDA (I2C) and MOSI (SPI), PA09 can be used for SCL (I2C) and SCK (SPI), and PA10 can be used for MISO (SPI) and as an SS line (assuming we use GPIO-based SS control).

## Additional Features Required

### Hardware

- No additional hardware features are required.

### Firmware

- Implement a function to switch between I2C and SPI mode in the SERCOM module during runtime. The function should reconfigure the SERCOM module, pins, and peripherals accordingly.
- Implement a GPIO-based SS control for SPI communication, so you can use the same PA10 pin for MISO and SS functionality.
- Implement separate I2C and SPI read/write functions and call them based on the active communication protocol.
