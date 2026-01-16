# Awesome Libraries

## Web Frameworks

### Flask

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, Flask!"

@app.route('/api/users/<int:user_id>')
def get_user(user_id):
    user = {"id": user_id, "name": "Alice"}
    return jsonify(user)

@app.route('/api/users', methods=['POST'])
def create_user():
    data = request.json
    # Process data
    return jsonify({"message": "User created"}), 201

if __name__ == '__main__':
    app.run(debug=True)

# Install: pip install flask
```

### FastAPI

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    name: str
    email: str
    age: int

@app.get("/")
async def root():
    return {"message": "Hello, FastAPI!"}

@app.get("/users/{user_id}")
async def get_user(user_id: int):
    if user_id == 0:
        raise HTTPException(status_code=404, detail="User not found")
    return {"id": user_id, "name": "Alice"}

@app.post("/users")
async def create_user(user: User):
    return {"message": "User created", "user": user}

# Run: uvicorn main:app --reload
# Install: pip install fastapi uvicorn
```

### Django

```python
# Django is a full-featured web framework
# Quick setup:

# Install
# pip install django

# Create project
# django-admin startproject myproject
# cd myproject
# python manage.py startapp myapp

# myapp/models.py
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    created_at = models.DateTimeField(auto_now_add=True)

# myapp/views.py
from django.http import JsonResponse
from .models import User

def user_list(request):
    users = User.objects.all().values()
    return JsonResponse(list(users), safe=False)

# Run migrations
# python manage.py makemigrations
# python manage.py migrate
# python manage.py runserver
```

## HTTP Clients

### Requests

```python
import requests

# GET request
response = requests.get('https://api.github.com')
print(response.status_code)
print(response.json())

# With parameters
params = {'q': 'python', 'sort': 'stars'}
response = requests.get('https://api.github.com/search/repositories', params=params)

# POST request
data = {'name': 'Alice', 'email': 'alice@example.com'}
response = requests.post('https://api.example.com/users', json=data)

# Headers
headers = {'Authorization': 'Bearer token'}
response = requests.get('https://api.example.com/data', headers=headers)

# Timeout
response = requests.get('https://api.example.com', timeout=5)

# Sessions (persistent cookies)
session = requests.Session()
session.headers.update({'User-Agent': 'MyApp/1.0'})
response = session.get('https://api.example.com')

# Install: pip install requests
```

### httpx (Async Alternative)

```python
import httpx
import asyncio

# Sync API (like requests)
response = httpx.get('https://api.github.com')
print(response.json())

# Async API
async def fetch_data():
    async with httpx.AsyncClient() as client:
        response = await client.get('https://api.github.com')
        return response.json()

# Multiple concurrent requests
async def fetch_multiple():
    async with httpx.AsyncClient() as client:
        urls = [
            'https://api.github.com',
            'https://api.example.com',
        ]
        tasks = [client.get(url) for url in urls]
        responses = await asyncio.gather(*tasks)
        return [r.json() for r in responses]

# Install: pip install httpx
```

## Data Science

### NumPy

```python
import numpy as np

# Create arrays
arr = np.array([1, 2, 3, 4, 5])
arr2d = np.array([[1, 2, 3], [4, 5, 6]])

# Array creation functions
zeros = np.zeros((3, 4))
ones = np.ones((2, 3))
range_arr = np.arange(0, 10, 2)  # [0, 2, 4, 6, 8]
linspace = np.linspace(0, 1, 5)  # [0, 0.25, 0.5, 0.75, 1]

# Array operations
arr = np.array([1, 2, 3, 4])
print(arr + 10)       # [11, 12, 13, 14]
print(arr * 2)        # [2, 4, 6, 8]
print(arr ** 2)       # [1, 4, 9, 16]
print(np.sqrt(arr))   # [1, 1.41, 1.73, 2]

# Matrix operations
a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])
print(a @ b)          # Matrix multiplication
print(np.dot(a, b))   # Same as above

# Statistical operations
arr = np.array([1, 2, 3, 4, 5])
print(arr.mean())
print(arr.std())
print(arr.sum())
print(arr.min(), arr.max())

# Install: pip install numpy
```

### Pandas

```python
import pandas as pd

# Create DataFrame
data = {
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [25, 30, 35],
    'city': ['NYC', 'LA', 'Chicago']
}
df = pd.DataFrame(data)

# Read from file
df = pd.read_csv('data.csv')
df = pd.read_excel('data.xlsx')
df = pd.read_json('data.json')

# Basic operations
print(df.head())       # First 5 rows
print(df.tail())       # Last 5 rows
print(df.info())       # DataFrame info
print(df.describe())   # Statistical summary

# Selecting data
print(df['name'])                    # Column
print(df[['name', 'age']])          # Multiple columns
print(df.loc[0])                     # Row by label
print(df.iloc[0])                    # Row by position
print(df[df['age'] > 25])           # Filter

# Adding/modifying columns
df['salary'] = [50000, 60000, 70000]
df['senior'] = df['age'] > 30

# Grouping
grouped = df.groupby('city')['age'].mean()

# Sorting
df_sorted = df.sort_values('age', ascending=False)

# Write to file
df.to_csv('output.csv', index=False)
df.to_excel('output.xlsx', index=False)

# Install: pip install pandas
```

### Matplotlib

```python
import matplotlib.pyplot as plt
import numpy as np

# Line plot
x = np.linspace(0, 10, 100)
y = np.sin(x)
plt.plot(x, y)
plt.xlabel('X axis')
plt.ylabel('Y axis')
plt.title('Sine Wave')
plt.show()

# Multiple plots
plt.plot(x, np.sin(x), label='sin(x)')
plt.plot(x, np.cos(x), label='cos(x)')
plt.legend()
plt.show()

# Scatter plot
x = np.random.rand(50)
y = np.random.rand(50)
plt.scatter(x, y)
plt.show()

# Bar chart
categories = ['A', 'B', 'C', 'D']
values = [23, 45, 56, 78]
plt.bar(categories, values)
plt.show()

# Histogram
data = np.random.randn(1000)
plt.hist(data, bins=30)
plt.show()

# Subplots
fig, axes = plt.subplots(2, 2, figsize=(10, 8))
axes[0, 0].plot(x, np.sin(x))
axes[0, 1].plot(x, np.cos(x))
axes[1, 0].scatter(x[:20], y[:20])
axes[1, 1].hist(data, bins=20)
plt.tight_layout()
plt.show()

# Install: pip install matplotlib
```

## Database

### SQLAlchemy

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# Create engine
engine = create_engine('sqlite:///example.db')

# Base class for models
Base = declarative_base()

# Define model
class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    name = Column(String)
    email = Column(String, unique=True)
    age = Column(Integer)

# Create tables
Base.metadata.create_all(engine)

# Create session
Session = sessionmaker(bind=engine)
session = Session()

# Create
user = User(name='Alice', email='alice@example.com', age=30)
session.add(user)
session.commit()

# Query
users = session.query(User).all()
user = session.query(User).filter_by(name='Alice').first()
users = session.query(User).filter(User.age > 25).all()

# Update
user = session.query(User).filter_by(name='Alice').first()
user.age = 31
session.commit()

# Delete
session.delete(user)
session.commit()

# Install: pip install sqlalchemy
```

### Redis

```python
import redis

# Connect
r = redis.Redis(host='localhost', port=6379, db=0)

# Strings
r.set('key', 'value')
value = r.get('key')
r.incr('counter')

# Expiration
r.setex('temp', 60, 'expires in 60 seconds')

# Lists
r.lpush('mylist', 'item1')
r.rpush('mylist', 'item2')
items = r.lrange('mylist', 0, -1)

# Sets
r.sadd('myset', 'member1')
r.sadd('myset', 'member2')
members = r.smembers('myset')

# Hashes
r.hset('user:1', 'name', 'Alice')
r.hset('user:1', 'age', 30)
name = r.hget('user:1', 'name')
user = r.hgetall('user:1')

# Pub/Sub
pubsub = r.pubsub()
pubsub.subscribe('channel')
for message in pubsub.listen():
    print(message)

# Install: pip install redis
```

## Testing and Quality

### pytest

```python
# test_math.py
import pytest

def add(a, b):
    return a + b

def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0

@pytest.mark.parametrize("a,b,expected", [
    (1, 2, 3),
    (5, 5, 10),
    (-1, 1, 0),
])
def test_add_parametrized(a, b, expected):
    assert add(a, b) == expected

# Run: pytest
# Install: pip install pytest
```

### Black (Code Formatter)

```bash
# Install
pip install black

# Format file
black myfile.py

# Format directory
black src/

# Check without modifying
black --check src/

# Configuration: pyproject.toml
# [tool.black]
# line-length = 88
# target-version = ['py38']
```

### mypy (Type Checker)

```bash
# Install
pip install mypy

# Check types
mypy myfile.py

# Configuration: mypy.ini
# [mypy]
# python_version = 3.11
# warn_return_any = True
# disallow_untyped_defs = True
```

## CLI Development

### Click

```python
import click

@click.command()
@click.option('--count', default=1, help='Number of greetings')
@click.option('--name', prompt='Your name', help='The person to greet')
def hello(count, name):
    """Simple program that greets NAME for a total of COUNT times."""
    for _ in range(count):
        click.echo(f'Hello, {name}!')

@click.group()
def cli():
    """Main CLI group."""
    pass

@cli.command()
def init():
    """Initialize project."""
    click.echo('Initializing...')

@cli.command()
@click.argument('filename')
def process(filename):
    """Process a file."""
    click.echo(f'Processing {filename}...')

if __name__ == '__main__':
    cli()

# Install: pip install click
```

### Rich (Terminal Formatting)

```python
from rich.console import Console
from rich.table import Table
from rich.progress import track
import time

console = Console()

# Styled text
console.print("Hello", style="bold red")
console.print("[bold cyan]Styled[/bold cyan] text")

# Tables
table = Table(title="Users")
table.add_column("Name", style="cyan")
table.add_column("Age", style="magenta")
table.add_row("Alice", "30")
table.add_row("Bob", "25")
console.print(table)

# Progress bar
for i in track(range(100), description="Processing..."):
    time.sleep(0.01)

# JSON
data = {"name": "Alice", "age": 30}
console.print_json(data=data)

# Install: pip install rich
```

## Utilities

### python-dotenv

```python
from dotenv import load_dotenv
import os

# Load .env file
load_dotenv()

# Access variables
api_key = os.getenv('API_KEY')
database_url = os.getenv('DATABASE_URL')

# .env file:
# API_KEY=your-api-key
# DATABASE_URL=postgresql://localhost/mydb

# Install: pip install python-dotenv
```

### Pydantic (Data Validation)

```python
from pydantic import BaseModel, EmailStr, Field, validator
from typing import Optional

class User(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    email: EmailStr
    age: int = Field(..., ge=0, le=150)
    is_active: bool = True

    @validator('name')
    def name_must_not_be_empty(cls, v):
        if not v.strip():
            raise ValueError('name cannot be empty')
        return v

# Usage
user = User(name="Alice", email="alice@example.com", age=30)
print(user.dict())

# Validation error
try:
    invalid = User(name="", email="invalid", age=-5)
except Exception as e:
    print(e)

# Install: pip install pydantic[email]
```

### loguru (Logging)

```python
from loguru import logger

# Basic logging
logger.debug("Debug message")
logger.info("Info message")
logger.warning("Warning message")
logger.error("Error message")
logger.critical("Critical message")

# File logging
logger.add("file.log", rotation="500 MB")

# Structured logging
logger.info("User {name} logged in", name="Alice")

# Exception logging
try:
    1 / 0
except Exception:
    logger.exception("An error occurred")

# Install: pip install loguru
```

## Async Libraries

### aiohttp

```python
import aiohttp
import asyncio

async def fetch(session, url):
    async with session.get(url) as response:
        return await response.text()

async def main():
    async with aiohttp.ClientSession() as session:
        # Single request
        html = await fetch(session, 'https://example.com')

        # Multiple concurrent requests
        urls = [
            'https://example.com',
            'https://example.org',
            'https://example.net',
        ]
        tasks = [fetch(session, url) for url in urls]
        results = await asyncio.gather(*tasks)

        return results

asyncio.run(main())

# Install: pip install aiohttp
```

### asyncpg (PostgreSQL)

```python
import asyncio
import asyncpg

async def main():
    # Connect
    conn = await asyncpg.connect(
        user='user',
        password='password',
        database='database',
        host='localhost'
    )

    # Execute query
    rows = await conn.fetch('SELECT * FROM users')
    for row in rows:
        print(row['name'], row['email'])

    # Insert
    await conn.execute(
        'INSERT INTO users(name, email) VALUES($1, $2)',
        'Alice', 'alice@example.com'
    )

    # Close
    await conn.close()

asyncio.run(main())

# Install: pip install asyncpg
```

> **Good to know:** FastAPI is faster than Flask and includes automatic API documentation. Requests is the de facto standard for HTTP, but httpx adds async support. NumPy powers most data science libraries. Pandas is essential for data manipulation. SQLAlchemy is the most popular Python ORM. Redis is great for caching and pub/sub. pytest is more powerful than unittest. Black formats code with zero configuration. Click makes CLI development simple. Rich makes terminal output beautiful. python-dotenv manages environment variables. Pydantic validates data with type hints. loguru simplifies logging. aiohttp is the async equivalent of requests. Use virtual environments for all projects.

---

## References

- [PyPI - Python Package Index](https://pypi.org/)
- [Flask Documentation](https://flask.palletsprojects.com/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Requests Documentation](https://requests.readthedocs.io/)
- [NumPy Documentation](https://numpy.org/doc/)
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [SQLAlchemy Documentation](https://docs.sqlalchemy.org/)
- [pytest Documentation](https://docs.pytest.org/)
- [Awesome Python - Curated List](https://github.com/vinta/awesome-python)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
