# ExifNotes to ExifTool

A Python script that converts a text file exported from [ExifNotes](https://play.google.com/store/apps/details?id=com.tommihirvonen.exifnotes&hl=en-US) into an executable shell script, with interactive file selection and validation.

## Purpose

This script helps photographers who use the [ExifNotes](https://play.google.com/store/apps/details?id=com.tommihirvonen.exifnotes&hl=en-US) app to maintain notes about their images' metadata. It converts the exported [ExifNotes](https://play.google.com/store/apps/details?id=com.tommihirvonen.exifnotes&hl=en-US) text file into an executable shell script with [ExifTool](https://exiftool.org/) commands. With one command you can then update the image files *without having to rename or move them*.

## Features

- Reads a text file exported from [ExifNotes](https://play.google.com/store/apps/details?id=com.tommihirvonen.exifnotes&hl=en-US) containing [ExifTool](https://exiftool.org/) commands
- Interactively allows you to:
  - Specify the directory containing your image files
  - Choose the file extension to process (if you forgot to set it in the ExifNotes app)
  - Enter film stock information (film size and film type)
  - Select start and end image files to define a range (supports reverse walking)
- Validates the number of available images against the number of commands
- Generates an executable shell script with the modified commands
- Adds hierarchical subject metadata for film organization in Lightroom

## Installation

1. Clone this repository or download [this file](https://raw.githubusercontent.com/stephanbrez/exifnotes-to-exiftool/refs/heads/main/exifnotes-to-exiftool)
2. Make the script executable:
   ```bash
   chmod +x exifnotes-to-exiftool
   ```
3. (Optional) Move the script to a directory in your PATH for global access:
   ```bash
   sudo mv exifnotes-to-exiftool /usr/local/bin/
   ```

## Usage

1. Export your ExifNotes data to a text file (e.g., `Fuji_400H_ExifToolCmds.txt`)
2. Run the script in one of two ways:
   - Direct execution (if in your PATH):
     ```bash
     exifnotes-to-exiftool Fuji_400H_ExifToolCmds.txt
     ```
   - Using Python interpreter:
     ```bash
     python3 <path to file>/exifnotes-to-exiftool Fuji_400H_ExifToolCmds.txt
     ```
3. Follow the interactive prompts to:
   - Specify the image directory (or use current directory)
   - Enter the file extension (e.g., .jpg, .tif)
   - Select the film size from a menu (35mm, 120, 4x5, 5x7, 8x10, or custom)
   - Enter the film stock name (e.g., Portra 400, Tri-X 400)
   - Select start and end image files to define a range
   - Confirm the selection (script auto-detects reverse if end comes before start)
4. Review the generated shell script before running it
5. Run the script: `./<path to images>/run_exiftool_Fuji_400H_ExifToolCmds.sh`

## Requirements

- Python 3.x
- No external dependencies (uses only built-in Python modules)

## Example

Given an input file like:
```
exiftool -Make="Hasselblad" -Model="SWC/M" -DateTime="2024:10:12 16:19" *_1.jpg
exiftool -Make="Hasselblad" -Model="SWC/M" -DateTime="2024:10:12 16:26" *_2.jpg
```

The script will:
1. Detect that you need 2 images
2. Prompt you for film size and stock
3. Let you select which images to use
4. Generate a shell script with commands like:
   ```bash
   exiftool -Make="Hasselblad" -Model="SWC/M" -DateTime="2024:10:12 16:19" "path/to/your/image1.jpg" -XMP-lr:HierarchicalSubject+="Film|35mm|Portra 400"
   exiftool -Make="Hasselblad" -Model="SWC/M" -DateTime="2024:10:12 16:26" "path/to/your/image2.jpg" -XMP-lr:HierarchicalSubject+="Film|35mm|Portra 400"
   ```

## Notes

- The script handles file range selection:
  - Equal files and commands: Proceeds directly
  - More images than commands: Prompts for START and END files to define range
  - Fewer images than commands: Warns and uses available files
  - If END comes before START: Automatically walks backward (reverse order)
- All paths in the generated script are properly quoted for shell safety
- The generated script is made executable automatically
- Command order is always sequential from the .txt file; file direction determines mapping

## Film Stock Hierarchy

When you enter film size and stock information, the script adds metadata using the XMP `lr:HierarchicalSubject` tag (Lightroom Hierarchical Subject). This creates a three-level hierarchy that organizes your images in Lightroom:

```
Film|35mm|Portra 400
```

### Hierarchy Levels

| Level | Example | Description |
|-------|---------|-------------|
| 1 | `Film` | Top-level category (always "Film") |
| 2 | `35mm` | Film size (from selection menu) |
| 3 | `Portra 400` | Film stock name (your input) |

### Available Film Sizes

The script provides a preset menu of common film sizes:
- 35mm
- 120
- 4x5
- 5x7
- 8x10
- Custom (enter your own size)

### Generated Commands

The generated ExifTool commands include the hierarchical subject:
```bash
exiftool -Make="Hasselblad" -Model="SWC/M" "image1.jpg" -XMP-lr:HierarchicalSubject+="Film|35mm|Portra 400"
```

This allows you to:
- Filter images by film type in Lightroom
- Quickly find all images shot on a specific stock
- Organize your film scans using Lightroom's keyword hierarchy 
