# Standard Metadata Specification

---

**Version:** 1.0 |
**Date:** October 05, 2026

---

## Introduction

Metadata associated with media playback is usually pretty standard. As a user, you namely expect to know what is being played (stream URL, title, subtitle), have visually engaging artwork associated with the content (for example, on a mobile device’s lock screen), or navigate within the content.

For products that do not already provide this metadata through a dedicated backend, Pillarbox defines a comprehensive, standard metadata format that can be adopted when implementing a playback metadata endpoint. All Pillarbox players natively understand this format, eliminating the need for custom metadata connector implementations.

## JSON Format

The Pillarbox standard metadata format is made of the following top-level structure:

| Key            | Description                                | Format                                  | Examples                                              |
|----------------|--------------------------------------------|-----------------------------------------|-------------------------------------------------------|
| `chapters`     | Chapters                                   | JSON array (of _Chapters_, see below)   | `[ ... ]`                                             |
| `customData`   | Custom data                                | JSON dictionary                         | `{ ... }`                                             |
| `description`  | A long description of the content          | String                                  | `Picking up the story where Back to the Future (...)` |
| `drm`          | DRM information                            | JSON dictionary (_DRM_, see below)      | `{ ... }`                                             |
| `episodeNumber`| The episode number                         | Integer                                 | 2                                                     |
| `identifier`   | A unique content identifier                | String                                  | `back_to_future_2`                                    |
| `posterUrl`    | The URL of an image describing the content | String                                  | `https://...`                                         |
| `seasonNumber` | The season number                          | Integer                                 | 1                                                     |
| `source`       | Source information                         | JSON dictionary (_Source_, see below)   | `{ ... }`                                             |
| `subtitle`     | A subtitle describing the content          | String                                  | `Science-fiction`                                     |
| `subtitles`    | Information about external subtitles       | JSON array (of _Subtitles_, see below)  | `[ ... ]`                                             |
| `timeRanges`   | Time ranges                                | JSON array (of _Time range_, see below) | `[ ... ]`                                             |
| `title`        | A title describing the content             | String                                  | `Back to the Future II`                               |
| `version`      | The version of the JSON format             | Number                                  | 1                                                     |
| `viewport`     | The viewport                               | `STANDARD`, `MONOSCOPIC` (360°)         | `STANDARD`                                            |

> [!INFO]
> All keys listed above are optional.

Objects appearing as JSON dictionaries or arrays are described below.

### Chapter

Describes a chapter associated with the content.

| Key          | Description                                | Format               | Examples            | Mandatory? |
|--------------|--------------------------------------------|----------------------|---------------------|------------|
| `endTime`    | The chapter's end time                     | Time in milliseconds | `30000`             | Y          |
| `identifier` | A unique identifier                        | String               | `hill_valley_1955`  | N          |
| `posterUrl`  | The URL of an image describing the chapter | String               | `https://...`       | N          |
| `startTime`  | The chapter's start time                   | Time in milliseconds | `10000`             | Y          |
| `title`      | A title describing the chapter             | String               | `Hill Valley, 1955` | Y          |

### Custom data

A JSON dictionary which can be used to convey any arbitrary useful information.

### DRM

The DRM configuration.

| Key              | Description                 | Format                                            | Examples            | Mandatory? |
|------------------|-----------------------------|---------------------------------------------------|---------------------|------------|
| `certificateUrl` | The certificate URL         | String                                            | `https://...`       | N          |
| `keySystem`      | The key system              | `WIDEVINE`, `FAIRPLAY`, `CLEAR_KEY`, `PLAY_READY` | `FAIRPLAY`          | Y          |
| `licenseUrl`     | The license acquisition URL | String                                            | `https://...`       | Y          |
| `multisession`   | If multi session is enabled | Boolean                                           | `true`              | N          |

### Source

Describes the source of the content.

| Key                   | Description                                       | Format                     | Examples                | Mandatory? |
|-----------------------|---------------------------------------------------|----------------------------|-------------------------|------------|
| `audioFragmentFormat` | The audio fragment format                         | String                     | `TS`                    | N          |
| `mimeType`            | The MIME type                                     | String                     | `application/x-mpegURL` | N          |
| `type`                | The type of content                               | `ON-DEMAND`, `LIVE`, `DVR` | `ON-DEMAND`             | N          |
| `url`                 | The URL to be played                              | String                     | `https://...`           | Y          |
| `videoFragmentFormat` | The video fragment format                         | String                     | `FMP4`                  | N          |

### Subtitles

Describes external subtitles provided for the content.

| Key        | Description                                       | Format                                 | Examples      |
|------------|---------------------------------------------------|----------------------------------------|---------------|
| `kind`     | The kind of subtitles                             | `SUBTITLES`, `DESCRIPTION`, `CAPTIONS` | `SUBTITLES`   |
| `label`    | A label describing the subtitles                  | String                                 | `English`     |
| `language` | The language code                                 | String (BCP 47)                        | `en`          |
| `url`      | The URL where the subtitles can be loaded         | String                                 | `https://...` |

> [!INFO]
> All keys listed above are mandatory.

### Time range

Describes a time range associated with the content.

| Key         | Description              | Format                                          | Examples  |
|-------------|--------------------------|-------------------------------------------------|-----------|
| `endTime`   | The chapter's end time   | Time in milliseconds                            | `60000`   |
| `startTime` | The chapter's start time | Time in milliseconds                            | `50000`   |
| `type`      | The type of time range   | `BLOCKED`, `OPENING_CREDITS`, `CLOSING_CREDITS` | `BLOCKED` |

> [!INFO]
> All keys listed above are mandatory.

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
        "keySystem": "FAIRPLAY",
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
            "startTime": "50000",
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
