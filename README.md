# ExifNotes to ExifTool

A Python script that converts a text file exported from [ExifNotes](https://play.google.com/store/apps/details?id=com.tommihirvonen.exifnotes&hl=en-US) into an executable shell script, with interactive file selection and validation.

## Purpose

This script helps photographers who use the [ExifNotes](https://play.google.com/store/apps/details?id=com.tommihirvonen.exifnotes&hl=en-US) app to maintain notes about their images' metadata. It converts the exported [ExifNotes](https://play.google.com/store/apps/details?id=com.tommihirvonen.exifnotes&hl=en-US) text file into executable [ExifTool](https://exiftool.org/) commands that can be run against actual image files.

## Features

- Reads a text file exported from [ExifNotes](https://play.google.com/store/apps/details?id=com.tommihirvonen.exifnotes&hl=en-US) containing [ExifTool](https://exiftool.org/) commands
- Interactively allows you to:
  - Specify the directory containing your image files
  - Choose the file extension to process (if you forgot to set it in the ExifNotes app)
  - Select the starting image when there are more images than commands (if you shot more frames than you logged)
- Validates the number of available images against the number of commands
- Generates an executable shell script with the modified commands

## Installation

1. Clone or download this repository
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
     python3 exifnotes-to-exiftool Fuji_400H_ExifToolCmds.txt
     ```
3. Follow the interactive prompts to:
   - Specify the image directory (or use current directory)
   - Enter the file extension (e.g., .jpg, .tif)
   - Select starting image if needed
   - Confirm the selection
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
2. Let you select which images to use
3. Generate a shell script with commands like:
   ```bash
   exiftool -Make="Hasselblad" -Model="SWC/M" -DateTime="2024:10:12 16:19" "path/to/your/image1.jpg"
   exiftool -Make="Hasselblad" -Model="SWC/M" -DateTime="2024:10:12 16:26" "path/to/your/image2.jpg"
   ```

## Notes

- The script handles three scenarios:
  - More images than commands: Lets you choose where to start
  - Fewer images than commands: Warns you and lets you proceed with available images
  - Equal number of images and commands: Proceeds directly
- All paths in the generated script are properly quoted for shell safety
- The generated script is made executable automatically 
