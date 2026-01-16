# Printing/Logging

## Print Function

### Basic Printing

```python
# Simple print
print("Hello, World!")

# Multiple arguments
print("Hello", "World")  # Hello World (space-separated)

# Print variables
name = "Alice"
age = 30
print("Name:", name, "Age:", age)
```

### Print Parameters

```python
# sep - separator between arguments (default: ' ')
print("apple", "banana", "cherry", sep=", ")  # apple, banana, cherry
print("2024", "01", "15", sep="-")  # 2024-01-15

# end - ending character (default: '\n')
print("Loading", end="...")  # No newline
print("Done!")  # Loading...Done!

# file - output stream (default: sys.stdout)
import sys
print("Error message", file=sys.stderr)

# Write to file
with open("output.txt", "w") as f:
    print("Hello, file!", file=f)

# flush - force flush buffer
print("Processing", flush=True)
```

## String Formatting

### F-Strings (Python 3.6+, Recommended)

```python
name = "Alice"
age = 30

# Basic f-string
print(f"My name is {name} and I'm {age} years old")

# Expressions inside {}
print(f"Next year I'll be {age + 1}")

# Format specifiers
price = 19.99
print(f"Price: ${price:.2f}")  # Price: $19.99

# Alignment
print(f"{'Left':<10}|")   # Left      |
print(f"{'Right':>10}|")  #      Right|
print(f"{'Center':^10}|") #  Center   |

# Number formatting
num = 1234567.89
print(f"{num:,.2f}")      # 1,234,567.89
print(f"{num:.2e}")       # 1.23e+06

# Padding with zeros
print(f"{42:05d}")        # 00042

# Percentage
ratio = 0.875
print(f"{ratio:.1%}")     # 87.5%

# Date formatting
from datetime import datetime
now = datetime.now()
print(f"Today is {now:%Y-%m-%d %H:%M:%S}")
```

### .format() Method

```python
# Positional arguments
print("Hello, {}!".format("World"))
print("{0} {1}".format("Hello", "World"))
print("{1} {0}".format("World", "Hello"))  # Hello World

# Named arguments
print("Hello, {name}!".format(name="Alice"))
print("{x} + {y} = {z}".format(x=1, y=2, z=3))

# Format specifiers
print("{:.2f}".format(3.14159))  # 3.14
print("{:>10}".format("text"))   #       text
```

### % Formatting (Old Style)

```python
# Still valid but less preferred
name = "Alice"
age = 30

print("Name: %s, Age: %d" % (name, age))
print("Pi: %.2f" % 3.14159)
```

## Output Styling

### Colors and Formatting (ANSI Escape Codes)

```python
# ANSI color codes
RED = "\033[31m"
GREEN = "\033[32m"
YELLOW = "\033[33m"
BLUE = "\033[34m"
RESET = "\033[0m"

print(f"{RED}Error!{RESET}")
print(f"{GREEN}Success!{RESET}")

# Bold, italic, underline
BOLD = "\033[1m"
UNDERLINE = "\033[4m"
print(f"{BOLD}Bold text{RESET}")
```

### Using colorama (Cross-platform)

```python
from colorama import init, Fore, Back, Style

init()  # Initialize colorama

print(Fore.RED + "Error!" + Style.RESET_ALL)
print(Fore.GREEN + "Success!" + Style.RESET_ALL)
print(Back.YELLOW + "Warning!" + Style.RESET_ALL)
```

### Using rich Library

```python
from rich import print
from rich.console import Console
from rich.table import Table

# Rich print with automatic syntax highlighting
print("[bold red]Error![/bold red]")
print("[green]Success![/green]")

# Console
console = Console()
console.print("Hello", style="bold blue")

# Table
table = Table(title="Users")
table.add_column("Name", style="cyan")
table.add_column("Age", style="magenta")
table.add_row("Alice", "30")
console.print(table)
```

## Logging

### Basic Logging

```python
import logging

# Configure logging
logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

# Log messages
logging.debug("Debug message")      # DEBUG
logging.info("Info message")        # INFO
logging.warning("Warning message")  # WARNING
logging.error("Error message")      # ERROR
logging.critical("Critical message") # CRITICAL
```

### Logging Levels

```python
import logging

# Set level (only messages >= this level will be shown)
logging.basicConfig(level=logging.INFO)

# DEBUG < INFO < WARNING < ERROR < CRITICAL

logging.debug("Won't be shown")
logging.info("Will be shown")
logging.warning("Will be shown")
```

### Logging to File

```python
import logging

logging.basicConfig(
    filename='app.log',
    filemode='a',  # 'a' for append, 'w' for overwrite
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

logging.info("Application started")
```

### Custom Logger

```python
import logging

# Create logger
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# Create file handler
file_handler = logging.FileHandler('app.log')
file_handler.setLevel(logging.ERROR)

# Create console handler
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.DEBUG)

# Create formatter
formatter = logging.Formatter(
    '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

# Add formatter to handlers
file_handler.setFormatter(formatter)
console_handler.setFormatter(formatter)

# Add handlers to logger
logger.addHandler(file_handler)
logger.addHandler(console_handler)

# Use logger
logger.debug("Debug message")
logger.info("Info message")
logger.error("Error message")
```

### Logging with Variables

```python
import logging

name = "Alice"
age = 30

# Use f-strings or %-formatting
logging.info(f"User {name} is {age} years old")
logging.info("User %s is %d years old", name, age)  # More efficient

# Log exceptions
try:
    result = 10 / 0
except ZeroDivisionError:
    logging.exception("An error occurred")  # Includes stack trace
```

### Structured Logging

```python
import logging
import json

class JsonFormatter(logging.Formatter):
    def format(self, record):
        log_data = {
            'timestamp': self.formatTime(record),
            'level': record.levelname,
            'message': record.getMessage(),
            'module': record.module,
        }
        return json.dumps(log_data)

handler = logging.StreamHandler()
handler.setFormatter(JsonFormatter())

logger = logging.getLogger(__name__)
logger.addHandler(handler)
logger.setLevel(logging.INFO)

logger.info("User logged in")
# Output: {"timestamp": "2024-01-15 10:30:45", "level": "INFO", ...}
```

## Debug Printing

### Using pprint

```python
from pprint import pprint

# Pretty print complex data structures
data = {
    'users': [
        {'name': 'Alice', 'age': 30, 'email': 'alice@example.com'},
        {'name': 'Bob', 'age': 25, 'email': 'bob@example.com'},
    ]
}

pprint(data, indent=2)
```

### Debug Function

```python
# Python 3.8+ f-string debugging
x = 10
y = 20

print(f"{x=}, {y=}")  # x=10, y=20
print(f"{x + y=}")    # x + y=30
```

### repr() vs str()

```python
# str() - human-readable
# repr() - developer-friendly, unambiguous

text = "Hello\nWorld"

print(str(text))   # Hello
                   # World

print(repr(text))  # 'Hello\nWorld'

# In f-strings
print(f"{text}")   # Uses str()
print(f"{text!r}") # Uses repr()
```

## Input

```python
# Read user input
name = input("Enter your name: ")
print(f"Hello, {name}!")

# Input is always string, convert if needed
age = int(input("Enter your age: "))
price = float(input("Enter price: "))

# Reading multiple values
data = input("Enter name and age: ").split()
name, age = data[0], int(data[1])
```

> **Good to know:** F-strings are faster and more readable than other formatting methods. The `logging` module is thread-safe and should be preferred over `print()` statements in production code. Use `logging.exception()` inside except blocks to automatically include stack traces. For debugging, the `{variable=}` syntax in f-strings (Python 3.8+) is incredibly useful. The `rich` library provides beautiful formatting out of the box with minimal code.

---

## References

- [Python - Input and Output](https://docs.python.org/3/tutorial/inputoutput.html)
- [Python - Logging HOWTO](https://docs.python.org/3/howto/logging.html)
- [Python - Logging Cookbook](https://docs.python.org/3/howto/logging-cookbook.html)
- [Rich - Python Library for Rich Text](https://rich.readthedocs.io/)
- [Colorama - Cross-platform colored terminal text](https://pypi.org/project/colorama/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
