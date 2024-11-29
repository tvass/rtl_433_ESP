This example demonstrates how to use the `rtl_433_ESP` library with an `ESP32` and a `CC1101` radio
chip to forward data from a `TX141TH-Bv2` sensor (operating at 433 MHz) to the Arduino Cloud.

- Copy `thingProperties.h.dist` -> `thingProperties.h`.
- Add your Wifi configuration, Arduino Cloud credentials, and Arduino Cloud Variables to `thingProperties.h`.
- In `platformio.ini`, as `-DMY_DEVICES` is `true`, make sure the decoder for your radio device is in `signalDecoder.cpp#332`.

```C
#ifndef MY_DEVICES
//
#else
    memcpy(&cfg->devices[0], &lacrosse_tx141x, sizeof(r_device));
#endif
```

- In the main `.ino` file, customize the callback function to suit your requirements. Make sure it reflects `initProperties()`.

```c
void rtl_433_Callback(char* message) {
  DynamicJsonBuffer jsonBuffer2(JSON_MSG_BUFFER);
  JsonObject& RFrtl_433_ESPdata = jsonBuffer2.parseObject(message);
  logJson(RFrtl_433_ESPdata);
  count++;
  // Adapt here if needed
  if (RFrtl_433_ESPdata.containsKey("id") && RFrtl_433_ESPdata["id"] == lacrosseId) {
    if (RFrtl_433_ESPdata.containsKey("temperature_C")) {
        float temp = RFrtl_433_ESPdata["temperature_C"];
        temperature = temp;
    }
    if (RFrtl_433_ESPdata.containsKey("humidity")) {
        float hum = RFrtl_433_ESPdata["humidity"];
        humidity = hum;
    }
  }
}
```

- Build and upload from `VSCode Platform.io`.

```
Building .pio/build/esp32_cc1101/firmware.bin
esptool.py v4.5
Creating esp32 image...
Merged 25 ELF sections
Successfully created esp32 image.
```

- Set the `device ID` value from from Arduino Cloud. We use it to filter signals and record values from a specific sensor.

Additional documentation for this project is available on the [Arduino Project page](https://projecthub.arduino.cc/projects/54bfce5c-5301-4306-80e4-f9a2949e6902/preview).