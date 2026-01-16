# Error Handling

## Exceptions

### Basic Try-Except

```python
# Handle specific exception
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")

# Multiple exception types
try:
    value = int("abc")
except (ValueError, TypeError):
    print("Invalid value")

# Separate handlers
try:
    data = {"name": "Alice"}
    print(data["age"])
except KeyError:
    print("Key not found")
except TypeError:
    print("Type error occurred")

# Catch all exceptions (use sparingly)
try:
    risky_operation()
except Exception as e:
    print(f"Error: {e}")
```

### Else and Finally

```python
# else: runs if no exception occurred
try:
    result = 10 / 2
except ZeroDivisionError:
    print("Error")
else:
    print(f"Result: {result}")  # Runs if try succeeds

# finally: always runs (cleanup)
try:
    file = open("data.txt", "r")
    data = file.read()
except FileNotFoundError:
    print("File not found")
finally:
    file.close()  # Always executed

# Complete structure
try:
    result = int(input("Enter number: "))
    value = 100 / result
except ValueError:
    print("Invalid input")
except ZeroDivisionError:
    print("Cannot divide by zero")
else:
    print(f"Result: {value}")
finally:
    print("Cleanup completed")
```

### Accessing Exception Details

```python
try:
    value = int("abc")
except ValueError as e:
    print(f"Error message: {e}")
    print(f"Error type: {type(e).__name__}")

    # Exception attributes
    print(e.args)  # Error arguments

# Multiple exceptions with same handler
try:
    risky_operation()
except (ValueError, TypeError, KeyError) as e:
    print(f"Error: {type(e).__name__}: {e}")
```

## Raising Exceptions

### Basic Raise

```python
# Raise built-in exception
def validate_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
    if age > 150:
        raise ValueError("Age is too high")
    return age

try:
    validate_age(-5)
except ValueError as e:
    print(e)

# Raise without message
def check_value(x):
    if x < 0:
        raise ValueError

# Re-raise caught exception
try:
    risky_operation()
except ValueError as e:
    print("Logging error...")
    raise  # Re-raises the same exception
```

### Raise From (Exception Chaining)

```python
# Chain exceptions to preserve context
def process_data(data):
    try:
        return int(data)
    except ValueError as e:
        raise TypeError("Invalid data type") from e

try:
    process_data("abc")
except TypeError as e:
    print(f"Error: {e}")
    print(f"Caused by: {e.__cause__}")

# Suppress original exception
def safe_divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as e:
        raise ValueError("Invalid divisor") from None

# Without 'from None', you'd see both exceptions
```

## Built-in Exceptions

```python
# Common exceptions

# ValueError - Invalid value
int("abc")

# TypeError - Wrong type
"2" + 2

# KeyError - Missing dictionary key
{"a": 1}["b"]

# IndexError - Index out of range
[1, 2, 3][10]

# AttributeError - Invalid attribute
"hello".invalid_method()

# FileNotFoundError - File doesn't exist
open("missing.txt")

# ZeroDivisionError - Division by zero
10 / 0

# ImportError - Cannot import module
import non_existent_module

# RuntimeError - Generic runtime error
raise RuntimeError("Something went wrong")

# StopIteration - Iterator exhausted
it = iter([1, 2])
next(it)
next(it)
next(it)  # Raises StopIteration

# AssertionError - Assertion failed
assert False, "Assertion message"
```

## Custom Exceptions

### Creating Custom Exceptions

```python
# Simple custom exception
class MyError(Exception):
    pass

raise MyError("Something went wrong")

# With custom attributes
class ValidationError(Exception):
    def __init__(self, field, message):
        self.field = field
        self.message = message
        super().__init__(f"{field}: {message}")

try:
    raise ValidationError("email", "Invalid format")
except ValidationError as e:
    print(f"Field: {e.field}")
    print(f"Message: {e.message}")

# Exception hierarchy
class DatabaseError(Exception):
    """Base class for database errors."""
    pass

class ConnectionError(DatabaseError):
    """Database connection error."""
    pass

class QueryError(DatabaseError):
    """Database query error."""
    pass

# Can catch base class to handle all
try:
    raise QueryError("Invalid SQL")
except DatabaseError as e:
    print(f"Database error: {e}")
```

### Advanced Custom Exceptions

```python
class HTTPError(Exception):
    """HTTP request error."""

    def __init__(self, status_code, message):
        self.status_code = status_code
        self.message = message
        super().__init__(f"HTTP {status_code}: {message}")

    def is_client_error(self):
        return 400 <= self.status_code < 500

    def is_server_error(self):
        return 500 <= self.status_code < 600

try:
    raise HTTPError(404, "Not Found")
except HTTPError as e:
    print(e)
    print(f"Client error: {e.is_client_error()}")
```

## Context Managers

### Using Context Managers

```python
# File handling with context manager
with open("data.txt", "r") as file:
    data = file.read()
# File automatically closed, even if exception occurs

# Multiple context managers
with open("input.txt") as infile, open("output.txt", "w") as outfile:
    data = infile.read()
    outfile.write(data.upper())

# Database connection
import sqlite3

with sqlite3.connect("database.db") as conn:
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users")
    results = cursor.fetchall()
# Connection automatically committed and closed
```

### Creating Context Managers (Class-based)

```python
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):
        """Called when entering with block."""
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_val, exc_tb):
        """Called when exiting with block."""
        if self.file:
            self.file.close()

        # Handle exception
        if exc_type is not None:
            print(f"Exception: {exc_type.__name__}: {exc_val}")

        # Return False to propagate exception
        # Return True to suppress exception
        return False

# Usage
with FileManager("data.txt", "r") as f:
    data = f.read()
```

### Creating Context Managers (Decorator-based)

```python
from contextlib import contextmanager

@contextmanager
def file_manager(filename, mode):
    """Simple file context manager."""
    file = open(filename, mode)
    try:
        yield file  # Return control to with block
    finally:
        file.close()  # Cleanup

# Usage
with file_manager("data.txt", "r") as f:
    data = f.read()

# Advanced example: temporary directory
import os
import tempfile
import shutil

@contextmanager
def temporary_directory():
    """Create and cleanup temporary directory."""
    temp_dir = tempfile.mkdtemp()
    try:
        yield temp_dir
    finally:
        shutil.rmtree(temp_dir)

with temporary_directory() as temp_dir:
    # Work with temporary directory
    filepath = os.path.join(temp_dir, "temp.txt")
    with open(filepath, "w") as f:
        f.write("temporary data")
# temp_dir automatically deleted

# Timer context manager
import time

@contextmanager
def timer(name):
    """Measure execution time."""
    start = time.time()
    try:
        yield
    finally:
        end = time.time()
        print(f"{name} took {end - start:.2f} seconds")

with timer("Database query"):
    # Simulate work
    time.sleep(1)
```

### Built-in Context Managers

```python
from contextlib import suppress, redirect_stdout, closing
import io

# suppress - ignore specific exceptions
with suppress(FileNotFoundError):
    os.remove("missing.txt")
# No exception raised if file doesn't exist

# redirect_stdout - capture stdout
f = io.StringIO()
with redirect_stdout(f):
    print("Hello")
    print("World")
output = f.getvalue()  # "Hello\nWorld\n"

# closing - ensure close() is called
from urllib.request import urlopen

with closing(urlopen("https://example.com")) as page:
    data = page.read()
# page.close() called automatically

# Lock for threading
from threading import Lock

lock = Lock()
with lock:
    # Critical section
    shared_resource += 1
# Lock automatically released
```

## Exception Handling Best Practices

### Specific Exceptions

```python
# Bad: Catching all exceptions
try:
    risky_operation()
except:
    pass  # Silently fails

# Good: Catch specific exceptions
try:
    value = int(user_input)
except ValueError:
    print("Invalid number")

# Bad: Too broad
try:
    process_data()
except Exception:
    pass

# Good: Handle expected errors
try:
    process_data()
except (ValueError, KeyError) as e:
    logger.error(f"Data error: {e}")
    raise
```

### Don't Swallow Exceptions

```python
# Bad: Hiding errors
try:
    important_operation()
except Exception:
    pass

# Good: Log and/or re-raise
import logging

try:
    important_operation()
except Exception as e:
    logging.error(f"Operation failed: {e}")
    raise
```

### EAFP vs LBYL

```python
# LBYL: Look Before You Leap
if "key" in dictionary:
    value = dictionary["key"]
else:
    value = None

# EAFP: Easier to Ask for Forgiveness than Permission (Pythonic)
try:
    value = dictionary["key"]
except KeyError:
    value = None

# Or use get()
value = dictionary.get("key")

# EAFP example: file operations
# Bad (LBYL)
import os
if os.path.exists("file.txt"):
    with open("file.txt") as f:
        data = f.read()

# Good (EAFP)
try:
    with open("file.txt") as f:
        data = f.read()
except FileNotFoundError:
    data = None
```

### Assertions

```python
# Use for debugging and development
# NOT for error handling
def calculate_average(numbers):
    assert len(numbers) > 0, "List cannot be empty"
    return sum(numbers) / len(numbers)

# Assertions can be disabled with -O flag
# python -O script.py

# Use exceptions for runtime errors
def calculate_average(numbers):
    if not numbers:
        raise ValueError("List cannot be empty")
    return sum(numbers) / len(numbers)
```

## Exception Handling Patterns

### Retry Pattern

```python
import time

def retry(func, max_attempts=3, delay=1):
    """Retry function on exception."""
    for attempt in range(max_attempts):
        try:
            return func()
        except Exception as e:
            if attempt == max_attempts - 1:
                raise
            print(f"Attempt {attempt + 1} failed: {e}")
            time.sleep(delay)

# Usage
def flaky_api_call():
    # Might fail intermittently
    import random
    if random.random() < 0.7:
        raise ConnectionError("API unreachable")
    return {"data": "success"}

result = retry(flaky_api_call)
```

### Fallback Pattern

```python
def get_config():
    """Try multiple config sources."""
    try:
        return load_from_file("config.json")
    except FileNotFoundError:
        try:
            return load_from_env()
        except KeyError:
            return default_config()
```

### Cleanup Pattern

```python
def process_file(filename):
    """Ensure cleanup even on error."""
    resource = None
    try:
        resource = acquire_resource()
        return process(resource)
    except Exception as e:
        logger.error(f"Processing failed: {e}")
        raise
    finally:
        if resource:
            release_resource(resource)
```

> **Good to know:** Use `try-except-else-finally` for complete error handling. The `else` block runs only if no exception occurs. The `finally` block always runs for cleanup. Use `raise` without arguments to re-raise the current exception. Exception chaining with `raise ... from ...` preserves error context. Custom exceptions should inherit from `Exception` or specific built-in exceptions. Context managers (`with` statement) ensure cleanup code runs. Use `@contextmanager` decorator for simple context managers. The EAFP style is more Pythonic than LBYL. Don't catch `Exception` unless you re-raise it. Use assertions for debugging, not error handling. Assertions can be disabled with the `-O` flag. Always close resources in `finally` blocks or use context managers.

---

## References

- [Python - Errors and Exceptions](https://docs.python.org/3/tutorial/errors.html)
- [Python - Built-in Exceptions](https://docs.python.org/3/library/exceptions.html)
- [Python - Context Managers](https://docs.python.org/3/library/contextlib.html)
- [PEP 343 - The "with" Statement](https://www.python.org/dev/peps/pep-0343/)
- [Real Python - Python Exceptions](https://realpython.com/python-exceptions/)
- [Real Python - Context Managers](https://realpython.com/python-with-statement/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
