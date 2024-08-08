# Monitoring

- Version: 1.0.0

## Introduction

Pillarbox integrates with a monitoring platform that provides real-time and historical dashboards for:

- Quality of Service (QoS): QoS refers to the performance level of a service, especially in terms of its transmission quality and service availability. 
- Quality of Experience (QoE): QoE is a user-centric measure that evaluates the overall experience of the user with a service.

This article describes the flexible event model that allows our players to send data to our monitoring platform.

## General Event Format

Events aggregate data related to specific points of interests in the lifetime of a playback session. All event JSON payloads have the same fixed top-level structure:

| Key | Description | Format | Examples |
| - | - | - | - | 
| `event_name` | The name of the event | `START`, `STOP`, `ERROR`, `HEARTBEAT` | `STOP` |
| `session_id` | A unique identifier for the session | String | `37b18444-76b6-4159-8539-d48ea5ecbc86` |
| `timestamp` | The timestamp when the event is sent | Unix timestamp in milliseconds | `1717665997932` |
| `version` | The version of the JSON format | [Semantic version](https://semver.org/) | `1.2.3` |
| `data` | Data associated with the event | JSON Dictionary | `{ ... }` |

> [!IMPORTANT]
> All keys listed above are mandatory.

Data associated with the event varies depending on the event itself and is discussed separately below.

## Start Event

An event with the name `START` must be sent to signal the start of a playback session. This event conveys important context information and is therefore required, no matter playback can successfully start or not:

- Playback successfully starts: The `START` event must be sent when the player is ready to play.
- Playback fails at startup: The `START` event must be sent immediately before the `ERROR` event.

The associated event data dictionary supports the following keys:

| Key | Description | Format | Examples |
| - | - | - | - | 
| `browser` | Browser information | JSON dictionary | `{ ... }` |
| `device` | Device information | JSON dictionary | `{ ... }` |
| `media` | Media information | JSON dictionary | `{ ... }` |
| `os` | Operating system information | JSON dictionary | `{ ... }` |
| `player` | Player information | JSON dictionary | `{ ... }` |
| `screen` | Screen information | JSON dictionary | `{ ... }` |
| `qoe_timings` | QoE timings | JSON dictionary | `{ ... }` |
| `qos_timings` | QoS timings | JSON dictionary | `{ ... }` |

> [!IMPORTANT]
> Requirements for each key are not provided explicitly but implementations should fill as much information as possible.

### Browser

The `browser` JSON data dictionary supports the following keys:

| Field | Description | Format | Examples |
| - | - | - | - | 
| `name` | The browser name | String | `Firefox`, `Safari` |
| `version` | The browser version | String | `129.0` |

### Device

The `device` JSON data dictionary supports the following keys:

| Field | Description | Format | Examples |
| - | - | - | - | 
| `id` | A unique anonymous identifier for the device | String | `105124c0-fa84-4028-908e-618f2402d46f` |
| `model` | The device model | String | `iPhone15.7`, `Samsung Galaxy S24` |
| `type` | The device type | `Car`, `Desktop`, `Headset`, `Phone`, `Tablet`, `TV` | `Phone` |

### Media

The `media` JSON data dictionary supports the following keys:

| Field | Description | Format | Examples |
| - | - | - | - | 
| `id` | A unique media identifier | String | `urn:rts:video:123456` |
| `asset_url` | The URL of the content being played | String | `https://rts1-lsvs.akamaized.net/out/v1/62441d2399f14dce9e558b5503edba11/index.m3u8` |
| `metadata_url` | The URL where media metadata was fetched | String | `https://il.srgssr.ch/integrationlayer/2.0/mediaComposition/byUrn/urn:rts:video:3608506` |
| `origin` | A description of the context in which the media is played | String | `ch.srgssr.app`, `https://www.rts.ch/info/article/123` |

Some remarks:

- If a token was appended to the URL of the asset being played it should be stripped before being assigned to `asset_url`.
- The `origin` is flexible but should describe the context of playback, for example an application identifier or the URL of the web page hosting the media.

### Operating System

The `os` JSON data dictionary supports the following keys:

| Field | Description | Format | Examples |
| - | - | - | - | 
| `name` | The operating system name | String | `macOS`, `Windows`, `Android` |
| `version` | The operating system version | String | `14.5` |

### Player

The `player` JSON data dictionary supports the following keys:

| Field | Description | Format | Examples |
| - | - | - | - | 
| `name` | The player name | String | `Pillarbox`, `Letterbox`, `video.js` |
| `version` | The player version | String | `1.2.3` |

### Screen

The `screen` JSON data dictionary supports the following keys:

| Field | Description | Format | Examples |
| - | - | - | - | 
| `width` | The screen width in pixels | Number | `3840` |
| `height` | The screen height in pixels | Number | `2160` |

### Quality of Experience Timings

The `qoe_timings` JSON data dictionary supports the following keys:

| Field | Description | Format | Examples |
| - | - | - | - | 
| `metadata` | Time the user waited for metadata to be retrieved | Duration in milliseconds | `412` |
| `asset` | Time the user waited for asset playback to start | Duration in milliseconds | `1145` |
| `total` | Total time the user waited for playback to start | Duration in milliseconds | `1763` |

> [!IMPORTANT]
> QoE timings measure the perceived user experience. If content preloading in a playlist makes it possible to start instantaneously (or almost), these values should be zero or close to zero.

### Quality of Service Timings

The `qos_timings` JSON data dictionary supports the following keys:

| Field | Description | Format | Examples |
| - | - | - | - | 
| `metadata` | Time for metadata to be retrieved by the player | Duration in milliseconds | `412` |
| `asset` | Time for the player to start playing the asset | Duration in milliseconds | `1145` |
| `drm` | Time to load DRM content keys | Duration in milliseconds | `245` |
| `token` | Time to fetch an authorization token | Duration in milliseconds | `356` |

> [!IMPORTANT]
> QoS timings measure actual system performance. They should reflect the time technically required to fetch content, whether content preloading could take place or not.

### Example

```json
TODO
```

## Error Event

An event with the name `ERROR` must be sent when an error, either fatal or not, has been encountered:

- A fatal error makes playback fail without the possibility to recover, either when playback is started or during playback (e.g. following a network failure).
- A non-fatal error (warning) informs about potential issues that occur in the background, e.g. bitrates that do not match what is advertised and that could lead to unexpected stalls.

> [!IMPORTANT]
> A fatal `ERROR` at startup must always be preceded by a `START` event.

The associated event data dictionary supports the following keys:

| Key | Description | Format | Examples |
| - | - | - | - | 
| `severity` | The error severity | `WARNING`, `FATAL` | `WARNING` |
| `name` | The name of the error | String | `ERR-404` |
| `message` | The message associated with the error | String | `Not found` |
| `player_position` | The current player position | Duration in milliseconds | `16548` |
| `url` | The URL that was affected by the error | String | `https://rts1-lsvs.akamaized.net/out/v1/62441d2399f14dce9e558b5503edba11/index_1_948290.ts` |
| `log` | Other information that might be helpful | String | `{ ... }` |

> [!IMPORTANT]
> Requirements for each key are not provided explicitly but implementations should fill as much information as possible.

Some remarks:

- If the error occurs after before playback has started (e.g. during metadata retrieval) then `player_position` must be omitted.
- The `url` should describe as closely as possible the content that was affected, down to media playlists or segment URLs, provided this information is available.
- The `log` is informally defined and can range from raw information to stack traces if helpful.
- For DVR streams the `player_position` must represent the distance from the live edge (0 for streams).

### Example

```json
TODO
```

## Status Events

Other events can be sent during the playback session, conveying status information about the quality of the experience.

| Event name | Description | Time at which the event is sent |
| - | - | - |
| `STOP` | Playback ended | When playback ends normally or is interrupted |
| `HEARTBEAT` | Informs that the session is still alive | Every 30 seconds (also when paused) |

The associated event data dictionary supports the following keys:

| Key | Description | Format | Examples |
| - | - | - | - | 
| `url` | The URL that is being played | String | `https://rts1-lsvs.akamaized.net/out/v1/62441d2399f14dce9e558b5503edba11/index_1_948290.ts` |
| `bitrate` | Bitrate of the content being played | Number in bytes per second | `1000000` |
| `bandwidth` | Bandwidth | Number in bytes per second | `4000000` |
| `buffered_duration` | Duration of the content currently available in buffer | Duration in milliseconds | `12000` |
| `stall` | Stall information | JSON dictionary | `{ ... }` |
| `playback_duration` | The duration of the playback session | Duration in milliseconds | `40000` |
| `player_position` | The current player position | Duration in milliseconds | `16548` |

> [!IMPORTANT]
> Requirements for each key are not provided explicitly but implementations should fill as much information as possible.

Some remarks:

- The `url` should describe as closely as possible the content currently being played, down to media playlists or segment URLs, provided this information is available.
- The `playback_duration` must be measured in wall-clock time, independently of playback speed adjustments.
- For DVR streams the `player_position` must represent the distance from the live edge (0 for streams).

### Stall

The `stall` JSON data dictionary supports the following keys:

| Field | Description | Format | Examples |
| - | - | - | - | 
| `count` | The total number of stalls experienced during the session | Number | `4` |
| `duration` | The total duration of stalls | Duration in milliseconds | `9000` |

The stall duration must be measured in wall-clock time, independently of playback speed adjustments.