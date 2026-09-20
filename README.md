# Godot 4 Advanced Logger (`Log`)

A thread-safe, feature-rich logging utility for Godot 4. This static helper class supports tiered log levels, category allowlisting, colored rich console printing, asynchronous file output, automatic log rotation, and safe crash handling.

## Features

* **Thread-Safe:** Uses a global `Mutex` lock to ensure concurrent threads can log safely without race conditions.
* **Tiered Log Levels:** Filter messages by severity (`DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL`).
* **Category Filtering:** Opt-in category allowlisting lets you toggle specific systems (e.g., `"Physics"`, `"Network"`, `"AI"`) on and off dynamically.
* **Rich Console Output:** Leverages Godot's `print_rich` with color-coded BBCode formatting for clear terminal readability.
* **File Logging & Rotation:** Automatically writes logs to disk (`user://debug.log`), rotating backups when file size limits are reached.
* **Fatal Error Handlers:** Gracefully closes file handles and triggers an assertion or runtime crash based on whether you are running in the editor or an exported build.


## Installation & Setup

1. Place `Log.gd` into your project scripts folder (e.g., `res://scripts/core/Log.gd`).
2. Because all methods and configurations are `static`, you can access `Log` globally from anywhere in your project without needing an Autoload singleton instance.


## Usage Guide

### 1. Basic Logging

Call the appropriate method based on the severity of the message, passing a category tag and a log string:

```gdscript
Log.debug("Player", "Player velocity initialized to zero.")
Log.info("GameManager", "Level 1 loaded successfully.")
Log.warn("Audio", "Missing sound asset for footsteps.")
Log.error("Inventory", "Failed to parse item ID 404.")

```

### 2. Category Filtering (Allowlisting)

By default, all categories pass through if `enabled_categories` is empty. You can restrict logging to specific debug channels:

```gdscript
# Enable only specific systems
Log.enable_category("Network")
Log.enable_category("Physics")

# Later, to disable a category
Log.disable_category("Physics")

```

### 3. Enabling File Output

To write logs to a file on disk, toggle `write_to_file` and optionally customize the path or file size threshold:

```gdscript
func _ready() -> void:
	Log.write_to_file = true
	Log.log_file_path = "user://my_game_debug.log"
	Log.max_file_size_bytes = 5 * 1024 * 1024 # 5 MB limit before rotation

```

### 4. Handling Fatal Errors

Triggering a fatal log will print the error, close file streams safely, and trigger an engine assertion (in-editor) or crash (in release builds):

```gdscript
if not critical_resource_loaded:
	Log.fatal("Core", "Essential configuration file is missing! Shutting down.")

```


## Script Reference

### Configuration Variables

| Variable | Type | Default | Description |
| --- | --- | --- | --- |
| `min_level` | `Level` | `Level.DEBUG` | Minimum threshold level required for a message to print or write. |
| `enabled_categories` | `Dictionary` | `{}` | Allowlist dictionary for specific category tags. Empty allows all. |
| `write_to_file` | `bool` | `false` | Master toggle to enable writing log output to a file. |
| `log_file_path` | `String` | `"user://debug.log"` | Target path for the log file. |
| `max_file_size_bytes` | `int` | `2 * 1024 * 1024` | Maximum file size (2 MB) before triggering log rotation (`.old`). |

### Static Methods

| Method | Description |
| --- | --- |
| `debug(cat, msg)` | Logs a `DEBUG` level message. |
| `info(cat, msg)` | Logs an `INFO` level message. |
| `warn(cat, msg)` | Logs a `WARN` level message and pushes to the editor warning console. |
| `error(cat, msg)` | Logs an `ERROR` level message and pushes to the editor error console. |
| `fatal(cat, msg)` | Logs a `FATAL` message, closes logs, and safely terminates/asserts. |
| `enable_category(cat)` | Adds a category tag to the active allowlist. |
| `disable_category(cat)` | Removes a category tag from the allowlist. |
| `close_log_file()` | Safely flushes and closes active file handlers. |


## License

Distributed under the MIT License. Feel free to use and customize for your Godot projects.
