EmbyVision Credits – OCR Edition
Release v1.0.0.1
Intelligent end-credits detection for Emby using OCR, video analysis, audio fingerprinting, and automatic episode learning.

Core Detection Engines

PaddleOCR (deep-learning OCR with strong multi-language support) — CPU & GPU editions
Tesseract OCR (traditional OCR engine)
External OCR service support with configurable endpoint, timeout, and retries
OCR connection test with success feedback and service/edition identification
Black Frame Detection (anime-friendly and general-purpose)
Chromaprint / Audio Fingerprinting matching
Automatic Episode Learning / Comparison (narrows search windows using previously detected episodes)
Multi-method detection pipeline with configurable priorities and fallbacks

Detection Methods & Techniques

OCR-based keyword and text detection on video frames
Character density detection (with optional primary-method mode and false-positive filters)
Black-screen / black-frame analysis with density, duration, and scene-merge controls
Audio silence detection
Audio pattern detection
Video pattern detection
Scene-change detection
Keyword / text-region detection
Anime-specific detection modes and methods
Credit structure detection
Scrolling credits pattern detection
Fuzzy keyword matching
Region of Interest (ROI) targeting
Image preprocessing and sharpening for OCR
Adaptive frame rate and optimized predictive sampling
Smart frame skipping and early-stop on consecutive matches
Parallel frame/batch processing with configurable batch size and delays

Detection Modes & Configuration

Multiple detection modes (including Black Frame Only and hybrid modes)
Configurable search start (minutes from end, percentage, or absolute)
Frame rate, max frames, max analysis duration, and stop-seconds-from-end controls
Minimum confidence / match thresholds
JPEG quality and max resolution limits for OCR frames
Hardware acceleration support (CUDA/NVDEC, VAAPI, and software fallback)
Codec-aware hardware decoding (H.264, HEVC, AV1, VP9)
FFmpeg thread limits, process priority, and custom pre-input arguments
Parallel sessions for black-frame and Chromaprint paths
Episode comparison tolerance and minimum episode count
Timestamp offset adjustment
Skip detection if file is unchanged
Optional video validation with timeout
Disable detection globally when needed

Per-Series / Per-Library Rules

Custom detection rules for series names, tags, studios, and libraries
Priority-based matching (series > tags/studios > library)
Override global settings on a per-rule basis
Full rule management UI (create, edit, reorder, delete)

Manual Detection & Processing

Process selected series / seasons / episodes
Queue all shows
Dry Run and Dry Run Debug modes
Skip existing credit markers option
Retry failed episodes
Live progress monitoring (processed, skipped, failed counts)
Cancel processing and clear queue
View and manage chapter markers
Season validation tools
Apply detected timestamps to an entire season
Add timestamps from dry-run results
Clear failure markers (per-series or all)

Auto Skip Integration

Works with Emby’s auto-skip / chapter marker system
Per-series Auto Skip exclusions list
Select-all / deselect-all controls for exclusions
Optional use of embedded chapter markers (scheduled and manual)

Markers, Chapters & Thumbnails

Creates Emby-compatible credits chapter markers
Thumbnail generation for detected credit points (configurable width & quality)
Marker update / edit API and UI support
Batch update missing markers for seasons

Backup & Restore

Export full credits markers backup (JSON)
Import backup with optional overwrite of existing markers
Bulk export series to folder
Per-series export / import
Scheduled backup task
Configurable backup folder and maximum number of scheduled backups
Optional auto-backup after detection
Optional auto-restore after library scan
Dedicated backup export/import progress tracking

Scheduled Tasks

Detect Credits in TV Shows — library-wide scheduled detection
Backup Credits Markers — automated marker backups
Only process new episodes option
Prevent concurrent plugin processing
Configurable notification on scheduled task completion

Tracer & Diagnostics

Tracer mode for monitoring detection activity
Lists of detected, failed, and pending episodes
Dismiss individual tracer entries
Clear detected / failed / all tracer lists
Debug logging and debug log retrieval
Memory usage reporting
Detailed failure tracking and retry support

Notifications

Notifications on scheduled detection completion
Notifications on auto-detection events
Success-only notification option
Minimum episode count threshold for notifications
Integrates with Emby’s notification system

User Interface

Dedicated plugin configuration pages:
Manual Detection
Settings
Rules
Notifications
OCR Enhancements
Auto Skip
Setup Guide
API documentation
Tracer

Collapsible, organized settings sections
Real-time progress and status indicators
Test Connection button for OCR service
Setup guide with Docker / Docker Compose examples for CPU and GPU OCR editions
API reference for all plugin endpoints

API Surface

Full REST-style API under /CreditsDetector/...
Start/stop detection, queue management, progress endpoints
Dry-run and debug endpoints
Marker CRUD and season apply
Backup export/import and bulk export
Tracer management
Failure marker management
Thumbnail and image serving
Detection methods, results, and history endpoints
Memory usage and configuration endpoints

Performance & Reliability

Configurable timeouts and retries for OCR requests
Parallel processing with rate limiting between batches
Hardware acceleration with automatic software fallback
Skip unchanged files to avoid redundant work
Episode-learning cache for faster subsequent detections
Process priority controls for Chromaprint / heavy workloads
Robust JSON response handling across different OCR server formats

Compatibility & Packaging

Emby Server plugin (net6.0)
Works with external PaddleOCR (CPU/GPU) and Tesseract services
Local-network and localhost OCR endpoint support
Version 1.0.0.1 (OCR Edition)


EmbyVision Credits v1.0.0.1 delivers a complete, production-ready credits detection solution for Emby — combining modern OCR, classical video/audio analysis, intelligent learning, full library automation, and deep Emby integration.
