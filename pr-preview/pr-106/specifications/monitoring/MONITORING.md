# Monitoring Specification

---

**Version:** 1.0 |
**Date:** August 19, 2024

---

## Introduction

Pillarbox integrates with a monitoring platform that provides real-time and historical dashboards for:

- Quality of Service (QoS): QoS refers to the performance level of a service, especially in terms of its transmission
  quality and service availability.
- Quality of Experience (QoE): QoE is a user-centric measure that evaluates the overall experience of the user with a
  service.

This article describes the flexible event model that allows our players to send data to our monitoring platform.

## JSON Key / Value Naming Conventions

The following naming conventions are applied for key / values appearing in JSON payloads:

- Keys use snake case.
- Values are provided in a form best suited for display, usually capitalized, with possible exceptions when brand
  names are involved (e.g. macOS must not be capitalized as MacOS).
- The only values provided in uppercase are event types (e.g. `START`).

> [!WARNING]
> Values provided by the system (e.g. device names) can be assumed as having a form already best suited for display.
> Their case must not be changed.

## General Event Format

Events provide data related to specific points of interests in the lifetime of a playback session. Three kinds of event
types are available:

- `START`
- `ERROR`
- Status events (`STOP`, `HEARTBEAT`)

Associated information is provided in JSON payloads all having the same fixed top-level structure containing the
following keys:

| Key          | Description                                 | Format                                                          | Examples                               |
|--------------|---------------------------------------------|-----------------------------------------------------------------|----------------------------------------|
| `data`       | Data associated with the event              | JSON dictionary                                                 | `{ ... }`                              |
| `event_name` | The name of the event                       | `START`, `STOP`, `ERROR`, `HEARTBEAT`                           | `STOP`                                 |
| `session_id` | A unique identifier for the session         | [UUID](https://www.itu.int/en/ITU-T/asn1/Pages/UUID/uuids.aspx) | `37b18444-76b6-4159-8539-d48ea5ecbc86` |
| `timestamp`  | The timestamp at the time the event is sent | [Unix timestamp](https://unixtime.org) in milliseconds          | `1717665997932`                        |
| `version`    | The version of the JSON format              | Number                                                          | 1                                      |

> [!WARNING]
> All keys listed above are mandatory.

All events associated with the same session **MUST** be assigned the same `session_id`. The `data` format associated
with each event type is described in more detail below.

## Start Event `data`

An event with the name `START` **MUST** be sent to signal the start of a playback session. This event conveys important
static context information and is therefore required, no matter playback successfully starts or not:

- Success: The `START` event **MUST** be sent when the player is ready to play.
- Failure: The `START` event **MUST** be sent immediately before an `ERROR` event describing the reason for the failure.

The associated event data dictionary supports the following keys:

| Key           | Description                  | Format          | Examples  |
|---------------|------------------------------|-----------------|-----------|
| `browser`     | Browser information          | JSON dictionary | `{ ... }` |
| `device`      | Device information           | JSON dictionary | `{ ... }` |
| `media`       | Media information            | JSON dictionary | `{ ... }` |
| `os`          | Operating system information | JSON dictionary | `{ ... }` |
| `player`      | Player information           | JSON dictionary | `{ ... }` |
| `screen`      | Screen information           | JSON dictionary | `{ ... }` |
| `qoe_timings` | QoE timings                  | JSON dictionary | `{ ... }` |
| `qos_timings` | QoS timings                  | JSON dictionary | `{ ... }` |

> [!WARNING]
> Requirements for each key are not provided explicitly but implementations **SHOULD** fill as much information as
> possible. If some information cannot be reliably determined it **SHOULD** be omitted.

### JSON Schema

[start-schema.json](specifications/monitoring/schemas/start-schema.json ':ignore')

### Browser

The `browser` JSON data dictionary supports the following keys:

| Field     | Description         | Format | Examples            |
|-----------|---------------------|--------|---------------------|
| `name`    | The browser name    | String | `Firefox`, `Safari` |
| `version` | The browser version | String | `129.0`             |

### Device

The `device` JSON data dictionary supports the following keys:

| Field   | Description                                  | Format                                               | Examples                               |
|---------|----------------------------------------------|------------------------------------------------------|----------------------------------------|
| `id`    | A unique anonymous identifier for the device | String                                               | `105124c0-fa84-4028-908e-618f2402d46f` |
| `model` | The device model                             | String                                               | `iPhone15.7`, `Samsung Galaxy S24`     |
| `type`  | The device type                              | `Car`, `Desktop`, `Headset`, `Phone`, `Tablet`, `TV` | `Phone`                                |

### Media

The `media` JSON data dictionary supports the following keys:

| Field          | Description                                               | Format | Examples                                                                                 |
|----------------|-----------------------------------------------------------|--------|------------------------------------------------------------------------------------------|
| `asset_url`    | The URL of the content being played                       | String | `https://...`                                                                            |
| `id`           | A unique media identifier                                 | String | `urn:rts:video:123456`                                                                   |
| `metadata_url` | The URL where media metadata was fetched                  | String | `https://...`                                                                            |
| `origin`       | A description of the context in which the media is played | String | `ch.srgssr.app`, `https://...`                                                           |

Some remarks:

- Any token appended **by the client** to the URL of the asset being played **SHOULD NOT** appear in `asset_url`.
- The `origin` is flexible but **SHOULD** describe the context of playback, for example an application identifier or the
  URL of the web page hosting the media.

### Operating System

The `os` JSON data dictionary supports the following keys:

| Field     | Description                  | Format | Examples                      |
|-----------|------------------------------|--------|-------------------------------|
| `name`    | The operating system name    | String | `macOS`, `Windows`, `Android` |
| `version` | The operating system version | String | `14.5`                        |

### Player

The `player` JSON data dictionary supports the following keys:

| Field      | Description         | Format                    | Examples                             |
|------------|---------------------|---------------------------|--------------------------------------|
| `name`     | The player name     | String                    | `Pillarbox`, `Letterbox`, `video.js` |
| `platform` | The player platform | `Android`, `Apple`, `Web` | `Android`                            |
| `version`  | The player version  | String                    | `1.2.3`                              |

### Quality of Experience Timings

The `qoe_timings` JSON data dictionary supports the following keys:

| Field      | Description                                       | Format               | Examples |
|------------|---------------------------------------------------|----------------------|----------|
| `asset`    | Time the user waited for asset playback to start  | Time in milliseconds | `1145`   |
| `metadata` | Time the user waited for metadata to be retrieved | Time in milliseconds | `412`    |
| `total`    | Total time the user waited for playback to start  | Time in milliseconds | `1763`   |

> [!WARNING]
> QoE timings measure the perceived user experience. If content preloading in a playlist makes it possible to start
> instantaneously (or almost), these values **SHOULD** be zero or close to zero.

### Quality of Service Timings

The `qos_timings` JSON data dictionary supports the following keys:

| Field      | Description                                     | Format               | Examples |
|------------|-------------------------------------------------|----------------------|----------|
| `asset`    | Time for the player to start playing the asset  | Time in milliseconds | `1145`   |
| `drm`      | Time to load DRM content keys                   | Time in milliseconds | `245`    |
| `metadata` | Time for metadata to be retrieved by the player | Time in milliseconds | `412`    |
| `token`    | Time to fetch an authorization token            | Time in milliseconds | `356`    |

> [!WARNING]
> QoS timings measure actual system performance. They **SHOULD** reflect the time technically required to fetch content,
> whether content preloading could take place or not.

### Screen

The `screen` JSON data dictionary supports the following keys:

| Field    | Description                 | Format | Examples |
|----------|-----------------------------|--------|----------|
| `height` | The screen height in pixels | Number | `2160`   |
| `width`  | The screen width in pixels  | Number | `3840`   |

### Example

```json
{
  "data": {
    "device": {
      "id": "8e9242a4-60b6-48f9-8dfb-6ee43e36c7eb",
      "model": "iPad13,4",
      "type": "Tablet"
    },
    "media": {
      "asset_url": "https://rts-vod-amd.akamaized.net/ww/14895342/85891228-1e53-371b-997a-094380f533e2/master.m3u8",
      "id": "urn:rts:video:14895342",
      "metadata_url": "https://il.srgssr.ch/integrationlayer/2.1/mediaComposition/byUrn/urn:rts:video:14895342?onlyChapters=true&vector=appplay",
      "origin": "ch.srgssr.Pillarbox-demo"
    },
    "os": {
      "name": "iPadOS",
      "version": "18.0"
    },
    "player": {
      "name": "Pillarbox",
      "platform": "Apple",
      "version": "2.0.0-49"
    },
    "qoe_timings": {
      "asset": 1164,
      "metadata": 320,
      "total": 1484
    },
    "screen": {
      "height": 2388,
      "width": 1668
    }
  },
  "event_name": "START",
  "session_id": "ebdb3da7-bc77-454e-9de0-a1dfa8091e84",
  "timestamp": 1723640597805,
  "version": 1
}
```

## Error Event `data`

An event with the name `ERROR` **MUST** be sent when an error, either fatal or not, has been encountered:

- A fatal error makes playback fail without the possibility to recover, either when playback is started or during
  playback (e.g. following a network failure).
- A non-fatal error (warning) informs about potential issues that occur behind the scenes and might affect the playback
  experience negatively.

> [!WARNING]
> A fatal `ERROR` at startup **MUST** always be preceded by a `START` event. If playback is restarted after a fatal
`ERROR` a new session **MUST** be created, beginning with a new `START` event.

The associated event data dictionary supports the following keys:

| Key                  | Description                                                                                          | Format                                                 | Examples                                                                                    |
|----------------------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------|---------------------------------------------------------------------------------------------|
| `duration`           | The content duration, as retrieved from the playlist                                                 | Time in milliseconds                                   | `16548`                                                                                     |
| `log`                | Any additional information that might be helpful                                                     | Any                                                    | `{ ... }`, `[...]`, `Stack trace symbols: ...`                                              |
| `message`            | The message associated with the error (might be localized)                                           | String                                                 | `Not found`                                                                                 |
| `name`               | The name of the error                                                                                | String                                                 | `ERR-404`                                                                                   |
| `position`           | The current player position, relative to the beginning of the playlist. Negative values are admitted | Time in milliseconds                                   | `16548`                                                                                     |
| `position_timestamp` | The current player timestamp, as retrieved from the playlist. Omitted if not available               | [Unix timestamp](https://unixtime.org) in milliseconds | `1717665997932`                                                                             |
| `severity`           | The error severity                                                                                   | `Warning`, `Fatal`                                     | `Warning`                                                                                   |
| `url`                | The URL that was affected by the error                                                               | String                                                 | `https://...`                                                                               |
| `vpn`                | A value indicating whether a VPN is enabled on the device                                            | Boolean                                                | `true`                                                                                      |

> [!WARNING]
> Requirements for each key are not provided explicitly but implementations **SHOULD** fill as much information as
> possible. If some information cannot be reliably determined it **SHOULD** be omitted.

Some remarks:

- If the error occurs before playback has started (e.g. during metadata retrieval) then `position`, `player_timestamp`
  and `duration` **MUST** be omitted.
- The `url` **SHOULD** describe the content that was affected as closely as possible, down to media playlists or segment
  URLs, provided this information is available.
- The `log` is informally defined so that any useful information can be added for investigation purposes.

### JSON Schema

[error-schema.json](specifications/monitoring/schemas/error-schema.json ':ignore')

### Example

```json
{
  "data": {
    "message": "Segment exceeds specified bandwidth for variant",
    "name": "CoreMediaErrorDomain(-12318)",
    "position": 1024,
    "severity": "Warning",
    "url": "https://rts-vod-amd.akamaized.net/ww/14895342/85891228-1e53-371b-997a-094380f533e2/index-f4-v1.m3u8"
  },
  "event_name": "ERROR",
  "session_id": "ebdb3da7-bc77-454e-9de0-a1dfa8091e84",
  "timestamp": 1723640598877,
  "version": 1,
  "vpn": false
}
```

## Status Event `data`

Other events **MUST** be sent during the playback session:

| Event name  | Description                             | Time at which the event is sent                                               |
|-------------|-----------------------------------------|-------------------------------------------------------------------------------|
| `STOP`      | Playback ended                          | When playback ends normally or is interrupted                                 |
| `HEARTBEAT` | Informs that the session is still alive | Every 30 seconds (also when paused). First heartbeat sent right after `START` |

The associated event data dictionary supports the following keys:

| Key                  | Description                                                                                          | Format                                                 | Examples                                                                                    |
|----------------------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------|---------------------------------------------------------------------------------------------|
| `airplay`            | A value indicating whether AirPlay is currently active                                               | Boolean                                                | `true`                                                                                      |
| `bandwidth`          | Bandwidth                                                                                            | Number in bits per second                              | `4000000`                                                                                   |
| `bitrate`            | Bitrate of the content being played                                                                  | Number in bits per second                              | `1000000`                                                                                   |
| `buffered_duration`  | Duration of the content currently available in buffer                                                | Time in milliseconds                                   | `12000`                                                                                     |
| `duration`           | The content duration, as retrieved from the playlist                                                 | Time in milliseconds                                   | `16548`                                                                                     |
| `frame_drops`        | The total number of frame drops experienced during the session                                       | Number                                                 | `12`                                                                                        |
| `playback_duration`  | The duration of the playback session                                                                 | Time in milliseconds                                   | `40000`                                                                                     |
| `position`           | The current player position, relative to the beginning of the playlist. Negative values are admitted | Time in milliseconds                                   | `16548`                                                                                     |
| `position_timestamp` | The current player timestamp, as retrieved from the playlist. Omitted if not available               | [Unix timestamp](https://unixtime.org) in milliseconds | `1717665997932`                                                                             |
| `stall`              | Stall information                                                                                    | JSON dictionary                                        | `{ ... }`                                                                                   |
| `stream_type`        | Stream type                                                                                          | `On-demand`, `Live`                                    | `On-demand`                                                                                 |
| `url`                | The URL that is being played                                                                         | String                                                 | `https://...`                                                                               |

> [!WARNING]
> Requirements for each key are not provided explicitly but implementations **SHOULD** fill as much information as
> possible. If some information cannot be reliably determined it **SHOULD** be omitted.

Some remarks:

- The `url` **SHOULD** describe the content currently being played as closely as possible, down to media playlists or
  segment URLs, provided this information is available.
- The `playback_duration` **MUST** be measured in wall-clock time, independently of playback speed adjustments.
- The `stream_type` is present in status events only, as those can more closely match potential stream type changes when
  a live playlist is closed and turns into an on-demand one.

### JSON Schema

[status-schema.json](specifications/monitoring/schemas/status-schema.json ':ignore')

### Stall

The `stall` JSON data dictionary supports the following keys:

| Field      | Description                                               | Format               | Examples |
|------------|-----------------------------------------------------------|----------------------|----------|
| `count`    | The total number of stalls experienced during the session | Number               | `4`      |
| `duration` | The total duration of stalls                              | Time in milliseconds | `9000`   |

The stall duration **MUST** be measured in wall-clock time, independently of playback speed adjustments.

> [!WARNING]
> If a player is able to provide stall information, both `count` and `duration` **MUST** be supplied, even if zero.

### Example

```json
{
  "data": {
    "airplay": false,
    "bandwidth": 23285774,
    "bitrate": 6129146,
    "buffered_duration": 36000,
    "duration": 2386040,
    "frame_drops": 2,
    "playback_duration": 10663,
    "position": 10618,
    "stall": {
      "count": 0,
      "duration": 0
    },
    "stream_type": "On-demand",
    "url": "https://rts-vod-amd.akamaized.net/ww/14895342/85891228-1e53-371b-997a-094380f533e2/index-f5-v1.m3u8"
  },
  "event_name": "STOP",
  "session_id": "ebdb3da7-bc77-454e-9de0-a1dfa8091e84",
  "timestamp": 1723640608474,
  "version": 1
}
```
