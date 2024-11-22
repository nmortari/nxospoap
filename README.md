# Cisco NX-OS POAP Script
This is a very thorough rewrite of the original Cisco NX-OS POAP script (https://github.com/datacenter/nexus9000/blob/master/nx-os/poap/poap.py).
This script adds additional functionality and updated commands for NX-OS.

## Supported Functionality:
- Allow a single upgrade from a specified version to a specified version
- Allow a single upgrade from any version to a specified version
- Allow a multi-stage upgrade from a specific version or version(s) to a specific version
- Allow a multi-stage upgrade from any version to a specified version
- Allow downgrades from any version to a specific version

## Requirements:
The following items are required for the POAP process:
- A Cisco Nexus switch running NX-OS version 7.0(3)I3(1) or higher
- A file server that can support one of the following transfer protocols: SCP, FTP, SFTP, HTTP, HTTPS, TFTP
- The NX-OS image file (or multiple image files) required to perform the ugprade
- A configuration file for your POAP switch (or POAP switches) to load

## Instructions For Use:
1. Input values for the options near the top of the script

    * **Username** - The username needed to download files from your file server.
        * If no login is required, a blank entry "" is allowed.

    * **Password** - The password needed to download files from your file server.
        * If no login is required, a blank entry "" is allowed.

    * **Hostname** - The hostname or IP of your file server.

    * **Transfer Protocol** - Which transfer protocol to use to download files from your file server.
        * Allowed values are: scp, ftp, sftp, http, https, tftp.

    * **Mode** - What should the script use to identify the configuration file stored on your file server.
        * Allowed values are: serial_number, mac, hostname, personality, raw.

    * **Upgrade Path** - The list of NX-OS images required in the upgrade path for your device. Upgrade path information can be found on the Cisco upgrade Matrix page (https://www.cisco.com/c/dam/en/us/td/docs/dcn/tools/nexus-9k3k-issu-matrix/index.html). You can think of this option as: ["Start" -> "End"].
        * If you need to install multiple upgrades: ["nxos.9.2.1.bin", "nxos.9.2.4.bin", "nxos.9.3.14.bin", "nxos64-cs.10.3.4a.M.bin"]
        * If you only need 1 upgrade: ["nxos.9.3.9.bin", "nxos.9.3.10.bin"]
        * If you want to downgrade: ["nxos.9.2.4.bin"]

    * **Config Path** - Directory to find the configuration file on your file server.

    * **Upgrade Image Path** - Directory to find the NX-OS image file on your file server.

    * **Required Space** - Set a required amount of space (in MB) that the bootflash must have available before proceeding with the script execution.

    * **HTTPS Require Certificate** - Are HTTPS certificates required to connect to your file server
        * Allowed values are: True, False

    * **Require MD5** - Should MD5 sums be verified for any files the script downloads.
        * Allowed values are: True, False

    * **Only Allow Versions In Upgrade Path** - Should the script only interact with switches that are running NX-OS versions listed in the upgrade path.
        * Allowed values are: True, False
        * This option can be used to allow the script to upgrade switches coming from various NX-OS versions, or to allow downgrades.
        * Example-1: If you have all leaf switches on 9.3.9, and all spine switches on 9.3.10, and you ONLY want to upgrade the spines, you would set this to True and set your upgrade path to start at 9.3.10.
        * Example-2: If you have various switches on 9.2.1, 9.2.3, and 9.2.4, but you want them to be able to join an upgrade path starting at 9.3.1, set this to False.
        * Example-3: If you want to downgrade switches, you would set this to False and enter your downgrade version as the only entry in your upgrade path.