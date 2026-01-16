# Standard Libraries

## File and Directory Operations

### os Module

```python
import os

# Current working directory
print(os.getcwd())
os.chdir('/path/to/directory')

# Environment variables
print(os.environ.get('HOME'))
os.environ['MY_VAR'] = 'value'

# Path operations
path = os.path.join('dir', 'subdir', 'file.txt')
print(os.path.exists(path))
print(os.path.isfile(path))
print(os.path.isdir('dir'))
print(os.path.abspath('file.txt'))
print(os.path.dirname(path))
print(os.path.basename(path))
print(os.path.splitext('file.txt'))  # ('file', '.txt')

# Directory operations
os.mkdir('newdir')
os.makedirs('parent/child/grandchild', exist_ok=True)
os.rmdir('emptydir')
os.removedirs('parent/child/grandchild')  # Removes empty directories

# File operations
os.remove('file.txt')
os.rename('old.txt', 'new.txt')
os.replace('old.txt', 'new.txt')  # Atomic on POSIX

# List directory
files = os.listdir('.')
for file in files:
    print(file)

# Walk directory tree
for root, dirs, files in os.walk('.'):
    for file in files:
        filepath = os.path.join(root, file)
        print(filepath)

# Process management
os.system('ls -la')  # Run shell command
result = os.popen('ls').read()  # Capture output
```

### pathlib Module (Modern Alternative)

```python
from pathlib import Path

# Create Path object
path = Path('dir/subdir/file.txt')
path = Path.home() / 'documents' / 'file.txt'

# Path properties
print(path.name)        # 'file.txt'
print(path.stem)        # 'file'
print(path.suffix)      # '.txt'
print(path.parent)      # 'dir/subdir'
print(path.parents[0])  # 'dir/subdir'
print(path.anchor)      # '/' or 'C:\\'

# Path operations
print(path.exists())
print(path.is_file())
print(path.is_dir())
print(path.absolute())
print(path.resolve())  # Absolute with symlinks resolved

# Read/write files
content = path.read_text()
path.write_text('Hello, World!')
data = path.read_bytes()
path.write_bytes(b'binary data')

# Directory operations
path = Path('newdir')
path.mkdir(exist_ok=True, parents=True)
path.rmdir()  # Remove empty directory

# Iterate directory
for file in Path('.').iterdir():
    print(file)

# Glob patterns
for file in Path('.').glob('*.py'):
    print(file)

for file in Path('.').rglob('*.txt'):  # Recursive
    print(file)

# File operations
old_path = Path('old.txt')
new_path = Path('new.txt')
old_path.rename(new_path)
old_path.replace(new_path)  # Atomic
old_path.unlink()  # Delete file

# Join paths
path = Path('home') / 'user' / 'documents'
path = path.joinpath('file.txt')
```

### shutil Module

```python
import shutil

# Copy files
shutil.copy('src.txt', 'dst.txt')        # Copy file
shutil.copy2('src.txt', 'dst.txt')       # Copy with metadata
shutil.copyfile('src.txt', 'dst.txt')    # Copy content only

# Copy directories
shutil.copytree('srcdir', 'dstdir')
shutil.copytree('srcdir', 'dstdir',
                ignore=shutil.ignore_patterns('*.pyc', 'tmp*'))

# Move files/directories
shutil.move('src', 'dst')

# Remove directory tree
shutil.rmtree('directory')

# Disk usage
total, used, free = shutil.disk_usage('/')
print(f"Total: {total // (2**30)} GB")

# Archive operations
shutil.make_archive('archive', 'zip', 'directory')
shutil.unpack_archive('archive.zip', 'extract_dir')

# Get available archive formats
print(shutil.get_archive_formats())
# [('bztar', "bzip2'ed tar-file"), ('gztar', "gzip'ed tar-file"), ...]
```

## System and Process

### sys Module

```python
import sys

# Command-line arguments
print(sys.argv)  # ['script.py', 'arg1', 'arg2']

# Python version
print(sys.version)
print(sys.version_info)  # sys.version_info(major=3, minor=11, ...)

# Module search path
print(sys.path)
sys.path.append('/custom/path')

# Standard streams
sys.stdout.write("Output\n")
sys.stderr.write("Error\n")
line = sys.stdin.readline()

# Exit program
sys.exit(0)  # Exit with code 0
sys.exit("Error message")  # Exit with code 1

# Platform information
print(sys.platform)  # 'linux', 'darwin', 'win32'

# Object size
import sys
x = [1, 2, 3]
print(sys.getsizeof(x))  # Size in bytes

# Reference count
import sys
x = []
print(sys.getrefcount(x))  # Number of references

# Recursion limit
print(sys.getrecursionlimit())
sys.setrecursionlimit(2000)
```

### subprocess Module

```python
import subprocess

# Run command (simple)
result = subprocess.run(['ls', '-la'])
print(result.returncode)

# Capture output
result = subprocess.run(
    ['ls', '-la'],
    capture_output=True,
    text=True
)
print(result.stdout)
print(result.stderr)

# Check return code
result = subprocess.run(['ls', 'nonexistent'], check=True)
# Raises CalledProcessError if return code != 0

# With input
result = subprocess.run(
    ['cat'],
    input='Hello\nWorld',
    text=True,
    capture_output=True
)
print(result.stdout)

# Pipe commands
p1 = subprocess.Popen(['ls'], stdout=subprocess.PIPE)
p2 = subprocess.Popen(['grep', 'py'],
                      stdin=p1.stdout,
                      stdout=subprocess.PIPE)
output = p2.communicate()[0]

# Shell commands (use carefully - security risk)
result = subprocess.run('ls | grep py',
                       shell=True,
                       capture_output=True,
                       text=True)

# Timeout
try:
    result = subprocess.run(['sleep', '10'], timeout=5)
except subprocess.TimeoutExpired:
    print("Command timed out")
```

## Date and Time

### datetime Module

```python
from datetime import datetime, date, time, timedelta

# Current date and time
now = datetime.now()
today = date.today()
current_time = datetime.now().time()

print(now)       # 2024-01-15 14:30:00.123456
print(today)     # 2024-01-15
print(current_time)  # 14:30:00.123456

# Create specific datetime
dt = datetime(2024, 1, 15, 14, 30, 0)
d = date(2024, 1, 15)
t = time(14, 30, 0)

# Format datetime
formatted = now.strftime("%Y-%m-%d %H:%M:%S")
formatted = now.strftime("%B %d, %Y")  # January 15, 2024

# Parse datetime
dt = datetime.strptime("2024-01-15 14:30:00", "%Y-%m-%d %H:%M:%S")

# Components
print(dt.year, dt.month, dt.day)
print(dt.hour, dt.minute, dt.second)
print(dt.weekday())  # 0 = Monday, 6 = Sunday

# Timedelta (duration)
delta = timedelta(days=7, hours=2, minutes=30)
future = now + delta
past = now - delta

difference = future - now
print(difference.days)
print(difference.seconds)
print(difference.total_seconds())

# Compare dates
if date1 > date2:
    print("date1 is later")

# Replace components
new_date = now.replace(year=2025, month=12)
```

### time Module

```python
import time

# Current time (seconds since epoch)
timestamp = time.time()
print(timestamp)  # 1705329000.123456

# Sleep
time.sleep(2)  # Sleep for 2 seconds

# Performance timing
start = time.perf_counter()
# ... code to measure ...
elapsed = time.perf_counter() - start
print(f"Elapsed: {elapsed:.4f}s")

# Monotonic clock (for timing, not affected by system time changes)
start = time.monotonic()
# ... code ...
elapsed = time.monotonic() - start

# Convert timestamp to datetime
dt = datetime.fromtimestamp(timestamp)

# Convert datetime to timestamp
timestamp = dt.timestamp()

# Time struct
t = time.localtime()
print(t.tm_year, t.tm_mon, t.tm_mday)

# Format time
formatted = time.strftime("%Y-%m-%d %H:%M:%S", t)
```

## Data Serialization

### json Module

```python
import json

# Python to JSON
data = {
    "name": "Alice",
    "age": 30,
    "skills": ["Python", "JavaScript"],
    "active": True
}

# Serialize to string
json_string = json.dumps(data)
json_string = json.dumps(data, indent=2)  # Pretty print

# Serialize to file
with open('data.json', 'w') as f:
    json.dump(data, f, indent=2)

# JSON to Python
json_string = '{"name": "Alice", "age": 30}'
data = json.loads(json_string)

# Deserialize from file
with open('data.json', 'r') as f:
    data = json.load(f)

# Custom encoding
from datetime import datetime

class DateTimeEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, datetime):
            return obj.isoformat()
        return super().default(obj)

data = {"timestamp": datetime.now()}
json_string = json.dumps(data, cls=DateTimeEncoder)

# Custom decoding
def datetime_decoder(dct):
    for key, value in dct.items():
        if isinstance(value, str):
            try:
                dct[key] = datetime.fromisoformat(value)
            except:
                pass
    return dct

data = json.loads(json_string, object_hook=datetime_decoder)
```

### pickle Module

```python
import pickle

# Serialize Python objects (binary)
data = {"name": "Alice", "scores": [85, 90, 95]}

# Serialize to file
with open('data.pkl', 'wb') as f:
    pickle.dump(data, f)

# Deserialize from file
with open('data.pkl', 'rb') as f:
    data = pickle.load(f)

# Serialize to bytes
bytes_data = pickle.dumps(data)

# Deserialize from bytes
data = pickle.loads(bytes_data)

# Pickle complex objects
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age

user = User("Alice", 30)
with open('user.pkl', 'wb') as f:
    pickle.dump(user, f)

# Warning: Only unpickle data you trust!
# Pickles can execute arbitrary code
```

## Regular Expressions

### re Module

```python
import re

# Match pattern
pattern = r'\d+'  # One or more digits
text = "There are 42 apples and 17 oranges"

# Search for pattern
match = re.search(pattern, text)
if match:
    print(match.group())  # '42'
    print(match.start())  # Start position
    print(match.end())    # End position

# Find all matches
matches = re.findall(pattern, text)
print(matches)  # ['42', '17']

# Find all with details
for match in re.finditer(pattern, text):
    print(match.group(), match.start())

# Match from beginning
match = re.match(r'There', text)
if match:
    print("Matched")

# Replace
new_text = re.sub(r'\d+', 'X', text)
print(new_text)  # "There are X apples and X oranges"

# Replace with function
def double(match):
    return str(int(match.group()) * 2)

new_text = re.sub(r'\d+', double, text)
# "There are 84 apples and 34 oranges"

# Split by pattern
parts = re.split(r'\s+', text)

# Groups
pattern = r'(\d+) (\w+)'
match = re.search(pattern, text)
if match:
    print(match.group(0))  # Full match: '42 apples'
    print(match.group(1))  # First group: '42'
    print(match.group(2))  # Second group: 'apples'
    print(match.groups())  # Tuple: ('42', 'apples')

# Named groups
pattern = r'(?P<count>\d+) (?P<item>\w+)'
match = re.search(pattern, text)
if match:
    print(match.group('count'))  # '42'
    print(match.group('item'))   # 'apples'
    print(match.groupdict())     # {'count': '42', 'item': 'apples'}

# Compile pattern for reuse
pattern = re.compile(r'\d+')
matches = pattern.findall(text)

# Flags
match = re.search(r'THERE', text, re.IGNORECASE)
match = re.search(r'line1.*line2', text, re.DOTALL)  # . matches \n
```

## Collections

### collections Module

```python
from collections import defaultdict, Counter, deque, namedtuple, OrderedDict

# defaultdict - with default factory
dd = defaultdict(list)
dd['key'].append(1)  # No KeyError

dd = defaultdict(int)
dd['count'] += 1  # No KeyError, starts at 0

# Counter - count hashable objects
words = ['apple', 'banana', 'apple', 'cherry', 'banana', 'apple']
counter = Counter(words)
print(counter)  # Counter({'apple': 3, 'banana': 2, 'cherry': 1})
print(counter['apple'])  # 3
print(counter.most_common(2))  # [('apple', 3), ('banana', 2)]

# Counter arithmetic
c1 = Counter(['a', 'b', 'c'])
c2 = Counter(['b', 'c', 'd'])
print(c1 + c2)  # Combine
print(c1 - c2)  # Subtract

# deque - double-ended queue
dq = deque([1, 2, 3])
dq.append(4)      # Add to right
dq.appendleft(0)  # Add to left
dq.pop()          # Remove from right
dq.popleft()      # Remove from left

# Rotate
dq.rotate(2)   # Rotate right
dq.rotate(-1)  # Rotate left

# namedtuple - immutable with named fields
Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)
print(p.x, p.y)
print(p[0], p[1])  # Also accessible by index

# Convert to dict
print(p._asdict())  # {'x': 10, 'y': 20}

# OrderedDict - remembers insertion order (dict does this in Python 3.7+)
od = OrderedDict()
od['a'] = 1
od['b'] = 2
od['c'] = 3

# Move to end
od.move_to_end('a')
print(list(od.keys()))  # ['b', 'c', 'a']
```

## Iteration Tools

### itertools Module

```python
from itertools import (
    count, cycle, repeat,
    chain, islice, zip_longest,
    product, permutations, combinations,
    groupby, accumulate
)

# Infinite iterators
for i in count(10, 2):  # 10, 12, 14, ...
    if i > 20:
        break

for item in cycle(['A', 'B', 'C']):  # A, B, C, A, B, C, ...
    break

for item in repeat('X', 3):  # X, X, X
    print(item)

# Finite iterators
list1 = [1, 2, 3]
list2 = [4, 5, 6]
combined = list(chain(list1, list2))  # [1, 2, 3, 4, 5, 6]

# islice - slice iterator
numbers = range(100)
result = list(islice(numbers, 10, 20))  # [10, 11, ..., 19]

# zip_longest - zip with padding
a = [1, 2, 3]
b = ['a', 'b']
result = list(zip_longest(a, b, fillvalue='N/A'))
# [(1, 'a'), (2, 'b'), (3, 'N/A')]

# Combinatoric iterators
# Product - Cartesian product
result = list(product([1, 2], ['a', 'b']))
# [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]

# Permutations - all orderings
result = list(permutations([1, 2, 3], 2))
# [(1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)]

# Combinations - no repetition
result = list(combinations([1, 2, 3], 2))
# [(1, 2), (1, 3), (2, 3)]

# groupby - group consecutive items
data = [('A', 1), ('A', 2), ('B', 3), ('B', 4)]
for key, group in groupby(data, key=lambda x: x[0]):
    print(key, list(group))
# A [('A', 1), ('A', 2')]
# B [('B', 3), ('B', 4')]

# accumulate - running totals
result = list(accumulate([1, 2, 3, 4, 5]))  # [1, 3, 6, 10, 15]
```

> **Good to know:** `pathlib` is the modern way to handle paths, preferred over `os.path`. `shutil` provides high-level file operations. `subprocess.run()` is preferred over `os.system()`. Always use `check=True` with subprocess to catch errors. `datetime` is timezone-naive by default; use `zoneinfo` for timezone-aware dates. Use `time.perf_counter()` for accurate timing. JSON can't serialize datetime directly; needs custom encoder. Pickle is Python-specific and can execute code (security risk). `re.compile()` improves performance for repeated pattern matching. `Counter` is perfect for frequency counting. `deque` provides O(1) append/pop from both ends. `itertools` provides memory-efficient iterators. Use `chain()` instead of concatenating lists.

---

## References

- [Python - Standard Library](https://docs.python.org/3/library/)
- [Python - os Module](https://docs.python.org/3/library/os.html)
- [Python - pathlib Module](https://docs.python.org/3/library/pathlib.html)
- [Python - datetime Module](https://docs.python.org/3/library/datetime.html)
- [Python - json Module](https://docs.python.org/3/library/json.html)
- [Python - re Module](https://docs.python.org/3/library/re.html)
- [Python - collections Module](https://docs.python.org/3/library/collections.html)
- [Python - itertools Module](https://docs.python.org/3/library/itertools.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
