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

- Implement a function to switch between I2C and SPI mode in the SERCOM module during runtime. The function should reconfigure the SERCOM module, pins, and peripherals accordingly. The following images from datasheet show some of the initialization steps and changes which need to be done.

Mode select for SERCOM module for switching between SPI and I2C:

![6CAFB314-AD33-4A6D-AA7F-F099C8BFFCF0_1_201_a](https://user-images.githubusercontent.com/29590379/236302241-6f71322c-65ba-46f2-8dc7-1c7f71d902b5.jpeg)

SPI initialization first steps:

![9CBA7762-86C0-45D4-B5F6-CC4AB89BF940_1_201_a](https://user-images.githubusercontent.com/29590379/236306823-f49856e3-7cfb-467f-916b-00ff71338d6e.jpeg)

![41E744E6-5BE7-44B3-A483-FABF375251B7_1_105_c](https://user-images.githubusercontent.com/29590379/236303130-ff4255c4-6b28-44d1-8961-124e38a4f8af.jpeg)

I2C initialization first steps:

![FDFDB77B-0108-4F68-8BCA-50CD238229E7_1_201_a](https://user-images.githubusercontent.com/29590379/236307742-4a638694-96ce-4eda-bb6b-d6269566f853.jpeg)

![C4F8B65F-F2DA-4215-BCCA-C973DFD8A3C5_1_201_a](https://user-images.githubusercontent.com/29590379/236308253-d3a1d86f-bc68-4eea-9bbd-d49ad3c7b819.jpeg)

- Implement separate I2C and SPI read/write functions and call them based on the active communication protocol.

## Steps for the firmware to switch between I2C and SPI protocols

### Start: 
Initialize the MCU, configure the GPIOs, and set up the peripherals.

Detect Device: Check the connected slave device type to determine the protocol to use (I2C or SPI). (explained in the following section)

Configure SERCOM: Based on the detected device type, configure the SERCOM module for either I2C or SPI mode.

1. If the detected device is an I2C device, go to state I2C Mode.

2. If the detected device is an SPI device, go to state SPI Mode.

I2C Mode:

1. Perform I2C operations (read/write) as required.

2. Continuously monitor for a change in the connected device type.

3. If a change in device type is detected, go to state Configure SERCOM.

SPI Mode:

1. Perform SPI operations (read/write) as required.

2. Continuously monitor for a change in the connected device type.

3. If a change in device type is detected, go to state Configure SERCOM.

### Check the connected slave device type to determine the protocol to use

To check the connected slave device type to determine the protocol to use (I2C or SPI), you can implement one of the following methods:

1. Use an additional GPIO for device type indication: 

Connect an extra GPIO line to the slave device, which indicates the type of protocol the device uses. Set this line to HIGH for I2C devices and LOW for SPI devices (or vice versa, based on your preference). In the firmware, read the state of this GPIO line to determine the device type.

2. Use a predefined timing or connection sequence: 

If you can control the connection sequence or timing of the devices, you can use this information to determine the device type. For example, if you connect an I2C device first and then an SPI device, you can use this sequence to determine which protocol to use. Alternatively, you can use a timer to switch between the two protocols at predefined intervals.

3. Manual user input: 

If the system allows for user interaction, you can prompt the user to select the protocol type when a new device is connected. The user can input the device type through a button, switch, or software interface.
