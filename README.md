# IS25LP128 Arduino Library Documentation

## Overview
This Arduino library provides an interface for communicating with the IS25LP128 flash module over SPI. The library includes functions for initializing the module, entering and exiting QPI mode, reading and writing data, erasing sectors and blocks, and reading the device ID. It also includes basic error handling.

## Class Definition
The library defines a single class, IS25LP128, which represents the flash module.

### Constructor
`IS25LP128(int csPin)`

The constructor takes one parameter, csPin, which is the number of the chip select pin.

### Functions/Methods
- `void begin()`: This method initializes the SPI interface and sets the chip select pin as output. It should be called in the setup function of your Arduino sketch.
- `void enterQPI()`: This method sends the Enter QPI command to the flash module, switching it to QPI mode. It then checks for errors.
- `void exitQPI()`: This method sends the Exit QPI command to the flash module, switching it back to SPI mode. It then checks for errors.
- `void writeEnable()`: This method sends the Write Enable command to the flash module, allowing the next write or erase operation to proceed. It then checks for errors.
- `void writeDisable()`: This method sends the Write Disable command to the flash module, preventing any write or erase operations. It then checks for errors.
- `uint8_t readStatusRegister()`: This method sends the Read Status Register command to the flash module and returns the value of the status register.
- `void writeStatusRegister(uint8_t status)`: This method sends the Write Status Register command to the flash module, followed by the new value of the status register. It then checks for errors.
- `void writeByte(uint32_t addr, uint8_t data)`: This method writes a byte of data to a specific address in the flash memory. It sends the Write Enable command, followed by the Page Program command, the address, and the data. It then waits for the write operation to finish, checks for errors, verifies the written data, and sends the Write Disable command. If an error occurs or the data doesn't match, it retries the operation up to 3 times.
- `uint8_t readByte(uint32_t addr)`: This method reads a byte of data from a specific address in the flash memory. It sends the Read Data command, followed by the address, and then reads the data. It then checks for errors.
- `void eraseSector(uint32_t addr)`: This method erases a sector of the flash memory. It sends the Write Enable command, followed by the Sector Erase command and the address of the sector. It then waits for the erase operation to finish, checks for errors, and sends the Write Disable command.
- `void eraseBlock32K(uint32_t addr)`: This method erases a 32K block of the flash memory. It sends the Write Enable command, followed by the 32K Block Erase command and the address of the block. It then waits for the erase operation to finish, checks for errors, and sends the Write Disable command.
- `void eraseBlock64K(uint32_t addr)`: This method erases a 64K block of the flash memory. It sends the Write Enable command, followed by the 64K Block Erase command and the address of the block. It then waits for the erase operation to finish, checks for errors, and sends the Write Disable command.
- `void eraseChip()`: This method erases```markdown
the entire flash memory. It sends the Write Enable command, followed by the Chip Erase command. It then waits for the erase operation to finish, checks for errors, and sends the Write Disable command.
- `uint8_t readDeviceID()`: This method reads the device ID from the flash module. It sends the Read Device ID command and then reads the ID. It then checks for errors.

## Usage
To use this library, include the header file at the beginning of your Arduino sketch, create an instance of the IS25LP128 class, and call the begin method in the setup function. Then you can call the other methods as needed to interact with the flash module.

```cpp
#include "IS25LP128.h"

IS25LP128 flash(10); // Chip select pin is 10

void setup() {
  flash.begin();
  // Other setup code...
}

void loop() {
  // Use flash methods as needed...
}
```

## Error Handling
The library includes sophisticated error handling. After each operation, it checks the status register for errors. If an error is detected, it handles the error by resetting the device.

The error handling mechanism works as follows:
1. After each operation, the `checkError` method is called. This method reads the status register of the flash module.
2. If bit 5 of the status register is set, this indicates an error. In response, the `checkError` method calls the `resetDevice` method.
3. The `resetDevice` method sends the Enable Reset and Reset Device commands to the flash module, effectively resetting the device and clearing the error.

This error handling mechanism ensures that the flash module is always in a known good state after each operation, even if an error occurs. However, it does not provide any information about the nature of the error or any potential data loss. If your application requires more detailed error information or data recovery, you may need to extend the error handling mechanism.

The `writeByte` function includes additional error handling and data verification. After writing data to the flash memory, it reads back the data and checks if it matches the original data. If it doesn't match, this indicates an error, and the function retries the operation up to 3 times. If the operation still fails after 3 attempts, the function handles the error.

## Limitations
While this library provides a comprehensive set of functions for basic operations with the IS25LP128 flash module, there are still some limitations:
1. Data Verification: The library currently only verifies data for byte write operations. For operations that write larger amounts of data, such as page or block writes, the library does not currently verify that the data was written correctly.
2. Error Recovery: The library's error recovery mechanism is basic. It resets the device in the event of an error and retries write operations up to three times. However, it does not provide any mechanisms for recovering lost or corrupted data. If your application requires more robust data recovery, you may need to extend the library.
3. Advanced Features: The library does not support all the features of the IS25LP128 flash module. For example, it does not support the One Time Program (OTP) area, the security registers, or the SFDP register. If you need to use these features, you may need to extend the library.
4. Performance: The library does not currently support any performance optimizations, such as using the Fast Read command for read operations or using the QPI mode for faster data transfer. If performance is a critical factor for your application, you may need to optimize the library.

## Future Considerations
Future versions of this library could include support for more advanced features of the IS25LP128 flash module. They could also include more robust error handling and possibly a higher-level interface for easier use. In addition, future versions of the library could include functions for reading and writing larger amounts of data, such as entire pages or blocks. This would improve the efficiency of the library for applications that need to transfer large amounts of data.
