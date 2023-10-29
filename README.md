# qmicli-modem-handler

## Introduction
`modem_control` is a Bash script designed to interact with and control modem devices. It provides a variety of functionalities including initializing the modem, retrieving device information, managing network connections, and more.

## Features
- Initialize modem with specific settings.
- Retrieve detailed device information.
- Manage network connections.
- Get signal strength and packet statistics.
- Retrieve SIM card information.
- Support for various modem operating modes.
- Consistent and structured JSON output for all functions.

## Constants

- _DEVICE_PATH_: The path to the cdc device of the modem. The path `/dev/cdc-wdm0` is usually default.
- _VENDOR_ID_MODEM_: The vendor ID of the modem. We use this for some reset functions in the script. To get the vendor ID of the modem use `lsusb`. example output: `Bus 001 Device 004: ID 1508:1001 Fibocom Fibocom Modem`

- _DEBUG_: If the value is set to `0` all non-JSON outputs are redirected to the _LOG_FILE_ file. otherwise, the error is also shown on the console. Make sure this is `0` in production if the called application/script relates on JSON parsing.
- _