# Video via USB to Raspi

Source: https://medium.com/propelland/raspberry-pi-tutorial-on-using-a-usb-camera-to-display-and-record-videos-with-python-a41c6938f89f

The plan is to decode it here and send via MQTT to ESPs and from there to LEDs.


===

ISSUES

USB video input device (MiraBox HDMI Splitter) disconnects randomly after a few seconds

Output from ``cat /var/log/syslog``:
* First line appears after resetting the USB device via ``sudo usbreset 534d:2109``
    * 534d:2109 is the device id. Can be found out via ``lsusb``
```
Oct  3 22:41:21 snipsign kernel: [11112.640413] usb 1-1.2: Found UVC 1.00 device MiraBox Capture (534d:2109)
Oct  3 22:41:47 snipsign kernel: [11138.089341] usb usb2-port1: over-current change #26
Oct  3 22:41:47 snipsign kernel: [11138.108880] usb 1-1-port1: over-current change #26
Oct  3 22:41:47 snipsign kernel: [11138.305247] usb usb2-port2: over-current change #26
Oct  3 22:41:47 snipsign kernel: [11138.329691] usb 1-1-port2: over-current change #26
Oct  3 22:41:47 snipsign kernel: [11138.521262] usb usb2-port3: over-current change #26
Oct  3 22:41:47 snipsign kernel: [11138.549676] usb 1-1-port3: over-current change #26
Oct  3 22:41:47 snipsign kernel: [11138.737258] usb usb2-port4: over-current change #26
Oct  3 22:41:47 snipsign kernel: [11138.770014] usb 1-1-port4: over-current change #26
Oct  3 22:41:47 snipsign kernel: [11138.989813] usb 1-1.4: USB disconnect, device number 123
Oct  3 22:41:48 snipsign kernel: [11139.301091] usb 1-1.4: new full-speed USB device number 125 using xhci_hcd
Oct  3 22:41:48 snipsign kernel: [11139.405570] usb 1-1.4: New USB device found, idVendor=1a86, idProduct=e2e3, bcdDevice= 0.00
Oct  3 22:41:48 snipsign kernel: [11139.405591] usb 1-1.4: New USB device strings: Mfr=1, Product=2, SerialNumber=0
Oct  3 22:41:48 snipsign kernel: [11139.405598] usb 1-1.4: Product: USB2IIC_CTP_CONTROL
Oct  3 22:41:48 snipsign kernel: [11139.405603] usb 1-1.4: Manufacturer: wch.cn
Oct  3 22:41:48 snipsign kernel: [11139.422795] input: wch.cn USB2IIC_CTP_CONTROL as /devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.4/1-1.4:1.0/0003:1A86:E2E3.007B/input/input175
Oct  3 22:41:48 snipsign kernel: [11139.423123] input: wch.cn USB2IIC_CTP_CONTROL UNKNOWN as /devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.4/1-1.4:1.0/0003:1A86:E2E3.007B/input/input176
Oct  3 22:41:48 snipsign kernel: [11139.423445] hid-multitouch 0003:1A86:E2E3.007B: input,hiddev97,hidraw1: USB HID v1.00 Device [wch.cn USB2IIC_CTP_CONTROL] on usb-0000:01:00.0-1.4/input0
Oct  3 22:41:48 snipsign kernel: [11139.424087] usb 1-1.2: USB disconnect, device number 124
Oct  3 22:41:48 snipsign mtp-probe: checking bus 1, device 125: "/sys/devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.4"
Oct  3 22:41:48 snipsign mtp-probe: bus: 1, device: 125 was not an MTP device
Oct  3 22:41:48 snipsign systemd-udevd[85884]: mouse0: Process '/usr/sbin/th-cmd --socket /var/run/thd.socket --passfd --udev' failed with exit code 1.
Oct  3 22:41:48 snipsign kernel: [11139.665119] usb 1-1.2: new high-speed USB device number 126 using xhci_hcd
Oct  3 22:41:48 snipsign systemd-udevd[85893]: event3: Process '/usr/sbin/th-cmd --socket /var/run/thd.socket --passfd --udev' failed with exit code 1.
Oct  3 22:41:48 snipsign systemd-udevd[85892]: event2: Process '/usr/sbin/th-cmd --socket /var/run/thd.socket --passfd --udev' failed with exit code 1.
Oct  3 22:41:48 snipsign mtp-probe: checking bus 1, device 125: "/sys/devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.4"
Oct  3 22:41:48 snipsign mtp-probe: bus: 1, device: 125 was not an MTP device
Oct  3 22:41:48 snipsign kernel: [11139.772499] usb 1-1.2: New USB device found, idVendor=534d, idProduct=2109, bcdDevice=21.00
Oct  3 22:41:48 snipsign kernel: [11139.772522] usb 1-1.2: New USB device strings: Mfr=1, Product=2, SerialNumber=0
Oct  3 22:41:48 snipsign kernel: [11139.772528] usb 1-1.2: Product: MiraBox Capture
Oct  3 22:41:48 snipsign kernel: [11139.772533] usb 1-1.2: Manufacturer: MACROSILICON
Oct  3 22:41:48 snipsign kernel: [11139.775627] usb 1-1.2: Found UVC 1.00 device MiraBox Capture (534d:2109)
Oct  3 22:41:48 snipsign kernel: [11139.781506] hid-generic 0003:534D:2109.007C: hiddev96,hidraw0: USB HID v1.10 Device [MACROSILICON MiraBox Capture] on usb-0000:01:00.0-1.2/input4
Oct  3 22:41:48 snipsign mtp-probe: checking bus 1, device 126: "/sys/devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.2"
Oct  3 22:41:48 snipsign mtp-probe: bus: 1, device: 126 was not an MTP device
Oct  3 22:41:48 snipsign mtp-probe: checking bus 1, device 126: "/sys/devices/platform/scb/fd500000.pcie/pci0000:00/0000:00:00.0/0000:01:00.0/usb1/1-1/1-1.2"
Oct  3 22:41:48 snipsign mtp-probe: bus: 1, device: 126 was not an MTP device
nik@snipsign:~/usbcamera $ 
```

lsusb:
```
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 125: ID 1a86:e2e3 QinHeng Electronics USB2IIC_CTP_CONTROL
Bus 001 Device 126: ID 534d:2109 MacroSilicon MiraBox Capture
Bus 001 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```