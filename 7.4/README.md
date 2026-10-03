# IPFire Mini Appliance 2019 by Zabbix Agent Active

## Description

This template gathers statistics for the [IPFire Mini Appliance (2019)](https://www.ipfire.org/docs/hardware/lightningwirelabs/mini)
(1st generation).

## Overview

For Zabbix version: 7.4

Supports monitoring of:
* Firmware/BIOS version
* HW Model/Version/Serial number
* CPU temperature

Use in conjunction with a default Template OS Linux-template for 
CPU/Memory/Storage monitoring of the IPFire appliance/instance.

This template was created for:

- IPFire Mini Appliance (2019) - 1st generation

**Warning**: This template will *NOT* work on the 2nd generation IPFire Mini 
Appliance (2025) or any other IPFire appliance.

## Author

Robin Roevens

## Setup

- Install and configure [IPFire addon `zabbix_agentd`](https://www.ipfire.org/docs/addons/zabbix_agentd)
  using Pakfire.
- Install the `firmware-update` package on the IPFire Mini appliance (if not already installed)
- Login to the IPFire Mini appliance SSH console
    - Create a new config file for the zabbix_agentd userparameters:
      ```bash
      vi /etc/zabbix_agentd/zabbix_agentd.d/template_ipfire_mini_appliance_2019.conf
      ```
    - Paste the following content into the file (`i` to enter insert mode in Vi):
      ```ini
      UserParameter=ipfire_appliance.firmware.info,sudo /usr/sbin/firmware-update info | awk -F':' 'BEGIN { ORS = ""; print "{" } { gsub(/^[ \t]+|[ \t]+$/, "", $1); gsub(/^[ \t]+|[ \t]+$/, "", $2); printf "%s\"%s\":\"%s\"", separator, $1, $2; separator = ","; } END { print "}" }' 
      ```
    - Save the file and exit the editor (`:wq`).
    - Edit the sudoers file for the zabbix_agentd user to allow running dmidecode without a password:
      ```bash
      visudo -f /etc/sudoers.d/zabbix_agentd_user
      ```
    - Add the following line (`i` to enter insert mode in Vi):
      ```
      zabbix ALL=(ALL) NOPASSWD: /usr/sbin/firmware-update info
      ```
    - Save the file and exit the editor (`:wq`).

## Zabbix configuration

No specific Zabbix configuration is required.

### Macros used
|Name|Description|Default|
|----|-----------|-------|
|{$CPU.TEMP.WARN} |<p>CPU temperature warning threshold</p>|`70` |
|{$CPU.TEMP.CRIT} |<p>CPU temperature critical threshold</p>|`105` |

## Credits

[IPFire Team](https://www.ipfire.org) for the IPFire distro and for accepting my 
contributions to allow easier/better monitoring using Zabbix Agent.

## Feedback

Please report any issues with the template at https://github.com/RobinR1/zbx-template-ipfire-mini-appliance-2019/issues
