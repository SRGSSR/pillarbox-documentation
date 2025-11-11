# Monitoring Backend

## Introduction

The Pillarbox monitoring backend is responsible for processing, transforming, and storing events sent by the players. It
consists primarily of the Event Dispatcher and Data Transfer services, which work together to ensure that all events are
correctly streamed, transformed, and persisted in OpenSearch for analysis and visualization.

This document provides an overview of the backend architecture, the data flow between components, and the
transformations applied before events are stored. It serves as a central reference for support engineers and developers
troubleshooting or extending the system.

## Repositories

The Pillarbox monitoring backend is composed of several repositories, each responsible for a specific part of the
system:

| Repository                                                     | Description                                                                                         |
|----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| [pillarbox-monitoring-infra][pillarbox-monitoring-infra]       | Terraform and Docker Compose setup for deploying and running the backend services                   |
| [pillarbox-monitoring-transfer][pillarbox-monitoring-transfer] | Listens to events from the Event Dispatcher, applies transformations, and stores them in OpenSearch |
| [pillarbox-event-dispatcher][pillarbox-event-dispatcher]       | Receives events from Pillarbox players and streams them to the Data Transfer service via SSE        |

## Data Transformations

The Pillarbox backend enriches and transforms the raw data received from the players. These transformations include
adding contextual information such as device details, and error details, etc.

The purpose of these transformations is to make it easier for support engineers and analysts to troubleshoot, monitor,
and analyze the data. These transformations never remove or alter the original data; they only add new information to
the existing raw data.

Here is an exhaustive list of the fields added to each event:

### General Events

All events are enriched with a `user_ip` field by the `pillarbox-event-dispatcher` service:

| Key       | Description                                                                                                             | Format | Examples      |
|-----------|-------------------------------------------------------------------------------------------------------------------------|--------|---------------|
| `user_ip` | The resolved user IP, taken from the `X-Forwarded-For` header if available, otherwise from the client’s remote address. | String | `192.168.0.1` |

### Start Event `data`

The `pillarbox-monitoring-transfer` service enriches `START` events with additional contextual information to make
analysis, filtering, and troubleshooting easier. The following transformations are applied:

| Key                  | Description                                                                      | Format  | Examples / Notes       |
|----------------------|----------------------------------------------------------------------------------|---------|------------------------|
| `browser.name`       | Browser name extracted from user agent (web only).                               | String  | `Chrome`, `Firefox`    |
| `browser.version`    | Browser version extracted from user agent (web only).                            | String  | `117.0.5938.132`       |
| `device.type`[^1]    | Device type extracted from user agent (web only).                                | String  | `Smartphone`, `Tablet` |
| `device.model`[^1]   | Device model, either translated for iOS or extracted from user agent (web only). | String  | `iPhone 14`            |
| `os.name`[^1]        | Operating system name extracted from user agent (web only).                      | String  | `iOS`, `Windows`       |
| `os.version`[^1]     | Operating system version extracted from user agent (web only).                   | String  | `16.5.1`               |
| `robot`[^2]          | Flag indicating if the event is from a robot or automated client (web only).     | Boolean | `true`, `false`        |
| `embed`[^2]          | Flag indicating the content comes from an embedded origin (web only).            | Boolean | `true`, `false`        |
| `media.short_origin` | Shortened version of the origin URL (web only).                                  | String  | `rts.ch/sport`         |
| `media.bu`           | Business unit resolved from the media ID.                                        | String  | `RTS`                  |
| `media.type`         | Media type resolved from the media ID.                                           | String  | `video`, `audio`       |
| `media.swisstxt`     | Flag indicating if the media is managed by Swisstxt.                             | Boolean | `true`, `false`        |

[^1]: For native implementations of Pillarbox these fields are provided by the players themselves.

[^2]: The `robot` and `embed `flags are used to filter out non-human traffic and events not generated by our managed
applications. These filters are automatically applied to all dashboards in Grafana when using the default configured
aliases, so they do not need to be added to every query manually.

### Error Event `data`

The `pillarbox-monitoring-transfer` service enriches `ERROR` events with additional metadata to classify content
restrictions and playback errors. The following transformations are applied:

| Key              | Description                                                              | Format  | Examples / Notes                      |
|------------------|--------------------------------------------------------------------------|---------|---------------------------------------|
| `block_reason`   | Identifies the reason for a content restriction, when applicable.        | String  | `GEOBLOCK`, `LEGAL`                   |
| `business_error` | Flag indicating if the error is a known business or content restriction. | Boolean | `true`, `false`                       |
| `error_type`     | Categorized technical error type detected from logs or error names.      | String  | `PLAYBACK_NETWORK_ERROR`, `DRM_ERROR` |

#### Content Restriction Detection

Content restrictions (e.g., geoblocking or age-based restrictions) are detected by matching the error message against a
predefined set of localized phrases. When a match is found, the `block_reason` is set accordingly and `business_error`
is flagged as `true`. See [BlockReasonProcessor][BlockReasonProcessor]

#### Technical Error Classification

For all other `ERROR` events (those not flagged as `business_error`), a second layer of analysis determines the
`error_type` based on platform-specific data:

* **Web**: The log is scanned for known error patterns using regex. The most recent and highest-priority match
  determines the error type.
* **iOS**: The `name` field of the error is compared against known identifiers from the native player.
* **Android**: Both the `log` and `name` fields are analyzed. Regex and keyword matching are used to infer the most
  likely cause.

On Grafana Errors classified as `DRM_NOT_SUPPORTED` and `CONNECTION_ERROR` are excluded systematically from the
error/success rate calculations.

See: [ErrorProcessor][ErrorProcessor]

[pillarbox-monitoring-infra]: https://github.com/SRGSSR/pillarbox-monitoring-infra

[pillarbox-monitoring-transfer]: https://github.com/SRGSSR/pillarbox-monitoring-transfer

[pillarbox-event-dispatcher]: https://github.com/SRGSSR/pillarbox-event-dispatcher

[BlockReasonProcessor]: https://github.com/SRGSSR/pillarbox-monitoring-transfer/blob/5b0a79a11d5ff7fe0b7906c67e94d5eb4877108d/src/main/kotlin/ch/srgssr/pillarbox/monitoring/event/model/BlockReasonProcessor.kt

[ErrorProcessor]: https://github.com/SRGSSR/pillarbox-monitoring-transfer/blob/5b0a79a11d5ff7fe0b7906c67e94d5eb4877108d/src/main/kotlin/ch/srgssr/pillarbox/monitoring/event/model/ErrorProcessor.kt
