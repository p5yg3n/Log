# Godot 4 Advanced Logger Utility (Log)

A high-performance, thread-safe, and feature-rich logging utility for Godot 4. Designed with static methods for clean global access (`Log.info("Tag", "Message")`), it features rich console formatting, file persistence with automatic rotation, and flexible category filtering.

---

## ✨ Features

* **Global Static API:** Clean, intuitive syntax (`Log.info("Player", "Jumped!")`) accessible from anywhere in your project without needing references or instantiation.
* **Severity Levels:** Supports `DEBUG`, `INFO`, `WARN`, `ERROR`, and `FATAL` tiers with custom verbosity control (`min_level`).
* **Thread-Safe:** Fully protected by a `Mutex`, ensuring safe logging even if you run tasks across multiple threads.
* **Rich Console Formatting:** Automatically color-codes output in the Godot editor console using `print_rich` based on severity.
* **File Logging & Auto-Rotation:** Optional persistence to `user://debug.log` with built-in file size management that automatically rotates old logs to `.old` backups.
* **Category Allowlists & Blocklists:** Easily isolate or mute specific systems (e.g., isolate `"Physics"` or mute noisy `"Audio"` logs).
* **Safe Fatal Crash Handling:** Automatically flushes logs, closes files, and safely triggers an engine crash or editor assertion on fatal errors.

---

## 📦 Installation & Setup

1. Create a folder in your project directory (e.g., `res://scripts/utils/`).
2. Add the core script file: `log.gd`.

---

## 🚀 Usage Guide

### 1. Basic Logging

Call the logger globally from any script using a category string and your message:

```gdscript
Log.debug("Physics", "Raycast hit ground at position: %s" % hit_pos)
Log.info("GameManager", "Level 1 loaded successfully.")
Log.warn("Inventory", "Attempted to add item to a full slot.")
Log.error("Network", "Failed to connect to server: Timeout.")

# Fatal will log, close files, and crash/assert the application
if not critical_resource:
	Log.fatal("Core", "Missing critical config resource!")

```

### 2. Enabling File Logging

By default, logging to a file is disabled to keep disk writes minimal during development. You can enable it globally (e.g., in an AutoLoad script or your main scene's `_ready()`):

```gdscript
func _ready() -> void:
	Log.write_to_file = true
	Log.log_file_path = "user://game_session.log"
	Log.max_file_size_bytes = 5 * 1024 * 1024 # 5 MB limit before rotation

```

### 3. Filtering Categories (Allowlist & Blocklist)

If you are debugging a specific subsystem, you can restrict logs to only show specific categories, or mute ones that are spamming your console:

```gdscript
# ONLY show logs from these categories (Allowlist)
Log.enable_category("Combat")
Log.enable_category("AI")

# OR mute specific noisy categories (Blocklist)
Log.mute_category("Audio")
Log.mute_category("Particles")

```

---

## 📚 Script Reference

### `Log.gd`

The global static logging utility class.

| Property | Type | Default | Description |
| --- | --- | --- | --- |
| `min_level` | `Level` | `Level.DEBUG` | Minimum severity level required to trigger output. |
| `write_to_file` | `bool` | `false` | When `true`, duplicates log messages to a persistent file. |
| `log_file_path` | `String` | `"user://debug.log"` | Target path for file logging. |
| `max_file_size_bytes` | `int` | `2 * 1024 * 1024` | Maximum file size (in bytes) before triggering a log rotation. |

| Method | Description |
| --- | --- |
| `Log.debug(cat, msg)` | Logs a low-priority debug message. |
| `Log.info(cat, msg)` | Logs standard runtime information. |
| `Log.warn(cat, msg)` | Logs a warning and pushes a yellow alert to the editor console. |
| `Log.error(cat, msg)` | Logs an error and pushes a red alert to the editor console. |
| `Log.fatal(cat, msg)` | Logs a critical failure, flushes files, and triggers a crash/assert. |
| `Log.enable_category(cat)` | Adds a category to the allowlist. |
| `Log.disable_category(cat)` | Removes a category from the allowlist. |
| `Log.mute_category(cat)` | Adds a category to the blocklist (mutes it). |
| `Log.unmute_category(cat)` | Removes a category from the blocklist. |
| `Log.close_log_file()` | Safely flushes and closes the active file stream. |

---

## 📝 License

Distributed under the MIT License. Feel free to use this in your own personal or commercial Godot projects.