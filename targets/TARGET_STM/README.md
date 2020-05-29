# README for mbed-os STM32 targets

## ST TOOLS

### USB drivers

Mandatory: get the latest USB driver in order to make available all the USB interfaces provided by the ST-LINK:
- ST Debug
- Virtual COM port
- ST Bridge interfaces

https://www.st.com/en/development-tools/stsw-link009.html

### ST-Link FW

Mandatory: get the latest ST-Link Firmware:

https://www.st.com/en/development-tools/stsw-link007.html

Note that with the latest FW version, you are able to check the version number easily with a simple "mbedls":

```
$ mbedls
| platform_name       | platform_name_unique   | mount_point | serial_port | target_id                | interface_version |
|---------------------|------------------------|-------------|-------------|--------------------------|-------------------|
| DISCO_H747I         | DISCO_H747I[0]         | D:          | COM13       | 081402210D03E72132477E08 | V3J7M2            |
| DISCO_L475VG_IOT01A | DISCO_L475VG_IOT01A[0] | E:          | COM9        | 07640221683B630A577FF553 | V2J37M26          |
```

### STM32 Cube

ST drivers are provided by what we called STM32Cube.

This is available in `st.com` page:
https://www.st.com/en/embedded-software/stm32cube-mcu-packages.html

Or in GitHub

This table summarizes the STM32Cube versions currently used in mbed-os master branch :

| STM32 Serie | Cube version | GitHub source                                     |
|-------------|--------------|---------------------------------------------------|
| F0          |    1.9.0     | https://github.com/STMicroelectronics/STM32CubeF0 |
| F1          |    1.6.1     | https://github.com/STMicroelectronics/STM32CubeF1 |
| F2          |    1.6.0     | https://github.com/STMicroelectronics/STM32CubeF2 |
| F3          |    1.9.0     | https://github.com/STMicroelectronics/STM32CubeF3 |
| F4          |    1.19.0    | https://github.com/STMicroelectronics/STM32CubeF4 |
| F7          |    1.16.0    | https://github.com/STMicroelectronics/STM32CubeF7 |
| G0          |    1.3.0     | https://github.com/STMicroelectronics/STM32CubeG0 |
| H7          |    1.4.0     | https://github.com/STMicroelectronics/STM32CubeH7 |
| L0          |    1.10.0    | https://github.com/STMicroelectronics/STM32CubeL0 |
| L1          |    1.8.1     | https://github.com/STMicroelectronics/STM32CubeL1 |
| L4          |    1.11.0    | https://github.com/STMicroelectronics/STM32CubeL4 |
| L5          |    1.1.0     | https://github.com/STMicroelectronics/STM32CubeL5 |
| WB          |    1.4.0     | https://github.com/STMicroelectronics/STM32CubeWB |


Cube driver files are copied in each targets/TARGET_STM/TARGET_STM32<xx>/STM32Cube_FW/ directory.

In mbed repo, we try to minimize the difference between "official" and copied files.


### STM32CubeMX

STM32CubeMX is a graphical tool that allows a very easy configuration of all STM32

https://www.st.com/en/development-tools/stm32cubemx.html

Tool is not used in mbed-os, but it can be useful for you.


### STM32CubeProgrammer

It provides an easy-to-use and efficient environment for reading, writing and verifying device memory.

https://www.st.com/en/development-tools/stm32cubeprog.html

Tool is not used in mbed-os, but it can be useful for you.



## Custom boards

It should be "easy" to add your custom board with a STM32 MCU in mbed-os

### STM32 organisation

STM32 root directory is https://github.com/ARMmbed/mbed-os/tree/master/targets/TARGET_STM

This contains:
- all STM32 families directories: F0, F1, F2, F3, F4, F7, G0, H7, L0, L1, L4, WB
- MBED porting layer common for all

Each STM32 family contains several "sub-families".

All STM32 for the same "sub-family" share the same FLASH size.

MBED porting layer specific for this family are placed here.

Example in TARGET_STM32G0:
- TARGET_STM32G031xx
- TARGET_STM32G071xx
- ...

Each STM32 sub-family contains:
- toolchains files
- board specific files


### Add a custom board

ST provides the complete support for few NUCLEO and DISCO boards.

Locate one of these boards with the minimum difference with your chosen MCU.

Copy paste, and update!


### Board specific files (pinmap)

2 files in mbed:
- PeripheralPins.c
- PinNames.h

Tip is to use a python script:

https://github.com/ARMmbed/mbed-os/blob/master/tools/targets/STM32_gen_PeripheralPins.py

STM32CubeMX has to be installed first. Path has to be specified in the `cube_path.json` file.

```
$ python tools/targets/STM32_gen_PeripheralPins.py -h

Script version 1.9
usage: STM32_gen_PeripheralPins.py [-h] [-l | -b | -m xml | -t HW | -c CUSTOM]

Script will generate PeripheralPins.c thanks to the xml files description available in
STM32CubeMX directory defined in 'cube_path.json':
        C:\Program Files\STMicroelectronics\STM32Cube\STM32CubeMX

optional arguments:
  -h, --help            show this help message and exit
  -l, --list            list available mcu xml files description in STM32CubeMX
  -b, --boards          list available boards description in STM32CubeMX
  -m xml, --mcu xml     specify the mcu xml file description in STM32CubeMX to use (use double quotes).
                           Parameter can be a filter like L496 if you want to parse all L496 chips (-m STM32 to parse all).
  -t HW, --target HW    specify the board file description in STM32CubeMX to use (use double quotes).
                           Parameter can be a filter like L496 (only the first file found will be parsed).
  -c CUSTOM, --custom CUSTOM
                        specify a custom board .ioc file description to use (use double quotes).

Once generated, you have to check and comment pins that can not be used (specific HW, internal ADC channels, remove PWM using us ticker timer, ...)
```

How to generate files for a custom boards based on a STM32F427 MCU:
```
$ python tools/targets/STM32_gen_PeripheralPins.py -l | grep F427
STM32F427A(G-I)Hx.xml
STM32F427I(G-I)Hx.xml
STM32F427I(G-I)Tx.xml
STM32F427V(G-I)Tx.xml
STM32F427Z(G-I)Tx.xml

$ python tools/targets/STM32_gen_PeripheralPins.py -m "STM32F427V(G-I)Tx.xml"

Script version 1.9
CubeMX DB version DB.5.0.60

 * Output directory: C:\github\mbed\STM32F427V(G-I)Tx
 * Generating PeripheralPins.c and PinNames.h with 'STM32F427V(G-I)Tx.xml'
 * GPIO file: C:\Program Files\STMicroelectronics\STM32Cube\STM32CubeMX\db\mcu\IP\GPIO-STM32F427_gpio_v1_0_Modes.xml
 * I/O pins found: 82 connected: 0
```

### Use of custom_targets.json

https://os.mbed.com/docs/mbed-os/v6.0-preview/porting/porting-a-custom-board.html

Example with a board based on STM32F103C8 (like BluePill):
- MCU_STM32F103x8 generic configuration is already available in targets.json file

```
$ python tools/targets/STM32_gen_PeripheralPins.py -m "STM32F103C(8-B)Tx.xml"
// PeripheralPins.c and PinNames.h template files are created in STM32H745ZITx directory

$ mv STM32F103C\(8-B\)Tx/ TARGET_BLUEPILL_F103C8
// Edit PeripheralPins.c and PinNames.h to match your board configuration

// Create a custom_targets.json with:
{
    "BLUEPILL_F103C8": {
        "inherits": [
            "MCU_STM32F103x8"
        ],
        "overrides": {
            "clock_source": "USE_PLL_HSE_XTAL"
        },
        "device_has_remove": [
            "STDIO_MESSAGES"
        ],
        "device_name": "STM32F103C8"
    }
}
```

Example with a board based on STM32H745ZI
- this is dual core MCU with CM4 and CM7
- MCU_STM32H745I_CM4 and MCU_STM32H745I_CM7 generic configuration is already available in targets.json file

```
$ python tools/targets/STM32_gen_PeripheralPins.py -m "STM32H745ZITx.xml"
// PeripheralPins.c and PinNames.h template files are created in STM32H745ZITx directory

$ mv STM32H745ZITx TARGET_H745ZI_BOARD
// Edit PeripheralPins.c and PinNames.h to match your board configuration

// Create a custom_targets.json with:
{
    "H745ZI_BOARD_CM4": {
        "inherits": [
            "MCU_STM32H745I_CM4"
        ],
        "extra_labels_add": [
            "H745ZI_BOARD"
        ]
    },
    "H745ZI_BOARD_CM7": {
        "inherits": [
            "MCU_STM32H745I_CM7"
        ],
        "extra_labels_add": [
            "H745ZI_BOARD"
        ]
    }
}
```


## ST specific implementation

### WiFi configuration

https://github.com/ARMmbed/wifi-ism43362

https://os.mbed.com/teams/ST/wiki/How-to-make-wifi-tests


### Ethernet configuration

Depending on your PHY, you will have to customize several configuration values:
- the number or RX buffer
- the number or TX buffer
- thread size
- PHY address
- media interface
- AutoNegotiation mode
- DuplexMode mode
- Speed mode
- PHY register Offset
- Speed mask information in the PHY status register
- Duplex mask information in the PHY status register

Check the default values in:
https://github.com/ARMmbed/mbed-os/blob/master/features/netsocket/emac-drivers/TARGET_STM/mbed_lib.json

Option is also to define your own `HAL_ETH_MspInit` function,
you then have to add **USE_USER_DEFINED_HAL_ETH_MSPINIT** macro.



## MBED Wiki pages

https://os.mbed.com/teams/ST/wiki/

https://os.mbed.com/teams/ST/wiki/How-to-enable-flash-dual-bank

https://os.mbed.com/teams/ST/wiki/Nucleo-144pins-ethernet-spi-conflict
