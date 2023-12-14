# QMICLI Modem Handler

- [QMICLI Modem Handler](#qmicli-modem-handler)
  - [Introduction](#introduction)
  - [Features](#features)
  - [Dependencies](#dependencies)
  - [Configuration Constants](#configuration-constants)
  - [Command Result Response](#command-result-response)
  - [Usage](#usage)
    - [Initialize modem](#initialize-modem)
      - [Initialize standalone](#initialize-standalone)
      - [Initialize with config file](#initialize-with-config-file)
    - [Disconnect (Teardown) modem](#disconnect-teardown-modem)
    - [Device and SIM info](#device-and-sim-info)
    - [Signal and network Info](#signal-and-network-info)

## Introduction
The `modem_control` Bash script is designed to interactions with a modem devices.

## Features
- **Modem Initialization**: Configure the modem with tailored settings
- **Device Information Retrieval**: Obtain details about the device to have a better understanding of its characteristics and status.
- **Network Connection Handling**: Take control of network connections, ensuring reliable and stable connectivity.
- **Signal Packet Statistics**: Monitor the signal strength and gather packet statistics.
- **SIM Card Information**: Access SIM card details.

## Dependencies

The script requires the following utilities to function properly:
- `usbreset`
- `qmicli`
- `awk`
- `jq`

## Configuration Constants
Adjust the following constants according to the specific needs of your setup.

- **_DEVICE_PATH_**: Specifies the path to the cdc device of the modem. By default, this is typically set to `/dev/cdc-wdm0`.
- **_VENDOR_ID_MODEM_**: Defines the vendor ID of the modem, which is utilized in certain reset functions within the script. To ascertain the vendor ID of your modem, you can use the `lsusb` command. For example, the output `Bus 001 Device 004: ID 1508:1001 Fibocom Fibocom Modem` indicates that the vendor ID is `1508`.
- **_DELAY_TIME_ITF_CHANGE_**: Time delay (in seconds) after an interface change.
- **_MAX_WAIT_TIME_**: Maximum wait time (in seconds) for loop operations.
- **_DEBUG_**: When set to `0`, all non-JSON outputs are redirected to the _LOG_FILE_. Conversely, if set to any other value, errors will also be displayed on the console. For production environments, especially when the called application or script relies on JSON parsing, it is important to ensure that this is set to `0`.
- **_DEBUG_LOG_AS_JSON_**: Currently, this feature is not implemented, as it has not found a use case in production environments.

## Command Result Response

Every command executed by the script provides a JSON-formatted response containing the following objects:

- **state**: An object that indicates the status of the command execution.
  - If the command is executed successfully, `state` is set to `"success"`.
  - In case of an error, `state` is set to `"error"`, and a error message is provided.
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

## Usage

This section outlines various commands for modem operations such as initialization, disconnection, and information retrieval.

### Initialize modem

The modem can be initialized in two different ways: using standalone mode or by using a configuration file in JSON format. The standalone mode allows for direct argument passing to the script, while the configuration file enables persistent settings.

#### Initialize standalone

To initialize the modem in standalone mode, you can execute the script with the required arguments as shown in the example below:

```bash
./modem_control -init-modem --mode=standalone --apn=wsim --no-roaming=no --auth=NONE
```

<details>
<summary>Show example output of <i>-init-modem</i></summary>

```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "sim_info": {
      "sim_card_state": "present",
      "slot_app_state": "available",
      "application_state": "ready",
      "pin1_state": "disabled",
      "imsi": "<imsi>",
      "iccid": "<iccid>"
    },
    "profile_settings": {
      "profile_pdp_type": "ipv4",
      "profile_no_roaming": "",
      "profile_apn": "wsim",
      "profile_username": "",
      "profile_password": "",
      "profile_auth": "none"
    },
    "interface": {
      "used_interface": "cellular",
      "interface_available": "true",
      "sys_path": "/sys/class/net/cellular",
      "interface_up": "true"
    },
    "network_settings": {
      "ip_family": "IPv4",
      "ip": "10.2.18.210",
      "mtu": "1500",
      "subnet": "255.255.255.252",
      "gateway": "10.2.18.209",
      "dns_primary": "130.244.127.161",
      "domains": "none",
      "dns_secondary": "130.244.127.169"
    },
    "device_info": {
      "imei": "869816054543839",
      "manufacturer": "Fibocom Wireless Inc.",
      "model": "NL668_EAU_05_M.2_10_51",
      "revision": "19305.1000.00.02.73.03",
      "hw_revision": "V1.0.0",
      "operating_mode": "online-connected"
    },
    "serving_provider": {
      "plmn": "262-01",
      "mcc": 262,
      "mnc": 1,
      "description": "TDG",
      "roaming": true,
      "selected_network": "3gpp",
      "current_serving": true
    },
    "signal_strength": {
      "simple_signal": {
        "signal_human_readable": "good",
        "signal_bar": 3
      },
      "extended_signal": {
        "rssi": {
          "value": -78,
          "unit": "dBm"
        },
        "rsrq": {
          "value": -15,
          "unit": "dB"
        },
        "rsrp": {
          "value": -108,
          "unit": "dBm"
        },
        "snr": {
          "value": -1,
          "unit": "dB"
        }
      }
    },
    "operating_mode": {
      "operating_mode": "online-connected"
    }
  }
}
```

</details>
<br/>

#### Initialize with config file

You can also connect by using a configuration file.
There is a example config file attached.

```bash
./modem_control -init-modem --mode="config.json" # path to config file
```
<br>

### Disconnect (Teardown) modem

Use the `-teardown-modem` command to disconnect.

```bash
./modem_control -teardown-modem
```

<details>
<summary>Show example output of <i>-teardown-modem</i></summary>

```json
{
  "state": {
    "state": "success",
    "message": "Network stopped successfully"
  },
  "value": {
    "operating_mode": "online-disconnected"
  }
}
```

</details>
<br/>

### Device and SIM info

**Verify modem availability on the system**

```bash
./modem_control -is-modem-available
```

<details>
<summary>Show example output of <i>-is-modem-available</i></summary>

```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "usb_vendor_product": "1508:1001",
    "device_path": "/dev/cdc-wdm0"
  }
}
```

</details>
<br/>

**Get some general modem device information**

```bash
./modem_control -get-device-info
```

<details>
<summary>Show example output of <i>-get-device-info</i></summary>

```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "imei": "<imei>",
    "manufacturer": "Fibocom Wireless Inc.",
    "model": "NL668_EAU_05_M.2_10_51",
    "revision": "19305.1000.00.02.73.03",
    "hw_revision": "V1.0.0",
    "operating_mode": "online-disconnected"
  }
}
```

</details>
<br/>

**Get some general SIM information**

```bash
./modem_control -get-sim-info
```

<details>
<summary>Show example output of <i>-get-device-info</i></summary>

```json
{
  "state": {
    "state": "ready",
    "message": "SIM is ready and can be used"
  },
  "value": {
    "sim_card_state": "present",
    "slot_app_state": "available",
    "application_state": "ready",
    "pin1_state": "disabled",
    "imsi": "240075813751866",
    "iccid": "89462038043014770400"
  }
}
```

</details>
<br/>

**Get the operating mode information**

<details>
<summary>Show example output of <i>-get-operating-mode</i></summary>

```bash
./modem_control -get-operating-mode
```

```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "operating_mode": "online-disconnected"
  }
}
```

</details>
<br/>

### Signal and network Info 

**Get provider information**

```bash
./modem_control -get-serving-provider
```

<details>
<summary>Show example output of <i>-get-serving-provider</i></summary>

```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "plmn": "262-01",
    "mcc": 262,
    "mnc": 1,
    "description": "TDG",
    "roaming": true,
    "selected_network": "3gpp",
    "current_serving": true
  }
}
```

</details>
<br/>

**Check autoreconnect based on provider and system data**

```bash
./modem_control -check-autoreconnect
```

> _Note:_
> This command checks if the provided network settings are still the same as the system settings

<details>
<summary>Show example output of <i>-check-autoreconnect</i></summary>

```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "report_action": "assigned_ip_eq_provided"
  }
}
```

</details>
<br/>

**Fetch signal strength information**

```bash
./modem_control -get-signal-strength
```

<details>
<summary>Show example output of <i>-get-signal-strength</i></summary>


```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "simple_signal": {
      "signal_human_readable": "good",
      "signal_bar": 3
    },
    "extended_signal": {
      "rssi": {
        "value": -78,
        "unit": "dBm"
      },
      "rsrq": {
        "value": -15,
        "unit": "dB"
      },
      "rsrp": {
        "value": -108,
        "unit": "dBm"
      },
      "snr": {
        "value": 2,
        "unit": "dB"
      }
    }
  }
}
```

</details>
<br/>

**Get packet statistic**

```bash
./modem_control -get-packet-statistics
```

<details>
<summary>Show example output of <i>-get-packet-statistics</i></summary>


```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "tx_packets": 14,
    "tx_bytes": 1052,
    "tx_dropped_packets": 0,
    "rx_packets": 14,
    "rx_bytes": 1324,
    "rx_dropped_packets": 0
  }
}
```

</details>
<br/>

**Get interface information**

```bash
./modem_control -get-itf-information
```

<details>
<summary>Show example output of <i>-get-itf-information</i></summary>

```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "used_interface": "cellular",
    "interface_available": "true",
    "sys_path": "/sys/class/net/cellular",
    "interface_up": "true"
  }
}
```

</details>
<br/>

**Get the network settings from provider**

```bash
./modem_control -get-network-settings
```

<details>
<summary>Show example output of <i>-get-network-settings</i></summary>

```json
{
  "state": {
    "state": "success",
    "message": "success"
  },
  "value": {
    "ip_family": "IPv4",
    "ip": "10.2.18.210",
    "mtu": "1500",
    "subnet": "255.255.255.252",
    "gateway": "10.2.18.209",
    "dns_primary": "130.244.127.161",
    "domains": "none",
    "dns_secondary": "130.244.127.169"
  }
}
```

</details>
<br/>