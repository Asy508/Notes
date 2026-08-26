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
A CLI tool that processes an input string with optional repetition,
case conversion, and file output.
```
```
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
```

## 2. Execution Commands (Copy & Paste)

Run these commands from your terminal in the same folder where cli_app.py is saved.

### 🔹 Command 1: Run WITHOUT any arguments

The script detects zero arguments and automatically prints the help menu.

```
python cli_app.py
```

Expected output:
```
usage: cli_app.py [-h] [-o OUTPUT] [-v] [-r REPEAT] [-u] input

Process a string with optional flags.

positional arguments:
  input                 The text string to process.

optional arguments:
  -h, --help            show this help message and exit
  -o OUTPUT, --output OUTPUT
                        Save output to a file (otherwise prints to screen).
  -v, --verbose         Print debug info to stderr.
  -r REPEAT, --repeat REPEAT
                        Repeat the input N times (default: 1).
  -u, --uppercase       Convert text to ALL CAPS.
```

### 🔹 Command 2: Run WITH only the required argument

```
python cli_app.py "Hello World"
```

### 🔹 Command 3: Run with optional flags (repeat & uppercase)
```
python cli_app.py "Python" --repeat 3 --uppercase
```
Expected output:

```text
PYTHON PYTHON PYTHON 
```
### 🔹 Command 4: Run with all flags, including verbose and file output

```bash
python cli_app.py "Data" -r 2 -u -v -o result.txt
```
Expected output (printed to stderr, visible in terminal):

```text
DEBUG: input = Data
DEBUG: repeat = 2
DEBUG: uppercase = True
INFO: Saved to result.txt
```
(Nothing is printed to stdout because we used -o)

Now check the file:

```bash
cat result.txt
```
Output:

```text
DATA DATA 
```
### 🔹 Command 5: Using short options (same as above, shorter)
```bash
python cli_app.py "CLI" -r 4 -u -v -o output.txt
```

## 3. Quick Reference – All Commands at a Glance
Copy and run these one by one to test everything:

```bash
# No arguments (shows help)
python cli_app.py

# Required only
python cli_app.py "Hello"

# With repeats
python cli_app.py "Hi" -r 5

# Uppercase
python cli_app.py "lower" -u

# Repeat + uppercase + verbose (print to screen)
python cli_app.py "Mix" -r 3 -u -v

# Save to file (silent)
python cli_app.py "Secret" -o my_output.txt

# Save to file with verbose (see debug + save)
python cli_app.py "Secret" -o my_output.txt -v

```