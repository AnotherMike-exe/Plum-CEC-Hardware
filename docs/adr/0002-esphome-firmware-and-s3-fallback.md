# ESPHome firmware, with the ESP32-S3 as the fallback MCU

The firmware is an ESPHome configuration (see Plum-CEC-Firmware
`docs/adr/0002-esphome-external-component.md`). If the native USB spike or test T1 fails
on the ESP32-C3, the hardware changes to the ESP32-S3, not the firmware platform. Keep
the bench wiring and a future PCB easy to change to an S3 module.
