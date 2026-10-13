# Google Cast specification

Pillarbox cast receiver and sender can handle chapters if they both implements the specification. 

The chapters are stored into `MediaTrack` object with the following properties:
- Id: `6000`
- Type: `MediaTrack.TYPE_TEXT`
- SubType: `MediaTrack.SUBTYPE_CHAPTERS`
- ContentType: `pillarbox/chapters`


## Chapter format

| Key          | Description                                 | Format                                                          | Examples                               |
|--------------|---------------------------------------------|-----------------------------------------------------------------|----------------------------------------|
| `id`       | A unique identifier of the chapter              | String                                                 | `urn:rts:video:1234`                              |
| `start` | The start position of the chapter in milliseconds                       | Number                               | `0`
| `end` | The end position of the chapter in milliseconds         |  Number | `134789` |
| `title`  | The title of the chapter | String         | `Chapter title`                        |
| `imageUrl`    | The imagae url related to the chapter             | String                                                          |    `https://image.com/image.png`                                   |

## Chapters format

The json that is set to the `MediaTrack` that is a JSON object containing a list of chapter.

| Key          | Description                                 | Format                                                          | Examples                               |
|--------------|---------------------------------------------|-----------------------------------------------------------------|----------------------------------------|
| `chapters`       | List of chapters             | JSON array                                                 | `[{...}, {...}]`                              |

### Sample

```json
{
    "chapters": [
       {
        "id": "urn:rts:video:1234",
        "start": 0,
        "end": 30000,
        "title": "Chapter title 1",
        "imageUrl": "https://image.png",
       },
        {
        "id": "urn:rts:video:12346",
        "start": 30000,
        "end": 60000,
        "title": "Chapter title 2",
        "imageUrl": "https://image.png",
       }                
    ],
}
```