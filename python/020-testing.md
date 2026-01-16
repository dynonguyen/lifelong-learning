# Testing

## pytest

### Basic Tests

```python
# test_math.py

def add(a, b):
    return a + b

def test_add():
    """Test addition function."""
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
    assert add(0, 0) == 0

def test_add_negative():
    """Test with negative numbers."""
    assert add(-2, -3) == -5

# Run tests:
# pytest test_math.py
# pytest                 # Run all tests
# pytest -v             # Verbose
# pytest -k "add"       # Run tests matching pattern
```

### Test Organization

```python
# Test files: test_*.py or *_test.py
# Test functions: test_*()
# Test classes: Test*

class TestCalculator:
    """Group related tests."""

    def test_addition(self):
        assert 2 + 2 == 4

    def test_subtraction(self):
        assert 5 - 3 == 2

    def test_multiplication(self):
        assert 3 * 4 == 12

# Test structure:
"""
project/
    src/
        calculator.py
    tests/
        __init__.py
        test_calculator.py
        test_utils.py
"""
```

### Fixtures

```python
import pytest

# Simple fixture
@pytest.fixture
def sample_data():
    """Provide sample data for tests."""
    return [1, 2, 3, 4, 5]

def test_sum(sample_data):
    assert sum(sample_data) == 15

def test_length(sample_data):
    assert len(sample_data) == 5

# Fixture with setup and teardown
@pytest.fixture
def database():
    """Database fixture."""
    db = create_database()
    db.connect()

    yield db  # Provide to test

    # Teardown
    db.disconnect()
    db.cleanup()

def test_query(database):
    result = database.query("SELECT * FROM users")
    assert len(result) > 0

# Fixture scope
@pytest.fixture(scope="module")
def expensive_resource():
    """Created once per module."""
    resource = create_expensive_resource()
    yield resource
    resource.cleanup()

# Scopes: function, class, module, package, session

# Parametrized fixture
@pytest.fixture(params=["sqlite", "postgres", "mysql"])
def database_type(request):
    """Test with multiple database types."""
    return request.param

def test_database_connection(database_type):
    db = connect_to(database_type)
    assert db.is_connected()
```

### Parametrize Tests

```python
import pytest

# Single parameter
@pytest.mark.parametrize("input,expected", [
    (2, 4),
    (3, 9),
    (4, 16),
    (5, 25),
])
def test_square(input, expected):
    assert input ** 2 == expected

# Multiple parameters
@pytest.mark.parametrize("a,b,expected", [
    (1, 1, 2),
    (2, 3, 5),
    (10, 20, 30),
    (-1, 1, 0),
])
def test_addition(a, b, expected):
    assert a + b == expected

# Parametrize with IDs
@pytest.mark.parametrize("input,expected", [
    (2, 4),
    (3, 9),
], ids=["two", "three"])
def test_with_ids(input, expected):
    assert input ** 2 == expected

# Multiple parametrize decorators (Cartesian product)
@pytest.mark.parametrize("x", [1, 2])
@pytest.mark.parametrize("y", [3, 4])
def test_multiply(x, y):
    assert x * y == x * y  # All combinations tested
```

### Assertions and Exceptions

```python
import pytest

def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b

# Test exceptions
def test_divide_by_zero():
    with pytest.raises(ValueError):
        divide(10, 0)

# Check exception message
def test_divide_by_zero_message():
    with pytest.raises(ValueError, match="Cannot divide by zero"):
        divide(10, 0)

# Capture exception details
def test_exception_details():
    with pytest.raises(ValueError) as exc_info:
        divide(10, 0)

    assert "divide by zero" in str(exc_info.value)
    assert exc_info.type == ValueError

# Approximate comparisons
def test_approximate():
    assert 0.1 + 0.2 == pytest.approx(0.3)
    assert 10 == pytest.approx(10.1, abs=0.2)
    assert 100 == pytest.approx(99, rel=0.01)  # 1% tolerance

# Collection comparisons
def test_lists():
    assert [1, 2, 3] == [1, 2, 3]
    assert {"a": 1, "b": 2} == {"a": 1, "b": 2}
    assert {1, 2, 3} == {3, 2, 1}
```

### Markers

```python
import pytest

# Mark tests
@pytest.mark.slow
def test_slow_operation():
    """Slow test."""
    time.sleep(2)
    assert True

@pytest.mark.integration
def test_api_integration():
    """Integration test."""
    assert True

@pytest.mark.skip(reason="Not implemented yet")
def test_future_feature():
    """Skipped test."""
    assert False

@pytest.mark.skipif(sys.version_info < (3, 10), reason="Requires Python 3.10+")
def test_python_310_feature():
    """Conditional skip."""
    assert True

@pytest.mark.xfail(reason="Known bug")
def test_known_bug():
    """Expected to fail."""
    assert False

# Run specific markers:
# pytest -m slow           # Run slow tests
# pytest -m "not slow"     # Skip slow tests
# pytest -m "integration"  # Run integration tests
```

### Configuration

```ini
# pytest.ini
[tool.pytest.ini_options]
minversion = "7.0"
testpaths = ["tests"]
python_files = ["test_*.py", "*_test.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
markers = [
    "slow: marks tests as slow",
    "integration: marks tests as integration tests",
    "unit: marks tests as unit tests"
]
addopts = "-v --tb=short"

# Or in pyproject.toml
[tool.pytest.ini_options]
testpaths = ["tests"]
markers = [
    "slow: slow tests",
    "integration: integration tests"
]
```

### Coverage

```bash
# Install pytest-cov
pip install pytest-cov

# Run with coverage
pytest --cov=src tests/

# Generate HTML report
pytest --cov=src --cov-report=html tests/

# Set coverage threshold
pytest --cov=src --cov-fail-under=80 tests/
```

```python
# .coveragerc
[run]
source = src
omit =
    */tests/*
    */venv/*

[report]
exclude_lines =
    pragma: no cover
    def __repr__
    raise AssertionError
    raise NotImplementedError
    if __name__ == .__main__.:
```

## unittest (Standard Library)

### Basic unittest

```python
import unittest

def add(a, b):
    return a + b

class TestMath(unittest.TestCase):
    """Test mathematical operations."""

    def test_add(self):
        """Test addition."""
        self.assertEqual(add(2, 3), 5)
        self.assertEqual(add(-1, 1), 0)

    def test_add_negative(self):
        """Test negative numbers."""
        self.assertEqual(add(-2, -3), -5)

    def test_add_floats(self):
        """Test float addition."""
        self.assertAlmostEqual(add(0.1, 0.2), 0.3)

if __name__ == "__main__":
    unittest.main()

# Run:
# python test_math.py
# python -m unittest test_math.py
# python -m unittest discover
```

### Assertions

```python
import unittest

class TestAssertions(unittest.TestCase):

    def test_equality(self):
        self.assertEqual(1 + 1, 2)
        self.assertNotEqual(1, 2)

    def test_truth(self):
        self.assertTrue(True)
        self.assertFalse(False)

    def test_none(self):
        self.assertIsNone(None)
        self.assertIsNotNone(0)

    def test_membership(self):
        self.assertIn(1, [1, 2, 3])
        self.assertNotIn(4, [1, 2, 3])

    def test_types(self):
        self.assertIsInstance(42, int)
        self.assertNotIsInstance("42", int)

    def test_exceptions(self):
        with self.assertRaises(ValueError):
            int("abc")

    def test_regex(self):
        self.assertRegex("hello world", r"hello")
```

### setUp and tearDown

```python
import unittest

class TestDatabase(unittest.TestCase):

    def setUp(self):
        """Run before each test."""
        self.db = Database()
        self.db.connect()

    def tearDown(self):
        """Run after each test."""
        self.db.disconnect()

    @classmethod
    def setUpClass(cls):
        """Run once before all tests."""
        cls.shared_resource = create_resource()

    @classmethod
    def tearDownClass(cls):
        """Run once after all tests."""
        cls.shared_resource.cleanup()

    def test_query(self):
        result = self.db.query("SELECT * FROM users")
        self.assertGreater(len(result), 0)
```

## Mocking

### unittest.mock

```python
from unittest.mock import Mock, MagicMock, patch

# Create mock object
mock = Mock()

# Set return value
mock.return_value = 42
assert mock() == 42

# Set side effect (function)
mock.side_effect = lambda x: x * 2
assert mock(5) == 10

# Set side effect (exception)
mock.side_effect = ValueError("Error")
# mock()  # Raises ValueError

# Check calls
mock = Mock()
mock(1, 2, key="value")
mock.assert_called_once_with(1, 2, key="value")
assert mock.call_count == 1

# Mock object attributes
mock = Mock()
mock.method.return_value = "result"
assert mock.method() == "result"

# MagicMock (with magic methods)
mock = MagicMock()
mock.__len__.return_value = 5
assert len(mock) == 5
```

### Patching

```python
from unittest.mock import patch
import requests

# Original function
def get_weather(city):
    response = requests.get(f"https://api.weather.com/{city}")
    return response.json()

# Patch function
@patch('requests.get')
def test_get_weather(mock_get):
    # Configure mock
    mock_get.return_value.json.return_value = {"temp": 20}

    # Test
    result = get_weather("NYC")

    # Assertions
    assert result == {"temp": 20}
    mock_get.assert_called_once_with("https://api.weather.com/NYC")

# Patch as context manager
def test_get_weather_context():
    with patch('requests.get') as mock_get:
        mock_get.return_value.json.return_value = {"temp": 20}
        result = get_weather("NYC")
        assert result == {"temp": 20}

# Patch object attribute
class MyClass:
    def __init__(self):
        self.value = 10

    def get_value(self):
        return self.value

@patch.object(MyClass, 'value', 42)
def test_patched_attribute():
    obj = MyClass()
    assert obj.value == 42

# Multiple patches
@patch('module.function1')
@patch('module.function2')
def test_multiple_patches(mock_func2, mock_func1):
    # Note: patches are applied bottom-up
    pass
```

### Mock Best Practices

```python
from unittest.mock import patch, Mock

# 1. Patch where it's used, not where it's defined
# If module_a imports function from module_b:
# from module_b import some_function
# Patch as 'module_a.some_function', not 'module_b.some_function'

# 2. Use spec to ensure mock has correct attributes
@patch('requests.get', spec=requests.get)
def test_with_spec(mock_get):
    mock_get.return_value.json.return_value = {}
    # mock_get.non_existent_method()  # AttributeError

# 3. Use autospec for stricter mocking
@patch('module.MyClass', autospec=True)
def test_with_autospec(mock_class):
    instance = mock_class.return_value
    instance.method.return_value = 42

# 4. Create mock that tracks calls
mock = Mock()
mock.method(1, 2, 3)
assert mock.method.call_args == ((1, 2, 3), {})
assert mock.method.call_args.args == (1, 2, 3)
assert mock.method.call_args.kwargs == {}

# 5. Reset mock between tests
mock = Mock()
mock.method()
mock.reset_mock()
assert mock.method.call_count == 0
```

## Testing Best Practices

```python
# 1. Test one thing per test
def test_user_creation():
    """Test only user creation."""
    user = User("Alice", "alice@example.com")
    assert user.name == "Alice"

def test_user_email():
    """Test only email validation."""
    user = User("Alice", "alice@example.com")
    assert user.email == "alice@example.com"

# 2. Use descriptive test names
def test_invalid_email_raises_value_error():
    with pytest.raises(ValueError):
        User("Alice", "invalid-email")

# 3. Arrange-Act-Assert pattern
def test_deposit():
    # Arrange
    account = BankAccount(balance=100)

    # Act
    account.deposit(50)

    # Assert
    assert account.balance == 150

# 4. Use fixtures for common setup
@pytest.fixture
def user():
    return User("Alice", "alice@example.com")

def test_with_fixture(user):
    assert user.name == "Alice"

# 5. Don't test implementation details
# Bad: Testing internal state
def test_internal_cache():
    obj = MyClass()
    obj.method()
    assert obj._cache == {}  # Testing internal

# Good: Testing behavior
def test_method_result():
    obj = MyClass()
    result = obj.method()
    assert result == expected_value

# 6. Keep tests independent
# Each test should run in isolation
# Don't rely on test execution order

# 7. Use meaningful assertions
# Bad
assert len(result) > 0

# Good
assert len(result) == 3, f"Expected 3 results, got {len(result)}"

# 8. Test edge cases
def test_empty_list():
    assert sum([]) == 0

def test_single_item():
    assert sum([5]) == 5

def test_negative_numbers():
    assert sum([-1, -2, -3]) == -6
```

## Test-Driven Development (TDD)

```python
# 1. Write test first (it fails)
def test_calculate_discount():
    price = 100
    discount = 0.1
    expected = 90
    assert calculate_discount(price, discount) == expected

# 2. Write minimal code to pass
def calculate_discount(price, discount):
    return price * (1 - discount)

# 3. Refactor
def calculate_discount(price: float, discount: float) -> float:
    """Calculate price after discount."""
    if not 0 <= discount <= 1:
        raise ValueError("Discount must be between 0 and 1")
    return price * (1 - discount)

# 4. Add more tests
def test_calculate_discount_invalid():
    with pytest.raises(ValueError):
        calculate_discount(100, 1.5)

# Repeat: Red -> Green -> Refactor
```

## Integration Testing

```python
import pytest
import requests

# Test actual API integration
@pytest.mark.integration
def test_api_integration():
    response = requests.get("https://api.github.com")
    assert response.status_code == 200
    assert "X-GitHub-Request-Id" in response.headers

# Test database integration
@pytest.mark.integration
def test_database_integration(database):
    user = User(name="Alice", email="alice@example.com")
    database.save(user)

    retrieved = database.get_user_by_email("alice@example.com")
    assert retrieved.name == "Alice"

# Test with Docker containers (testcontainers)
from testcontainers.postgres import PostgresContainer

@pytest.fixture(scope="module")
def postgres_container():
    with PostgresContainer("postgres:15") as postgres:
        yield postgres

def test_with_postgres(postgres_container):
    conn_string = postgres_container.get_connection_url()
    # Test with real Postgres
```

> **Good to know:** `pytest` is more popular and powerful than `unittest`. Test functions must start with `test_`. Use fixtures for setup and teardown code. Parametrize tests to run the same test with different inputs. Use markers to categorize and selectively run tests. `pytest.approx()` handles floating-point comparisons. Mock external dependencies in unit tests. Patch where objects are used, not where they're defined. Use `spec` or `autospec` for safer mocking. Follow AAA pattern: Arrange-Act-Assert. Test behavior, not implementation details. Keep tests independent and isolated. TDD: write test first, then code. Use integration tests for external systems. Aim for high code coverage but prioritize meaningful tests.

---

## References

- [pytest - Official Documentation](https://docs.pytest.org/)
- [unittest - Official Documentation](https://docs.python.org/3/library/unittest.html)
- [unittest.mock - Official Documentation](https://docs.python.org/3/library/unittest.mock.html)
- [pytest-cov - Coverage Plugin](https://pytest-cov.readthedocs.io/)
- [Real Python - Testing](https://realpython.com/pytest-python-testing/)
- [Test-Driven Development](https://testdriven.io/test-driven-development/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
