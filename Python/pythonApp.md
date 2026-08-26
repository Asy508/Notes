# Python CLI Application – Complete Self‑Contained Guide

This single file contains:
- The full Python script (`cli_app.py`) 
- All execution commands (with and without arguments)
- Expected outputs for each scenario

Just copy the script to your local machine, run the commands below, and push this `.md` file straight to your Git repository.

---

## 1. The Python Script (`cli_app.py`)

Copy the code below into a file named **`cli_app.py`** in your project folder.

```python
#!/usr/bin/env python3
"""
A CLI tool that processes an input string with optional repetition,
case conversion, and file output.
"""
import argparse
import sys

def main():
    parser = argparse.ArgumentParser(
        description="Process a string with optional flags."
    )

    # Required argument
    parser.add_argument(
        "input",
        type=str,
        help="The text string to process."
    )

    # Optional arguments
    parser.add_argument(
        "-o", "--output",
        type=str,
        help="Save output to a file (otherwise prints to screen)."
    )
    parser.add_argument(
        "-v", "--verbose",
        action="store_true",
        help="Print debug info to stderr."
    )
    parser.add_argument(
        "-r", "--repeat",
        type=int,
        default=1,
        help="Repeat the input N times (default: 1)."
    )
    parser.add_argument(
        "-u", "--uppercase",
        action="store_true",
        help="Convert text to ALL CAPS."
    )

    # If user runs script with NO arguments, show help and exit
    if len(sys.argv) == 1:
        parser.print_help()
        sys.exit(0)

    args = parser.parse_args()

    # Process the data
    result = args.input
    if args.repeat > 1:
        result = (result + " ") * args.repeat  # Add space between repeats
    if args.uppercase:
        result = result.upper()

    # Verbose logging (goes to stderr, won't affect output redirection)
    if args.verbose:
        print(f"DEBUG: input = {args.input}", file=sys.stderr)
        print(f"DEBUG: repeat = {args.repeat}", file=sys.stderr)
        print(f"DEBUG: uppercase = {args.uppercase}", file=sys.stderr)

    # Write to file or print to stdout
    if args.output:
        with open(args.output, "w", encoding="utf-8") as f:
            f.write(result)
        if args.verbose:
            print(f"INFO: Saved to {args.output}", file=sys.stderr)
    else:
        print(result)

if __name__ == "__main__":
    main()

## 2. Execution Commands (Copy & Paste)

Run these commands from your terminal in the same folder where cli_app.py is saved.

### 🔹 Command 1: Run WITHOUT any arguments

The script detects zero arguments and automatically prints the help menu.

---
python cli_app.py
---

Expected output:
---
usage: cli_app.py [-h] [-o OUTPUT] [-v] [-r REPEAT] [-u] input
---