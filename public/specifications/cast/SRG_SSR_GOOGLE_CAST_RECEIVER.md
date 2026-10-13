# Google Cast Specification

---

**Version:** 1.0 |
**Date:** October 13, 2026

---

## Introduction

Cast receivers and senders can handle custom Pillarbox metadata, including:

- Chapters
- Credits
- Blocked time ranges

This is supported when both the sender and receiver implement the same metadata specification. The metadata is stored in the `customData` field of the Google Cast `MediaInfo` object contained in the `MediaQueueItem`.

Receivers should also ensure that the corresponding `MediaInfo` in `MediaStatus` is kept up to date.

## Chapter format

| Key           | Description                                       | Format | Examples                       |
|---------------|---------------------------------------------------|--------|--------------------------------|
| `id`          | A unique identifier of the chapter                | String | `urn:rts:video:1234`           |
| `start`       | The start position of the chapter in milliseconds | Number | `0`                            |
| `end`         | The end position of the chapter in milliseconds   | Number | `134789`                       |
| `title`       | The title of the chapter                          | String | `Chapter title`                |
| `description` | An optional description of the chapter            | String | `The description of a chapter` |
| `imageUrl`    | The image url related to the chapter              | String | `https://image.com/image.png`  |

## Credit format

| Key     | Description                                      | Format                           | Examples        |
|---------|--------------------------------------------------|----------------------------------|-----------------|
| `type`  | The type of the credit                           | `OpeningCredit`, `ClosingCredit` | `ClosingCredit` |
| `start` | The start position of the credit in milliseconds | Number                           | `0`             |
| `end`   | The end position of the credit in milliseconds   | Number                           | `134789`        |

## Blocked time range format

| Key      | Description                                      | Format | Examples              |
|----------|--------------------------------------------------|--------|-----------------------|
| `start`  | The start position of the credit in milliseconds | Number | `0`                   |
| `end`    | The end position of the credit in milliseconds   | Number | `134789`              |
| `reason` | An optional string describing the reason         | String | `GEOBLOCK`            |
| `id`     | An optional unique identifier                    | String | `urn:srf:video:12345` |

## Pillarbox metadata

The json to set into `MediaInfo.customData`.

| Key                 | Description                 | Format     | Examples         |
|---------------------|-----------------------------|------------|------------------|
| `chapters`          | List of chapters            | JSON array | `[{...}, {...}]` |
| `credits`           | List of credits             | JSON array | `[{...}, {...}]` |
| `blockedTimeRanges` | List of blocked time ranges | JSON array | `[{...}, {...}]` |

### CustomData sample

```json
{
    "pillarbox": {
        "chapters": [
            {
                "id": "urn:rts:video:1234",
                "start": 0,
                "end": 30000,
                "title": "Chapter title 1",
                "description": "The description of the chapter 1",
                "imageUrl": "https://image.png"
            },
            {
                "id": "urn:rts:video:12346",
                "start": 30000,
                "end": 60000,
                "title": "Chapter title 2",
                "imageUrl": "https://image.png"
            }
        ],
        "credits": [
            {
                "type": "OpeningCredit",
                "start": 10000,
                "end": 20000
            },
            {
                "type": "ClosingCredit",
                "start": 880000,
                "end": 900000
            }
        ],
        "blockedTimeRanges": [
            {
                "start": 4000,
                "end": 8000,
                "reason": "GEOBLOCK",
                "id": "urn:rts:video:89465389"
            },
            {
                "start": 156000,
                "end": 160000
            }
        ]
    }
}
```
