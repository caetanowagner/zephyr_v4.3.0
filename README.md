# Introduction to Zephyr

Using the Introduction to Zephyr course from Shawn Himmel and trying to upgrade to zephyr v4.3.0
Linux/macOS:

```sh
python -m venv venv
source venv/bin/activate
python -m pip install pyserial==3.5 esptool==4.8.1
```

From this directory, build the image (this will take some time):

```sh
docker build -t env-zephyr-espressif -f Dockerfile.espressif .
```

Linux/macOS:

```sh
docker run --rm -it -p 3333:3333 -p 2222:22 -p 8800:8800 -v "$(pwd)"/workspace:/workspace -w /workspace env-zephyr-espressif
```

```
cd apps/01_demo_blink
west build -p always -b esp32s3_devkitc/esp32s3/procpu -- -DDTC_OVERLAY_FILE=boards/esp32s3_devkitc.overlay
```

### Flash Demo Application

> **Important!** make sure to execute flashing and serial monitor programs from your **host OS** (not from within the Docker container)

```sh
python -m esptool --port "<PORT>" --chip auto --baud 921600 --before default_reset --after hard_reset write_flash -u --flash_mode keep --flash_freq 40m --flash_size detect 0x0 workspace/apps/01_blink/build/zephyr/zephyr.bin
```

> **Important**: If you are using Linux and get a `Permission denied` or `Port doesn't exist` error when flashing, you likely need to add your user to the *dialout* group with the following command: `sudo usermod -a -G dialout $USER`. Log out and log back in (or restart). You should then be able to call the *esptool* command again to flash the firmware.

Open a serial port for debugging. Change `<PORT>` to the serial port for your ESP32 board.

```sh
python -m serial.tools.miniterm "<PORT>" 115200
```

Build the image:

```sh
docker build -t env-zephyr-rp2 -f Dockerfile.rp2 .
```

Run the image on Linux/macOS:

```sh
docker run --rm -it -p 3333:3333 -p 2222:22 -p 8800:8800 -v "$(pwd)"/workspace:/workspace -w /workspace env-zephyr-rp2
```

Run the image on Windows (PowerShell):

```sh
docker run --rm -it -p 3333:3333 -p 2222:22 -p 8800:8800 -v "${PWD}\workspace:/workspace" -w /workspace env-zephyr-rp2
```

Connect to the container by following one of the methods given in the Espressif version of [Connect to Container](#connect-to-container).

Build demo application:

```sh
cd apps/01_demo_blink
west build -p always -b rpi_pico -- -DDTC_OVERLAY_FILE=boards/rpi_pico.overlay -DEXTRA_CONF_FILE=boards/rpi_pico.conf
```

Activate the Python virtual environment (Linux/macOS: `source venv/bin/activate`, Windows: `venv\Scripts\activate`) if not done so already. Use miniterm (or your serial terminal of choice):

```sh
python -m serial.tools.miniterm "<PORT>" 115200
```

For more information about configuring the console over USB CDC ACM, see the following resources: 
 * https://docs.zephyrproject.org/latest/connectivity/usb/device/usb_device.html
 * https://docs.zephyrproject.org/latest/samples/subsys/usb/console/README.html


## License

All software in this repository, unless otherwise noted, is licensed under the [Apache-2.0](https://www.apache.org/licenses/LICENSE-2.0) license.


