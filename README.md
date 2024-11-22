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
* Username (required) - The username needed to download files from your file server. If no login is required, a blank entry "" is allowed.
* Password (required) - The password required to download files from your file server. If no login is required, a blank entry "" is allowed.
* Hostname (required) - The hostname or IP of your file server
* Transfer Protocol (required) - Which transfer protocol to use to download files from your file server. Allowed values are: scp, ftp, sftp, http, https, tftp.
* Mode (required) - What should the script use to identify the configuration file stored on your file server. Allowed values are: serial_number, mac, hostname, personality, raw.