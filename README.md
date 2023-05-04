# NZAsgn1
# SAM-D21-DA1 Family MCU I2C/SPI Shared 6-pin Connector

This document describes the pinout configuration for the shared 5-pin connector for I2C and SPI communication using the SAM-D21-DA1 Family MCU.

## Suggested Pinout Configuration

1. VCC
2. GND
3. Data/Signal Pin 1: PA08 (SDA/MOSI)
4. Data/Signal Pin 2: PA09 (SCL/SCK)
5. Data/Signal Pin 3: PA10 (MISO)
6. Data/Signal Pin 4: PA11 (SS)

## Reasoning for Pinout Selection

I2C protocol requires two lines: SDA (Serial Data) and SCL (Serial Clock). SPI protocol requires three lines: MOSI (Master Out Slave In), MISO (Master In Slave Out), and SCK (Serial Clock). In addition, SPI also requires an SS (Slave Select) line for each slave device. In this case, since we have a single slave device, we can use a single SS line. Furthermore, the datasheet states that only some of the pins can be used for I2C protocol.

![B596989B-7B09-437D-8419-419E6D46EF90_1_201_a](https://user-images.githubusercontent.com/29590379/236282771-d3b443d7-75fe-4715-ac83-c77fb5ff77d0.jpeg)

By referring to the SAM-D21-DA1 Family datasheet, we can utilize the SERCOM (Serial Communication Interface) module for both I2C and SPI communication. The SERCOM module can be configured for I2C, SPI, or UART operation, which will allow us to switch between I2C and SPI during runtime.

## Relevant Information in the Datasheet

1. **Page 29**: Section 6.1 (I/O Multiplexing and Considerations) shows that the SERCOM module can be used for I2C and SPI, and the peripheral functions can be multiplexed on different pins.

![04030732-415B-483D-B627-61B957AECD6A_1_201_a](https://user-images.githubusercontent.com/29590379/236287369-494eef1b-c56f-4b5d-963c-accb0a8579a4.jpeg)

## Additional Features Required

### Hardware

- No additional hardware features are required.

### Firmware

- Implement a function to switch between I2C and SPI mode in the SERCOM module during runtime. The function should reconfigure the SERCOM module, pins, and peripherals accordingly.
- Implement a GPIO-based SS control for SPI communication, so you can use the same PA10 pin for MISO and SS functionality.
- Implement separate I2C and SPI read/write functions and call them based on the active communication protocol.
