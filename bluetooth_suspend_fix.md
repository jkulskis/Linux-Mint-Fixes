I made these changes to fix my bluetooth problems. The first section has a fix to keep my PC from waking up right after a suspend when bluetooth is on, while still keeping my blutooth adapter keyboard and mouse enabled. The second section has a fix to reconnect to my bluetooth speaker (or any other connected devices) after a suspend or restart.

## Bluetooth will wake a suspend: ##
*Slightly modified from https://forum.manjaro.org/t/laptop-wakes-from-sleep-if-bluetooth-is-enabled/50647/41*
#### You’ll need to create four script files: ####

##### /etc/systemd/system/bluetooth-reload.service #####
```
[Unit]
Description=Reload bluetooth driver after system resume
After=hibernate.target suspend.target hybrid-sleep.target

[Service]
Type=simple
ExecStart=-/usr/bin/bluetooth-enable.service

[Install]
WantedBy=hibernate.target 
WantedBy=suspend.target
WantedBy=hybrid-sleep.target
```
##### /etc/systemd/system/bluetooth-suspend.service #####
```
[Unit]
Description=Bluetooth module suspend helper
Before=sleep.target
WantedBy=hibernate.target
WantedBy=hybrid-sleep.target

[Service]
Type=simple
ExecStart=-/usr/bin/bluetooth-suspend.sh

[Install]
WantedBy=sleep.target
WantedBy=hibernate.target
WantedBy=hybrid-sleep.target
```

##### /usr/bin/bluetooth-suspend.sh #####
```sh
#!/bin/bash
modprobe -r btusb
```

##### /usr/bin/bluetooth-enable.sh #####
```sh
#!/bin/bash
modprobe btusb
```

#### Next, you’ll need to run the following list of commands. ####

```sh
sudo chmod +x /etc/systemd/system/bluetooth-reload.service
sudo chown root:root /etc/systemd/system/bluetooth-reload.service
sudo chmod +x /etc/systemd/system/bluetooth-suspend.service
sudo chown root:root /etc/systemd/system/bluetooth-suspend.service
sudo chmod +x /usr/bin/bluetooth-suspend.sh
sudo chown root:root /usr/bin/bluetooth-suspend.sh
sudo chmod +x /usr/bin/bluetooth-enable.sh
sudo chown root:root /usr/bin/bluetooth-enable.sh
sudo systemctl enable bluetooth-suspend.service
sudo systemctl start bluetooth-suspend.service
sudo systemctl enable bluetooth-reload.service
sudo systemctl start bluetooth-reload.service
```

## Bluetooth does not automatically reconnect: ##
*From https://unix.stackexchange.com/questions/299128/bluetooth-mouse-does-not-automatically-reconnect-on-reboot*

#### Edit /etc/bluetooth/input.conf and remove the # commenting out the line so the line ####

`#UserspaceHID=true`

now reads:

`UserspaceHID=true`
