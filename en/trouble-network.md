# OpenIPC Wiki
[Table of Content](../index.md)

## Troubleshooting: Network does not work

Some IP-cameras require additional parameters set to make the network interface work.

#### For recent firmware

Use `extras` boot variable. In Linux console run this
```
fw_setenv extras 'hieth.phyaddru=0 hieth.phyaddrd=1' && reboot
```
If the above settings did not work, try this instead
```
fw_setenv extras 'hieth.phyaddru=1 hieth.phyaddrd=0' && reboot
```
If the above settings did not work, try this instead
```
fw_setenv extras 'hieth.mdioifu=0 hieth.mdioifd=0' && reboot
```
If the above settings did not work, try this instead
```
fw_setenv extras 'hieth.mdioifu=1 hieth.mdioifd=1' && reboot
```
If nothing of the above worked for you, seek help on [our Telegram channel](https://t.me/openipc).


#### For older firmware

Set `phyaddru` and `phyaddrd` variables from U-Boot console:
```
setenv phyaddru 0; setenv phyaddrd 1; saveenv; reset
```
If the above settings did not work, try this instead
```
setenv phyaddru 1; setenv phyaddrd 0; saveenv; reset
```
If the above settings did not work, try this instead
```
setenv phyaddru 0; setenv phyaddrd 0; saveenv; reset
```
If the above settings did not work, try this instead
```
setenv phyaddru 1; setenv phyaddrd 1; saveenv; reset
```

#### For the hi3518ev100
Set `phyaddru` and `phyaddrd` variables from U-Boot console:
    setenv phyaddru 1
    setenv phyaddrd 2
    setenv mdio_intf rmii
    saveenv

From Linux console:
    setenv extras 'hieth.phyaddru=1 hieth.phyaddrd=2'; saveenv

If nothing of the above worked for you, seek help on [our Telegram channel](https://t.me/openipc).
