# QMICLI Modem Handler

## Introduction
The `modem_control` Bash script is meticulously designed to facilitate interactions with and manage modem devices efficiently. It encompasses a wide array of features, from initializing the modem to managing network connections, retrieving device-specific information, and much more.

## Features
- **Modem Initialization**: Configure the modem with tailored settings to suit specific requirements.
- **Device Information Retrieval**: Obtain comprehensive details about the device to have a better understanding of its characteristics and status.
- **Network Connection Management**: Take control of network connections, ensuring reliable and stable connectivity.
- **Signal Strength and Packet Statistics**: Monitor the signal strength and gather packet statistics to optimize performance and troubleshoot issues.
- **SIM Card Information**: Access vital SIM card details.
- **Operating Modes Support**: Offers versatility by supporting various modem operating modes, enhancing compatibility and functionality.
- **Structured JSON Output**: Ensures consistency and structure in the JSON output across all functions, facilitating easier parsing and integration.

## Configuration Constants
Adjust the following constants according to the specific needs of your setup.

- **_DEVICE_PATH_**: Specifies the path to the cdc device of the modem. By default, this is typically set to `/dev/cdc-wdm0`.
- **_VENDOR_ID_MODEM_**: Defines the vendor ID of the modem, which is utilized in certain reset functions within the script. To ascertain the vendor ID of your modem, you can use the `lsusb` command. For example, the output `Bus 001 Device 004: ID 1508:1001 Fibocom Fibocom Modem` indicates that the vendor ID is `1508`.
- **_DEBUG_**: When set to `0`, all non-JSON outputs are redirected to the _LOG_FILE_. Conversely, if set to any other value, errors will also be displayed on the console. For production environments, especially when the called application or script relies on JSON parsing, it is imperative to ensure that this is set to `0`.
- **_DEBUG_LOG_AS_JSON_**: Currently, this feature is not implemented, as it has not found a use case in production environments.

By adhering to these guidelines and making the necessary adjustments to the constants, you can tailor the `modem_control` script to meet the unique requirements of your setup, ensuring optimal performance and functionality.

## Usage

### Command Result Response

Every command executed by the script provides a JSON-formatted response containing the following objects:

- **state**: An object that indicates the status of the command execution.
  - If the command is executed successfully, `state` is set to `"success"`.
  - In case of an error, `state` is set to `"error"`, and a descriptive error message is provided.
- **value**: An object that holds the result of the command. In case of an error, this is set to an empty object.

Example of a successful command execution:

```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {}
}
```

### Initialize modem

The modem can be initialized in two different ways: using standalone mode or by employing a configuration file in JSON format. The standalone mode allows for direct argument passing to the script, while the configuration file enables persistent settings.

#### Standalone mode:

To initialize the modem in standalone mode, you can execute the script with the required arguments as shown in the example below:

```bash
./modem_control -init-modem --mode=standalone --apn=wsim --no-roaming=no --auth=NONE
```