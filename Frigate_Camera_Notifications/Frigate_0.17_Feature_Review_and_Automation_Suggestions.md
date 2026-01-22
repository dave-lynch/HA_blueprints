# Frigate 0.17 Feature Review and Automation Suggestions

This document summarizes Frigate 0.17 features (from the v0.17.0-beta2
release notes) and highlights automation/blueprint opportunities for
Home Assistant users.

## Feature review (0.17 highlights with automation impact)

- Classification model training
  - State classification: train state changes for regions (e.g., gate
    open/closed, door open/closed).
  - Object classification: train sub-labels for objects (e.g., "Fido",
    "hard-hat", "delivery uniform").
- GenAI enhancements
  - Review item summaries (title/description + dangerous/suspicious/normal).
  - Object descriptions now apply to reviews.
  - GenAI can be toggled per camera via MQTT.
- Semantic search triggers
  - Trigger actions when tracked objects match an image or text description
    above a similarity threshold.
- Audio transcription and analysis
  - Local transcription for speech events; "speech" events are available.
- Review item behavior improvements
  - Smarter stationary object tracking.
  - Improved loitering logic per object type.
  - Severity-based review cutoff (alerts end independently of ongoing detections).
- MQTT changes
  - Dedicated camera health/status topics.
- Configuration and UI changes that affect automation UX
  - More settings can be saved without restart (cameras, zones, masks).
  - Debug view moved to Live view settings.
  - Config safe mode for invalid configs.

## Upgrade notes that can impact automations

- GenAI config fields moved under objects -> genai.
- Recordings retention is now fully tiered (continuous vs motion).
- strftime_fmt is removed; date formatting is UI language driven.
- go2rtc exec/expr/echo sources are disabled by default (security hardening).
- Auto detect resolution changes may require explicit detect width/height.

## Automation/blueprint suggestions

1. Classification-aware routing and filters
   - Add inputs to filter on state classification results (gate open/closed).
   - Surface object classification sub-labels in title/message and allow
     allowlist/denylist filters.
2. Review summary-driven notifications
   - Use GenAI review summary title/description as the notification content.
   - Map dangerous/suspicious/normal to channels, critical alerts, or
     escalation rules.
3. Semantic-search trigger bridge
   - Provide a template to consume Frigate semantic triggers and map
     matched descriptions to HA actions (lights, sirens, snapshots).
4. Audio event integration
   - Add support for speech/audio events and include transcripts in
     notifications, with separate cooldowns.
5. Camera health watchdogs
   - Use camera health/status MQTT to notify on offline roles or degraded
     streams, and optionally suppress camera notifications while offline.
6. GenAI cost/performance controls
   - Provide an automation snippet to toggle GenAI per camera via MQTT
     (night-only, away-only, or load-based).
7. LPR workflows
   - Pair normalized plates with allowlists (open gate, unlock door) and
     blocklists (critical alerts).
8. Dedupe improvements using review IDs
   - Prefer review_id for grouping and update existing notifications rather
     than sending new ones; apply severity-based cooldowns.
9. Documentation updates for 0.17 UI
   - Highlight the new Debug view location, safe-mode behavior, and the
     add-camera wizard steps.

## Open questions / validation items

- Confirm the exact MQTT payload fields for review summaries, semantic
  triggers, and camera health topics.
- Verify which fields are available in HA Frigate integration events vs raw
  MQTT topics for reliable templating.
