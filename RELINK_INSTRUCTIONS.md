# Relinking Instructions

This project uses the **ESP32 Arduino Core** and **WebSocketsServer** libraries, which are distributed under the **GNU Lesser General Public License (LGPL-2.1)**. To comply with the LGPL, we provide the **object files** of our application so you can recompile and relink the firmware with your own modified versions of these libraries.

## What you need

- **ESP32 toolchain** (ESP-IDF or PlatformIO)
- **Source code** of the LGPL libraries:
  - [ESP32 Arduino Core](https://github.com/espressif/arduino-esp32) (LGPL-2.1)
  - [WebSocketsServer (arduinoWebSockets)](https://github.com/Links2004/arduinoWebSockets) (LGPL-2.1)
- **Object files** from this repository (provided in the `objects/` folder)
- **esptool.py** to flash the final firmware

## Steps

### 1. Get the source code of the LGPL libraries

git clone https://github.com/espressif/arduino-esp32.git
git clone https://github.com/Links2004/arduinoWebSockets.git

2. Modify the libraries (if needed)

Make your changes to the library source code as you wish.
3. Compile the libraries

Using PlatformIO or ESP-IDF, compile the libraries to produce static .a files.

Example with PlatformIO (assuming you have a project that uses these libraries):
bash

cd arduino-esp32
pio run

The compiled library file will be located at:
text

.pio/build/your_board/libFrameworkArduino.a

For WebSockets, you'll get libWebSockets.a or similar (depending on your build setup).

If you are using ESP-IDF, compile the libraries as components to produce .a files in the build/ directory.
4. Link with the provided object files

We provide the object files of our application (all .o and .a files except those belonging to the LGPL libraries). Use the linker to combine them with your modified libraries.

Example command (adjust paths to your system):
bash

# Assuming you have the object files in ./objects/
ld -r -o combined.o \
    ./objects/*.o \
    /path/to/libFrameworkArduino.a \
    /path/to/libWebSockets.a \
    -T /path/to/esp32.ld \
    -L /path/to/esp32/lib

Alternative with PlatformIO:

If you use PlatformIO, you can place your object files into the lib/ folder of a new project, add the modified LGPL libraries as dependencies, and let PlatformIO link everything automatically. Then you can build the project normally.
5. Convert to final firmware binary

Use esptool.py to convert the linked object file to a .bin:
bash

esptool.py --chip esp32s3 elf2image combined.o -o firmware.bin

Or, if you used PlatformIO, the build process will produce firmware.bin directly.
6. Flash the new firmware
bash

esptool.py --chip esp32s3 --port /dev/ttyUSB0 write_flash 0x10000 firmware.bin

(Replace /dev/ttyUSB0 with your actual serial port.)
Notes

    The exact commands depend on your build environment. Adjust paths and toolchain accordingly.

    If you use PlatformIO, you can also place your object files into the lib/ folder and let PlatformIO link everything automatically.

    For more details, refer to the official ESP-IDF documentation.

    The provided object files were built for ESP32-S3. For other chips, you may need to rebuild them.

Licenses

    Our application code is distributed under the MIT License (see LICENSE).

    The LGPL libraries remain under their respective licenses (LGPL-2.1).
