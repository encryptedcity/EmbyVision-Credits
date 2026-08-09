# EmbyVision Credits

**AI-powered end credits detection for Emby**

EmbyVision Credits is an advanced end-credits detection plugin for **Emby Server**. It analyzes TV episodes to determine where end credits begin and creates Emby chapter markers at the detected credit point, allowing Emby to use its chapter/Auto Skip functionality.

EmbyVision Credits combines multiple detection techniques, including OCR, black-frame analysis, audio analysis, video-pattern analysis, character-density analysis, fuzzy keyword matching, episode comparison, and adaptive sampling.

**Current Edition:** OCR Edition
**Plugin Version:** 1.0.0.1
**Target Framework:** .NET 6
**Platform:** Emby Server

---

## Features

### OCR Detection

EmbyVision Credits supports external OCR processing through the EncryptedCity EmbyVision OCR service.

Supported OCR configurations include:

* **PaddleOCR CPU**
* **PaddleOCR GPU**
* OCR confidence filtering
* OCR keyword detection
* Fuzzy keyword matching
* Region-of-interest processing
* Image preprocessing
* Sharpening
* Adaptive sampling
* Smart frame skipping
* Consecutive-match detection
* Early stopping
* Configurable OCR timeouts and retries

The OCR engine runs as a separate service and is accessed by the plugin through HTTP.

This keeps the heavy OCR workload outside of the Emby plugin process.

---

## Detection Engines

EmbyVision Credits uses multiple detection systems that can work independently or together.

### OCR

Detects credit text directly from video frames.

Supported external OCR service:

**EncryptedCity EmbyVision OCR**

* CPU Edition
* GPU Edition
* PaddleOCR
* HTTP API

### Black Frame Detection

Analyzes video frames for black or near-black sections commonly associated with the end of an episode.

This can be particularly useful for anime and shows with distinctive credit transitions.

### Audio Analysis

Uses audio characteristics and fingerprinting to help identify recurring credit sequences.

### Character Density Detection

Analyzes the amount and distribution of visible text in frames.

This can help identify scrolling or dense credit sequences.

### Video Pattern Detection

Uses video characteristics such as:

* Scene changes
* Frame patterns
* Credit transitions
* Visual changes
* Repeated sequences
* Scrolling-credit behavior

### Episode Comparison

Previously processed episodes can be used to help narrow the expected credit region for subsequent episodes.

This can significantly reduce unnecessary scanning.

---

# How Detection Works

EmbyVision Credits does not depend on a single detection technique.

Depending on the configuration, the plugin can combine multiple signals such as:

1. OCR text recognition
2. Credit-related keywords
3. Character density
4. Black-frame analysis
5. Audio characteristics
6. Video/scene patterns
7. Episode comparison
8. Confidence scoring
9. Consecutive frame confirmation
10. Predictive/adaptive sampling

The goal is to determine a reliable credit-start position rather than simply reacting to one frame.

---

# Emby Integration

When credits are detected, EmbyVision Credits can create an Emby-compatible chapter marker at the detected location.

This allows Emby to identify the credits and use its available chapter/Auto Skip functionality.

Features include:

* Automatic credit chapter creation
* Manual detection
* Scheduled detection
* Library-wide processing
* Season processing
* Episode processing
* Existing-marker detection
* Optional marker validation
* Thumbnail generation
* Marker backup
* Marker restore
* Per-series rules
* Library rules
* Studio rules
* Tag rules
* Auto Skip exclusions

---

# Requirements

## Emby Server

A recent Emby Server version with plugin support is required.

The plugin targets:

**.NET 6**

## OCR

OCR is provided by a separate external service.

Recommended:

**EncryptedCity EmbyVision OCR**

Available configurations:

* CPU Edition
* GPU Edition

The CPU edition does not require an NVIDIA GPU.

The GPU edition requires compatible NVIDIA drivers and NVIDIA Container Toolkit support on the Docker host.

---

# Architecture

EmbyVision Credits and the OCR service are separate components.

```text
┌──────────────────────────────┐
│         Emby Server          │
│                              │
│   EmbyVision Credits Plugin  │
└──────────────┬───────────────┘
               │
               │ HTTP OCR API
               │
       ┌───────▼────────┐
       │ EncryptedCity  │
       │ EmbyVision OCR │
       └───────┬────────┘
               │
        ┌──────┴──────┐
        │             │
     CPU OCR       GPU OCR
     Port 8000     Port 8001
```

The plugin performs the detection workflow while the OCR service performs the OCR processing.

---

# Installation

## 1. Install EmbyVision Credits

Download the latest plugin release from the repository's **Releases** section.

The plugin is distributed as:

```text
EmbyVisionCredits.dll
```

Copy the DLL into the Emby plugins directory.

### Windows

The usual Emby plugin location is:

```text
%AppData%\Emby-Server\programdata\plugins
```

### Linux

Depending on the Emby installation:

```text
/config/plugins
```

### Docker

If your Emby container maps its configuration directory to a host path, place the plugin in:

```text
/config/plugins
```

inside the Emby container.

For example, if your host maps:

```text
/mnt/user/appdata/emby
```

to:

```text
/config
```

then the plugin would normally be placed under:

```text
/mnt/user/appdata/emby/plugins
```

Restart Emby after installing or replacing the plugin DLL.

Then open:

**Emby Dashboard → Plugins**

and verify that **EmbyVision Credits** is installed.

---

# 2. Install EmbyVision OCR

EmbyVision Credits uses an external OCR service for PaddleOCR processing.

The OCR service is maintained separately from the Emby plugin.

There are two supported configurations.

## CPU Edition

The CPU service listens on:

```text
8000
```

Example:

```text
http://YOUR-OCR-SERVER:8000
```

The CPU edition does not require an NVIDIA GPU.

---

## GPU Edition

The GPU service listens on:

```text
8001
```

Example:

```text
http://YOUR-OCR-SERVER:8001
```

The GPU edition uses PaddleOCR with CUDA acceleration.

GPU operation requires:

* NVIDIA GPU
* Compatible NVIDIA driver
* NVIDIA Container Toolkit
* Docker configured for NVIDIA GPU access

The OCR service repository contains the current Docker configuration and image/build instructions.

**Do not use an old OCR image name from an outdated README. Use the image and instructions published by the current EncryptedCity EmbyVision OCR repository.**

---

# 3. Configure OCR in EmbyVision Credits

Open:

**Emby Dashboard → Plugins → EmbyVision Credits**

Open the plugin configuration page.

Find the OCR service configuration.

Select:

```text
OCR Engine: PaddleOCR
```

Then configure the appropriate endpoint.

### CPU

```text
http://YOUR-OCR-SERVER:8000
```

### GPU

```text
http://YOUR-OCR-SERVER:8001
```

If the OCR service is running on the same machine as Emby:

```text
http://localhost:8000
```

or:

```text
http://localhost:8001
```

If Emby is running inside Docker and the OCR service is another container, use the appropriate Docker network/container hostname rather than assuming `localhost` refers to the OCR container.

---

# 4. Test the Connection

After configuring the endpoint, use:

**Test Connection**

The plugin should contact the OCR service and report whether the service is available.

If the connection fails, verify:

* OCR container is running
* Correct IP address
* Correct port
* Docker networking
* Firewall rules
* NVIDIA container configuration when using GPU OCR
* OCR service logs
* Emby server logs

---

# Quick Start

Once the plugin and OCR service are installed:

1. Open **EmbyVision Credits**.
2. Configure the OCR service.
3. Run **Test Connection**.
4. Open **Manual Detection**.
5. Select a series, season, or episode.
6. Run a **Dry Run** first if you want to inspect the detection without applying markers.
7. Review the results.
8. Run the actual detection.
9. Check the episode's chapters in Emby.

For automated processing, configure the scheduled detection task.

---

# Manual Detection

The Manual Detection page provides tools for testing and processing your library.

Depending on the configured version/options, you can process:

* Individual episodes
* Seasons
* Series
* Multiple selections
* Entire libraries

Additional controls include:

* Dry Run
* Dry Run Debug
* Skip existing markers
* Queue processing
* Cancel processing
* Progress monitoring

---

# Automatic Detection

EmbyVision Credits can process episodes automatically using Emby's scheduled-task system.

Automatic detection can be configured to process your library without manually starting each detection job.

The plugin also provides configurable detection rules so different libraries or series can use different behavior.

---

# Detection Rules

Rules can be configured for different parts of a media library.

Supported rule types include:

* Series
* Tags
* Studios
* Libraries

Rules can be used to customize detection behavior for specific content.

This is useful when one series requires different detection settings from another.

---

# Anime Detection

Anime can have very different credit structures from traditional live-action television.

EmbyVision Credits therefore includes detection options specifically useful for anime, including:

* Black-frame analysis
* Character-density analysis
* OCR
* Credit keyword detection
* Adaptive sampling
* Video-pattern analysis

These methods can be combined rather than relying exclusively on OCR.

---

# Episode Learning

Episode comparison allows previously processed episodes to help guide future detections.

For example, if earlier episodes in a season consistently place credits within a particular portion of the runtime, later episodes can use that information to reduce unnecessary scanning.

This can improve both detection speed and consistency.

---

# Performance

EmbyVision Credits includes several performance controls.

These can include:

* Frame sampling rate
* Maximum frames
* Search start position
* Batch processing
* Processing delays
* Parallel processing
* Smart frame skipping
* Early stopping
* Episode comparison
* Hardware acceleration
* OCR service selection

The best configuration depends on the media library and hardware.

---

# GPU Acceleration

GPU acceleration is primarily relevant to the external PaddleOCR GPU service and FFmpeg/video processing.

The OCR GPU service uses CUDA when properly configured.

The plugin can also use available FFmpeg hardware acceleration where supported by the host and the selected configuration.

GPU OCR and video decoding are separate workloads.

A system does not necessarily require a second GPU; whether a single GPU can handle both workloads depends on GPU memory, workload, video codec, resolution, concurrent processing, and the amount of other GPU work occurring on the server.

---

# OCR Service Ports

The current EncryptedCity OCR configuration uses:

| Service            |     Port | Accelerator |
| ------------------ | -------: | ----------- |
| EmbyVision OCR CPU | **8000** | CPU         |
| EmbyVision OCR GPU | **8001** | NVIDIA CUDA |

Older port references should not be used.

---

# Configuration

The plugin configuration contains controls for areas including:

### OCR Service

* OCR engine
* OCR endpoint
* Connection testing
* Timeout
* Retry behavior

### OCR Detection

* Search start
* Frame rate
* Maximum frames
* Confidence
* Keywords
* Fuzzy matching
* ROI
* Image preprocessing

### Episode Comparison

* Previous-episode learning
* Search-window optimization
* Detection guidance

### Character Density

* Density detection
* Thresholds
* Filtering
* False-positive reduction

### Black Frame Detection

* Black-frame threshold
* Duration
* Density
* Processing options
* Anime-oriented settings

### Audio Detection

* Audio analysis
* Fingerprinting
* Similarity thresholds
* Fallback behavior

### Hardware Acceleration

* FFmpeg hardware acceleration
* NVIDIA/CUDA acceleration
* NVDEC where supported
* Software fallback
* Threading

### Automation

* Scheduled detection
* Automatic processing
* Queue management
* Existing-marker handling

### Rules

* Series rules
* Tag rules
* Studio rules
* Library rules

### Backup

* Marker export
* Marker import
* Bulk export
* Scheduled backups

### Tracer

* Detection results
* Failed episodes
* Debug information
* Processing diagnostics

---

# Backup and Restore

EmbyVision Credits includes marker backup functionality.

Markers can be exported so they can be restored later or transferred as part of library management.

Available functionality includes:

* Marker export
* Marker import
* Bulk export
* Scheduled backups
* Restore operations

Use the backup tools before making large-scale changes to detection settings or reprocessing a large library.

---

# Tracer

Tracer provides diagnostic information about detection attempts.

It can be used to investigate:

* Successful detections
* Failed detections
* Detection decisions
* OCR results
* Detection timing
* Processing problems

When reporting a detection problem, Tracer output together with the relevant Emby and OCR logs is extremely useful.

---

# API

EmbyVision Credits exposes plugin endpoints under:

```text
/CreditsDetector/
```

The API supports functionality including:

* Starting detection
* Cancelling detection
* Queue management
* Dry runs
* Progress monitoring
* Marker operations
* Season operations
* Backup/export
* Import
* Tracer operations
* OCR connection testing
* Thumbnail/debug functionality

The plugin's **API** tab contains the current endpoint documentation.

Because the API can change between plugin versions, the API tab in the installed version should be treated as the authoritative reference.

---

# Troubleshooting

## OCR connection fails

Check:

1. OCR container is running.
2. Correct server IP/hostname is configured.
3. Correct port is configured.
4. Port **8000** is being used for CPU OCR.
5. Port **8001** is being used for GPU OCR.
6. Docker networking allows the Emby container to reach the OCR container.
7. Firewall rules allow the connection.
8. Check OCR container logs.
9. Check Emby server logs.

---

## GPU OCR fails

Check:

* NVIDIA driver
* NVIDIA Container Toolkit
* GPU visibility inside Docker
* GPU memory availability
* OCR container logs
* PaddleOCR initialization

A GPU OCR container can start successfully while still failing when an OCR request requires more GPU memory than is available.

---

## Detection finds no credits

Try:

* Running a Dry Run
* Running Dry Run Debug
* Checking Tracer
* Checking OCR results
* Reviewing credit keywords
* Adjusting the search start
* Adjusting frame sampling
* Enabling episode comparison
* Enabling black-frame detection
* Checking the media's video/audio characteristics

Different shows can require different detection strategies.

---

## Anime credits are missed

Try enabling or increasing the importance of:

* Black-frame detection
* Character-density detection
* OCR
* Anime detection options
* Video-pattern detection

Anime credit sequences do not always behave like conventional live-action credits.

---

## Detection is slow

Possible improvements include:

* Enable episode comparison
* Reduce unnecessary frame sampling
* Use smart frame skipping
* Use GPU OCR when appropriate
* Enable supported FFmpeg hardware acceleration
* Adjust parallel processing
* Avoid unnecessarily large OCR search ranges

Actual performance depends heavily on the media, CPU, GPU, storage, and OCR configuration.

---

## Plugin does not appear in Emby

Check:

* DLL is in the correct plugins directory
* Emby was restarted
* DLL is compatible with the installed Emby version
* Plugin dependencies are present
* Emby server logs for plugin-loading errors

---

# Building From Source

Clone the EncryptedCity EmbyVision Credits repository:

```bash
git clone https://github.com/encryptedcity/EmbyVision-Credits.git
cd EmbyVision-Credits
```

Build the plugin:

```bash
dotnet build -c Release
```

The plugin targets:

```text
net6.0
```

The resulting DLL will be located under the project's build output directory, normally:

```text
bin/Release/net6.0/
```

The exact output path can vary depending on the project configuration.

---

# Development Requirements

To build the plugin from source you need:

* .NET 6 SDK
* Git
* A compatible development environment
* Emby development/plugin dependencies used by the project

The project is written primarily in C# with the Emby configuration interface using web technologies.

---

# Related Repository

## EmbyVision OCR

EmbyVision OCR is the separate OCR service used by EmbyVision Credits.

It provides the external PaddleOCR service used by the plugin.

The current service supports:

```text
CPU Edition → Port 8000
GPU Edition → Port 8001
```

See the **EncryptedCity EmbyVision OCR repository** for:

* Docker instructions
* CPU image
* GPU image
* PaddleOCR configuration
* NVIDIA requirements
* Container configuration
* API information
* Troubleshooting

---

# Project Structure

The EmbyVision Credits source contains the plugin, detection services, configuration interface, scheduled processing, API functionality, and supporting components.

A simplified structure is:

```text
EmbyVision-Credits/
├── EmbyVisionCredits/
├── EmbyVisionCredits.Api/
├── EmbyVisionCredits.Services/
│   └── DetectionMethods/
├── EmbyVisionCredits.ScheduledTasks/
├── EmbyVisionCredits.Configuration/
├── EmbyVisionCredits.csproj
└── README.md
```

The exact directory structure can change as development continues.

---

# Version

| Field            | Value                                |
| ---------------- | ------------------------------------ |
| Plugin           | **EmbyVision Credits**               |
| Edition          | **OCR Edition**                      |
| Version          | **1.0.0.1**                          |
| Target Framework | **.NET 6 / net6.0**                  |
| OCR              | **PaddleOCR / external OCR service** |
| CPU OCR Port     | **8000**                             |
| GPU OCR Port     | **8001**                             |

---

# License

This project is currently distributed without a license file unless one is included in the repository.

Until an explicit license is added, the source code remains **all rights reserved**.

Do not add an MIT, GPL, or other open-source license to documentation unless the repository actually contains that license and the author has chosen to release the project under it.

---

# Credits

**EmbyVision Credits**
EncryptedCity

OCR:

**PaddleOCR**

OCR service:

**EncryptedCity EmbyVision OCR**

Audio fingerprinting:

**Chromaprint**

Media server:

**Emby Server**

---

# Support and Bug Reports

Before reporting a problem, collect:

* Emby Server version
* EmbyVision Credits version
* Operating system
* CPU
* GPU, if applicable
* OCR edition being used
* OCR endpoint/port
* Detection method/settings
* Relevant Emby log entries
* OCR container logs
* Tracer/Dry Run Debug results

For OCR problems, also specify whether the service is:

```text
CPU → 8000
```

or:

```text
GPU → 8001
```

This information makes it much easier to determine whether a problem is in EmbyVision Credits, FFmpeg/media processing, or the external OCR service.

---

# About EmbyVision Credits

EmbyVision Credits is designed to provide intelligent, configurable end-credit detection for Emby libraries without relying on a single detection technique.

By combining OCR, video analysis, audio analysis, character-density analysis, episode comparison, and configurable detection rules, the plugin can adapt to a wide variety of television content, including both live-action and anime.

**EmbyVision Credits — intelligent end-credits detection for Emby.**

**Created by EncryptedCity.**
