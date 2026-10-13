# Google Cast Specification

---

**Version:** 1.0 |
**Date:** October 13, 2026

---

## Introduction

SRG SSR Google Cast receivers provide useful metadata, including:

- Blocked time ranges
- Chapters
- Credits

This metadata is stored in the `customData` field of the Google Cast `MediaInfo` object contained in the `MediaQueueItem`.

Receivers should also ensure that the corresponding `MediaInfo` in `MediaStatus` is kept up to date.

> [!INFO]
> The format is compatible with [Pillarbox's standard metadata format](/specifications/standard-connector/METADATA.md) and can be parsed using the same code.

## JSON Format

The SRG SSR Google Cast metadata format is made of the following top-level structure:

| Key               | Description                                | Mandatory | Format                                        | Examples  |
|-------------------|--------------------------------------------|-----------|-----------------------------------------------|-----------|
| `blockedSegments` | Blocked segments                           | No        | JSON array (of _Blocked segments_, see below) | `[ ... ]` |
| `chapters`        | Chapters                                   | No        | JSON array (of _Chapters_, see below)         | `[ ... ]` |
| `timeRanges`      | Time ranges                                | No        | JSON array (of _Time range_, see below)       | `[ ... ]` |

Objects appearing as JSON arrays are described below.

### Blocked segment

Describes a blocked segment associated with the content.

| Key           | Description                           | Mandatory | Format                                          | Examples           |
|---------------|---------------------------------------|-----------|-------------------------------------------------|--------------------|
| `blockReason` | The reason why the segment is blocked | Yes       | `LEGAL`, `COMMERCIAL`                           | `LEGAL`            |
| `endTime`     | The segment's end time                | Yes       | Time in milliseconds                            | `60000`            |
| `identifier`  | A unique identifier                   | Yes       | String                                          | `forbidden_intro`  |
| `startTime`   | The segment's start time              | Yes       | Time in milliseconds                            | `50000`            |

> [!INFO]
> Blocked segments are also provided as time ranges, but with less metadata than in Pillarbox’s standard metadata format.

## Chapter format

Describes a chapter associated with the content.

| Key          | Description                                | Mandatory | Format               | Examples            |
|--------------|--------------------------------------------|-----------|----------------------|---------------------|
| `endTime`    | The chapter's end time                     | Yes       | Time in milliseconds | `30000`             |
| `identifier` | A unique identifier                        | No        | String               | `hill_valley_1955`  |
| `posterUrl`  | The URL of an image describing the chapter | No        | String               | `https://...`       |
| `startTime`  | The chapter's start time                   | Yes       | Time in milliseconds | `10000`             |
| `title`      | A title describing the chapter             | Yes       | String               | `Hill Valley, 1955` |

### Time range

Describes a time range associated with the content.

| Key         | Description              | Mandatory | Format                                          | Examples  |
|-------------|--------------------------|-----------|-------------------------------------------------|-----------|
| `endTime`   | The chapter's end time   | Yes       | Time in milliseconds                            | `60000`   |
| `startTime` | The chapter's start time | Yes       | Time in milliseconds                            | `50000`   |
| `type`      | The type of time range   | Yes       | `BLOCKED`, `OPENING_CREDITS`, `CLOSING_CREDITS` | `BLOCKED` |

### CustomData sample

```json
{
    "blockedSegments": [
        {
            "blockReason": "LEGAL",
            "endTime": 60000,
            "identifier": "forbidden_intro",
            "startTime": 50000
        }
    ],
    "chapters": [
        {
            "endTime": 30000,
            "identifier": "hill_valley_1955",
            "posterUrl": "https://...",
            "startTime": 10000,
            "title": "Hill Valley, 1955"
        }
    ],
    "timeRanges": [
        {
            "endTime": 60000,
            "startTime": 50000,
            "type": "BLOCKED"
        }
    ]
}
```

## JSON Schema

[metadata-schema.json](specifications/cast/schemas/metadata-schema.json ':ignore')
