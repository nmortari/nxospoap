# Cisco NX-OS POAP Script
This is a very thorough rewrite of the original Cisco NX-OS POAP script (https://github.com/datacenter/nexus9000/blob/master/nx-os/poap/poap.py).
This script adds additional functionality and updated commands for NX-OS.

## Supported Functionality:
- Allow a single upgrade from a specific version to a specific version.
- Allow a single upgrade from any version to a specific version.
- Allow a multi-stage upgrade from a specific version or version(s) to a specific version.
- Allow a multi-stage upgrade from any version to a specific version.
- Allow downgrades from any version to a specific version.

## Requirements:
The following items are required for the POAP process:
- A Cisco Nexus switch running NX-OS version 7.0(3)I3(1) or higher.
- A file server that can support one of the following transfer protocols: SCP, FTP, SFTP, HTTP, HTTPS, TFTP.
- The NX-OS image file (or multiple image files) required to perform the ugprade.
- A configuration file for your POAP switch (or POAP switches) to load.
- This Python script to execute the POAP process.

## Instructions For Use:
1. Input values for the options near the top of the script

    * **Username** - The username needed to download files from your file server.
        * If no login is required, a blank entry `""` is allowed.

    * **Password** - The password needed to download files from your file server.
        * If no login is required, a blank entry `""` is allowed.

    * **Hostname** - The hostname or IP of your file server.

    * **Transfer Protocol** - Which transfer protocol to use to download files from your file server.
        * Allowed values are: `scp, ftp, sftp, http, https, tftp`

    * **Mode** - What should the script use to identify the configuration file stored on your file server.
        * Allowed values are: `serial_number, mac, hostname, personality, raw`

    * **Upgrade Path** - The list of NX-OS images required in the upgrade path for your device. Upgrade path information can be found on the Cisco upgrade Matrix page (https://www.cisco.com/c/dam/en/us/td/docs/dcn/tools/nexus-9k3k-issu-matrix/index.html). You can think of this option as: `["Start" -> "End"]`.
        * If you need to install multiple upgrades: `["nxos.9.2.1.bin", "nxos.9.2.4.bin", "nxos.9.3.14.bin", "nxos64-cs.10.3.4a.M.bin"]`
        * If you only need 1 upgrade: `["nxos.9.3.9.bin", "nxos.9.3.10.bin"]`
        * If you want to downgrade: `["nxos.9.2.4.bin"]`

    * **Config Path** - Directory to find the configuration file on your file server.

    * **Upgrade Image Path** - Directory to find the NX-OS image file on your file server.

    * **Required Space** - Set a required amount of space (in MB) that the bootflash must have available before proceeding with the script execution.

    * **HTTPS Require Certificate** - Are HTTPS certificates required to connect to your file server
        * Allowed values are: `True, False`

    * **Require MD5** - Should MD5 sums be verified for any files the script downloads. It is recommended to keep this as True.
        * Allowed values are: `True, False`

    * **Only Allow Versions In Upgrade Path** - Should the script only interact with switches that are running NX-OS versions listed in the upgrade path.
        * Allowed values are: `True, False`
        * This option can be used to allow the script to upgrade switches coming from various NX-OS versions, or to allow downgrades.
        * Example-1: If you have all leaf switches on 9.3.9, and all spine switches on 9.3.10, and you ONLY want to upgrade the spines, you would set this to True and set your upgrade path to start at 9.3.10.
        * Example-2: If you have various switches on 9.2.1, 9.2.3, and 9.2.4, but you want them to be able to join an upgrade path starting at 9.3.1, set this to False.
        * Example-3: If you want to downgrade switches, you would set this to False and enter your downgrade version as the only entry in your upgrade path.

2. After your changes are complete (or any time you make a change to the script), regenerate the MD5 sum for the POAP script with the following command:
```
f=poap_http_multi_upgrade.py ; cat $f | sed '/^#md5sum/d' > $f.md5 ; sed -i "s/^#md5sum=.*/#md5sum=\"$(md5sum $f.md5 | sed 's/ .*//')\"/" $f
```

3. Copy the switch configuration file to your file server so it is available for the POAP script. Your configuration file must be named `conf.<options mode>`.
    * Example: If you selected Serial Number as your mode, your configuration file must be named `conf.SAL1911B05K`
    * A valid configuration file can be found below:
    ```
    !
    ! CONFIGURATION FILE FOR N9K
    !
    ! VARIABLES

    hostname poaptest

    vrf context management
      ip route 0.0.0.0/0 1.1.1.1

    username admin password abc123 role network-admin

    interface mgmt0
      vrf member management
      ip address 1.1.1.2/24
    ```

4. If Require MD5 is set to True, generate an MD5 file for your configuration file. This must be stored in the same folder as your configuration file.
    * MD5 sums can be generated with the following command:
    ```
    md5sum conf.SAL1911B05K > conf.SAL1911B05K.md5
    ```
5. Copy the NX-OS image file(s) to your file server so it is available for the POAP script.

6. If Require MD5 is set to True, generate an MD5 file for your NX-OS image file(s). This must be stored in the same folder as your NX-OS file(s).
    * MD5 sums can be generated with the following command:
    ```
    md5sum nxos.9.3.10.bin > nxos.9.3.10.bin.md5
    ```