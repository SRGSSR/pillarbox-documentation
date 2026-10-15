# Standard Metadata Specification

---

**Version:** 1.0 |
**Date:** October 05, 2026

---

## Introduction

Metadata associated with media playback is usually pretty standard. A player namely expects to know what is being played (stream URL) and have basic metadata (title, subtitle, artwork) that can be displayed to the user.

For products that do not already provide this metadata through a dedicated backend, Pillarbox defines a comprehensive, standard metadata format that can be adopted when implementing a playback metadata endpoint. All Pillarbox players natively understand this format, eliminating the need for custom metadata connector implementations.

## JSON Format

The Pillarbox standard metadata format is made of the following top-level structure:

| Key            | Description                                | Mandatory | Format                                  | Examples                                              |
|----------------|--------------------------------------------|-----------|-----------------------------------------|-------------------------------------------------------|
| `chapters`     | Chapters                                   | No        | JSON array (of _Chapters_, see below)   | `[ ... ]`                                             |
| `customData`   | Custom data                                | No        | JSON dictionary                         | `{ ... }`                                             |
| `description`  | A long description of the content          | No        | String                                  | `Picking up the story where Back to the Future (...)` |
| `drm`          | DRM information                            | No        | JSON dictionary (_DRM_, see below)      | `{ ... }`                                             |
| `episodeNumber`| The episode number                         | No        | Integer                                 | 2                                                     |
| `identifier`   | A unique content identifier                | No        | String                                  | `back_to_future_2`                                    |
| `posterUrl`    | The URL of an image describing the content | No        | String                                  | `https://...`                                         |
| `seasonNumber` | The season number                          | No        | Integer                                 | 1                                                     |
| `source`       | Source information                         | No        | JSON dictionary (_Source_, see below)   | `{ ... }`                                             |
| `subtitle`     | A subtitle describing the content          | No        | String                                  | `Science-fiction`                                     |
| `subtitles`    | Information about external subtitles       | No        | JSON array (of _Subtitles_, see below)  | `[ ... ]`                                             |
| `timeRanges`   | Time ranges                                | No        | JSON array (of _Time range_, see below) | `[ ... ]`                                             |
| `title`        | A title describing the content             | No        | String                                  | `Back to the Future II`                               |
| `version`      | The version of the JSON format             | No        | Integer                                 | 1                                                     |
| `viewport`     | The viewport                               | No        | `STANDARD`, `MONOSCOPIC` (360°)         | `STANDARD`                                            |

Objects appearing as JSON dictionaries or arrays are described below.

### Chapter

Describes a chapter associated with the content.

| Key          | Description                                | Mandatory | Format               | Examples            |
|--------------|--------------------------------------------|-----------|----------------------|---------------------|
| `endTime`    | The chapter's end time                     | Yes       | Time in milliseconds | `30000`             |
| `identifier` | A unique identifier                        | No        | String               | `hill_valley_1955`  |
| `posterUrl`  | The URL of an image describing the chapter | No        | String               | `https://...`       |
| `startTime`  | The chapter's start time                   | Yes       | Time in milliseconds | `10000`             |
| `title`      | A title describing the chapter             | Yes       | String               | `Hill Valley, 1955` |

### Custom data

A JSON dictionary which can be used to convey any arbitrary useful information.

### DRM

The DRM configuration.

| Key              | Description                 | Mandatory | Format  | Examples            |
|------------------|-----------------------------|-----------|---------|---------------------|
| `certificateUrl` | The certificate URL         | No        | String  | `https://...`       |
| `keySystem`      | The key system              | Yes       | String  | `com.apple.fps`     |
| `licenseUrl`     | The license acquisition URL | Yes       | String  | `https://...`       |
| `multisession`   | If multi session is enabled | No        | Boolean | `true`              |

### Source

Describes the source of the content.

| Key                   | Description               | Mandatory | Format                     | Examples                |
|-----------------------|---------------------------|-----------|----------------------------|-------------------------|
| `audioFragmentFormat` | The audio fragment format | No        | String                     | `TS`                    |
| `mimeType`            | The MIME type             | No        | String                     | `application/x-mpegURL` |
| `type`                | The type of content       | No        | `ON-DEMAND`, `LIVE`, `DVR` | `ON-DEMAND`             |
| `url`                 | The URL to be played      | Yes       | String                     | `https://...`           |
| `videoFragmentFormat` | The video fragment format | No        | String                     | `FMP4`                  |

### Subtitles

Describes external subtitles provided for the content.

| Key        | Description                               | Mandatory | Format                                 | Examples      |
|------------|-------------------------------------------|-----------|----------------------------------------|---------------|
| `kind`     | The kind of subtitles                     | Yes       | `SUBTITLES`, `DESCRIPTION`, `CAPTIONS` | `SUBTITLES`   |
| `label`    | A label describing the subtitles          | Yes       | String                                 | `English`     |
| `language` | The language code                         | Yes       | String (BCP 47)                        | `en`          |
| `url`      | The URL where the subtitles can be loaded | Yes       | String                                 | `https://...` |

### Time range

Describes a time range associated with the content.

| Key         | Description              | Mandatory | Format                                          | Examples  |
|-------------|--------------------------|-----------|-------------------------------------------------|-----------|
| `endTime`   | The chapter's end time   | Yes       | Time in milliseconds                            | `60000`   |
| `startTime` | The chapter's start time | Yes       | Time in milliseconds                            | `50000`   |
| `type`      | The type of time range   | Yes       | `BLOCKED`, `OPENING_CREDITS`, `CLOSING_CREDITS` | `BLOCKED` |

## Example

```json
{
    "chapters": [
        {
            "endTime": 30000,
            "identifier": "hill_valley_1955",
            "posterUrl": "https://...",
            "startTime": 10000,
            "title": "Hill Valley, 1955"
        }
    ],
    "customData": {
    },
    "description": "Picking up the story where Back to the Future (...)",
    "drm": {
        "certificateUrl": "https://...",
        "keySystem": "com.apple.fps",
        "licenseUrl": "https://...",
        "multisession": true
    },
    "episodeNumber": 2,
    "identifier": "back_to_future_2",
    "posterUrl": "https://...",
    "seasonNumber": 1,
    "source": {
        "audioFragmentFormat": "TS",
        "mimeType": "application/x-mpegURL",
        "type": "ON-DEMAND",
        "url": "https://...",
        "videoFragmentFormat": "FMP4"
    },
    "subtitle": "Science-fiction",
    "subtitles": [
        {
            "kind": "SUBTITLES",
            "label": "English",
            "language": "en",
            "url": "https://..."
        }
    ],
    "timeRanges": [
        {
            "endTime": 60000,
            "startTime": 50000,
            "type": "BLOCKED"
        }
    ],
    "title": "Back to the Future II",
    "version": 1,
    "viewport": "STANDARD"
}
```

## JSON Schema

[metadata-schema.json](specifications/standard-connector/schemas/metadata-schema.json ':ignore')
