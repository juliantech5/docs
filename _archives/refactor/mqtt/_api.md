# API Reference

As MQTT broker use:

* Host: `<Region>.thethings.network`
* Port: `1883`

> TLS is not yet supported in this version.

## Region

Each application is registered to one or more regions. This determines the host of the MQTT broker you will use.

Currently we have:

* `eu`
* `us-west`
* `brazil`
* `se-asia`

> On [preview.dashboard.thethingsnetwork.org](https://preview.dashboard.thethingsnetwork.org/), go to your application. Under **Handler Status** you will also find the region the application is registered to. You will need the part that follows `ttn-handler-`, e.g. `eu`.

<div class="alert alert-danger"><strong>Warning:</strong> The names of the current regions might still change until this version goes into production.</div>

## Authentication

For authentication use:

* Username: Application ID (string).
* Password: Application Access Key (base64).

> On [preview.dashboard.thethingsnetwork.org](https://preview.dashboard.thethingsnetwork.org/), go to your application to find the **Application ID** and **Access Keys** you can use.

## Topic: Activations

To get activations for all devices registered to the application you have authenticated against, subscribe to:

```
<AppID>/devices/+/activations
```

Or, since the authentication already limits you to an Application:

```
+/devices/+/activations
```

To get activations for a specific device subscribe to:

```
+/devices/<DeviceID>/activations
```

Or if you like to be verbose:

```
<AppID>/devices/<DeviceID>/activations
```

### Message format

A published topic and JSON encoded payload could look like:

```bash
hello-world/devices/my-uno/activations

{
  "app_eui": "70B3D57EF000001C",
  "dev_eui": "0004A30B001B7AD2",
  "dev_addr": "26012723",
  "metadata": {
    "time": "2016-09-13T09:59:02.90329585Z",
    "frequency": 868.5,
    "modulation": "LORA",
    "data_rate": "SF7BW125",
    "coding_rate": "4/5",
    "gateways": [{
      "eui": "B827EBFFFE87BD22",
      "timestamp": 1484146403,
      "time": "2016-09-13T09:59:02.867283Z",
      "channel": 2,
      "rssi": -49,
      "snr": 7,
      "rf_chain": 1
    }]
  }
}
```

> As you can see the ID of the activated device is not part of the payload, but must be filtered from the returned topic.

## Topic: Up (Receive)

To receive messages from all devices registered to the application, subscribe to:

```
<AppID>/devices/+/up
```

Or, since the authentication already limits you to an Application:

```
+/devices/+/up
```

To get messages for a specific device subscribe to:

```
+/devices/<AppID>/up
```

Or if you like to be verbose:

```
<AppID>/devices/<DeviceID>/up
```

### Message format

A published topic and JSON encoded payload could look like:

```bash
hello-world/devices/my-uno/up

{
  "port": 1,
  "counter": 0,
  "payload_raw": "SGVsbG8=",
  "payload_fields": {
    "message": "Hello"
  },
  "metadata": {
    "time": "2016-09-13T09:59:08.179119279Z",
    "frequency": 868.3,
    "modulation": "LORA",
    "data_rate": "SF7BW125",
    "coding_rate": "4/5",
    "gateways": [{
      "eui": "B827EBFFFE87BD22",
      "timestamp": 1489443003,
      "time": "2016-09-13T09:59:08.167028Z",
      "channel": 1,
      "rssi": -49,
      "snr": 8,
      "rf_chain": 1
    }]
  }
}
```

> As you can see the ID of the device is not part of the payload, but must be filtered from the returned topic.

## Topic: Down (Send)

To send a message to a specific device registered to the application, publish to:

```
+/devices/<DeviceID>/down
```

Or, in this case maybe more clear:

```
<AppID>/devices/<DeviceID>/down
```

### Message format

Encoded as JSON string, format your message as:

```json
{
  "payload_raw": "SGVsbG8gd29ybGQK=",
  "port": 1
}
```

* `payload_raw [string]`: Payload as a base64 encoded array of bytes.
* `ttl [string]`: Time-to-live. How long the message should remained queued until it can be delivered to the device.