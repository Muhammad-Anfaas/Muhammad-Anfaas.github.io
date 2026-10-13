---
title: "Unit Testing with pytest"
date: 2026-10-13
draft: false
featureimage: "unit-feature.png"
heroStyle: "background"
showTableOfContents: true

---

# Introduction to Unit Testing with pytest

Unit testing is the practice of writing small, automated checks that verify individual pieces of your code (functions, methods, classes) work as expected. Instead of running your program by hand and eyeballing the output, you write tests once and run them in seconds, every time you change something. This catches bugs early, makes refactoring safe, and acts as living documentation of how your code is supposed to behave.

Before moving forward, we first need to understand what a unit test is and what makes a good one.

## What is a Unit Test?
A **unit** is the smallest testable piece of a program, usually a single function or method. A **unit test** calls that unit with some input and checks that the output (or behavior) matches what you expect.

A good unit test is:

- **Fast**: it runs in milliseconds, so you can run hundreds of them often
- **Isolated**: it doesn't depend on the network, a database, or other tests
- **Repeatable**: it gives the same result every time
- **Focused**: it checks one behavior, so a failure points to one problem

## The Arrange-Act-Assert Pattern
Most tests follow three steps:

1. **Arrange**: set up the data and objects you need
2. **Act**: call the code you want to test
3. **Assert**: check that the result is what you expected

Keeping this structure makes tests easy to read and easy to write.

## Why pytest?
Python ships with a built-in `unittest` module, but **pytest** is the most popular choice because it needs far less boilerplate. You write plain functions and use the normal `assert` statement. It also gives detailed failure messages, powerful fixtures, and a large plugin ecosystem.

---

## Getting Started
Install pytest with pip:

```bash
pip install pytest
```

Check that it works:

```bash
pytest --version
```

pytest finds tests automatically using naming conventions:

- Test files are named `test_*.py` or `*_test.py`
- Test functions start with `test_`
- Test classes start with `Test` (and have no `__init__` method)

A simple project layout looks like this:

```
my_project/
├── calculator.py
└── test_calculator.py
```

---

## Writing Your First Test
Let's start with a small module to test:

```python
# calculator.py

def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def multiply(a, b):
    return a * b

def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b
```

Now the tests:

```python
# test_calculator.py
from calculator import add, subtract, multiply, divide

def test_add():
    assert add(2, 3) == 5

def test_subtract():
    assert subtract(10, 4) == 6

def test_multiply():
    assert multiply(3, 4) == 12

def test_divide():
    assert divide(10, 2) == 5
```

Run all tests from the project folder:

```bash
pytest
```

**Output:**
```
============================= test session starts ==============================
collected 4 items

test_calculator.py ....                                                  [100%]

============================== 4 passed in 0.01s ===============================
```

Each dot is one passing test. That's all it takes to get started: plain functions and plain `assert`.

### What a Failure Looks Like
Suppose we accidentally wrote the wrong expected value:

```python
def test_add():
    assert add(2, 3) == 6
```

pytest shows exactly what went wrong:

```
================================== FAILURES ===================================
__________________________________ test_add ___________________________________

    def test_add():
>       assert add(2, 3) == 6
E       assert 5 == 6
E        +  where 5 = add(2, 3)

test_calculator.py:4: AssertionError
========================= 1 failed, 3 passed in 0.02s =========================
```

It shows the failing line, the actual value (`5`), and the expected value (`6`), so you rarely need a debugger to find the problem.

---

## Assertions
pytest uses the standard `assert` keyword, but it rewrites assertions behind the scenes to give rich failure details. Here are some common patterns:

```python
def test_assertion_examples():
    assert 5 > 3
    assert "py" in "pytest"
    assert [1, 2, 3] == [1, 2, 3]
    assert {"a": 1}.get("a") == 1
    assert not []          # an empty list is falsy
    assert isinstance(3.5, float)
```

### Comparing Floats
Floating-point numbers can't be compared exactly, because `0.1 + 0.2` is actually `0.30000000000000004`. Use `pytest.approx` instead:

```python
import pytest

def test_float_addition():
    assert 0.1 + 0.2 == pytest.approx(0.3)
```

---

## Testing for Exceptions
Good tests also check that code fails correctly. Use `pytest.raises` as a context manager to confirm an exception is raised:

```python
import pytest
from calculator import divide

def test_divide_by_zero():
    with pytest.raises(ValueError):
        divide(10, 0)

def test_divide_by_zero_message():
    with pytest.raises(ValueError, match="divide by zero"):
        divide(10, 0)
```

The test passes only if the exception is raised inside the `with` block. If no exception occurs, or a different type is raised, the test fails. The optional `match` argument checks the error message against a regular expression.

---

## Parametrized Tests
Often you want to run the same test with many inputs. Copy-pasting test functions is tedious, so use `@pytest.mark.parametrize`:

```python
import pytest
from calculator import add

@pytest.mark.parametrize("a, b, expected", [
    (2, 3, 5),
    (-1, 1, 0),
    (0, 0, 0),
    (10, -4, 6),
])
def test_add(a, b, expected):
    assert add(a, b) == expected
```

Run it with the `-v` (verbose) flag:

```bash
pytest -v
```

**Output:**
```
test_calculator.py::test_add[2-3-5] PASSED
test_calculator.py::test_add[-1-1-0] PASSED
test_calculator.py::test_add[0-0-0] PASSED
test_calculator.py::test_add[10--4-6] PASSED
```

One function becomes four independent tests. If one case fails, pytest reports exactly which inputs caused it, and the others still run.

---

## Fixtures
Many tests need the same setup, like creating an object or preparing some data. A **fixture** is a reusable piece of setup code that pytest passes into your tests automatically.

First, a class to test:

```python
# bank.py

class BankAccount:
    def __init__(self, balance=0):
        self.balance = balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit must be positive")
        self.balance += amount

    def withdraw(self, amount):
        if amount > self.balance:
            raise ValueError("Insufficient funds")
        self.balance -= amount
```

Now the tests, using a fixture:

```python
# test_bank.py
import pytest
from bank import BankAccount

@pytest.fixture
def account():
    return BankAccount(balance=100)

def test_deposit(account):
    account.deposit(50)
    assert account.balance == 150

def test_withdraw(account):
    account.withdraw(30)
    assert account.balance == 70

def test_withdraw_insufficient_funds(account):
    with pytest.raises(ValueError, match="Insufficient funds"):
        account.withdraw(500)
```

Any test that lists `account` as a parameter receives a **fresh** `BankAccount` with a balance of 100. Because each test gets its own object, tests can't affect each other, which keeps them isolated.

### Setup and Teardown with yield
If a fixture needs to clean up afterwards (closing a file, removing a temp record), use `yield`. Everything after the `yield` runs once the test finishes, even if it failed:

```python
@pytest.fixture
def database_connection():
    conn = open_connection()     # setup
    yield conn                   # the test runs here
    conn.close()                 # teardown
```

### Sharing Fixtures with conftest.py
Fixtures placed in a file named `conftest.py` are automatically available to every test in that folder and its subfolders, with no imports needed. This is the right place for fixtures used across several test files.

pytest also ships with useful built-in fixtures, such as `tmp_path` (a temporary directory for file tests) and `monkeypatch` (temporarily changing variables or environment settings).

---

## Isolating Code with Mocks
A unit test should test **your** code, not the internet. If a function calls an external API, the test would be slow and could fail for reasons unrelated to your logic. The solution is to replace the external call with a **mock**.

```python
# weather.py
import requests

def get_temperature(city):
    response = requests.get(f"https://api.example.com/weather?city={city}")
    response.raise_for_status()
    return response.json()["temp"]

def describe_weather(city):
    temp = get_temperature(city)
    if temp >= 30:
        return "Hot"
    elif temp >= 15:
        return "Mild"
    return "Cold"
```

We want to test the logic in `describe_weather` without making a real network call. Python's built-in `unittest.mock` works perfectly with pytest:

```python
# test_weather.py
from unittest.mock import patch
import weather

@patch("weather.get_temperature")
def test_describe_weather_hot(mock_get):
    mock_get.return_value = 35

    assert weather.describe_weather("Paris") == "Hot"
    mock_get.assert_called_once_with("Paris")

@patch("weather.get_temperature")
def test_describe_weather_cold(mock_get):
    mock_get.return_value = 5

    assert weather.describe_weather("Oslo") == "Cold"
```

`@patch("weather.get_temperature")` replaces the real function with a fake one for the duration of the test, and `return_value` controls what it returns. The test is now instant and never touches the network. (This example needs `pip install requests` because `weather.py` imports it.)

> **Note:** Patch the name **where it is used**, not where it is defined. Here, `describe_weather` looks up `get_temperature` inside the `weather` module, so we patch `"weather.get_temperature"`.

---

## Skipping and Expected Failures
pytest markers let you control which tests run:

```python
import sys
import pytest

@pytest.mark.skip(reason="Feature not implemented yet")
def test_future_feature():
    ...

@pytest.mark.skipif(sys.platform == "win32", reason="Does not run on Windows")
def test_unix_only():
    ...

@pytest.mark.xfail(reason="Known bug, fix in progress")
def test_known_bug():
    assert 1 + 1 == 3
```

- `skip` never runs the test
- `skipif` skips it only when a condition is true
- `xfail` runs it but expects it to fail, so it doesn't break your test run

---

## Useful Command-Line Options

| Command | What it does |
|---|---|
| `pytest` | Run all tests in the current folder and subfolders |
| `pytest -v` | Verbose output, showing each test name |
| `pytest test_bank.py` | Run one file |
| `pytest test_bank.py::test_deposit` | Run a single test |
| `pytest -k "withdraw"` | Run only tests whose name contains "withdraw" |
| `pytest -x` | Stop after the first failure |
| `pytest --lf` | Re-run only the tests that failed last time |
| `pytest -s` | Show `print()` output, which pytest hides by default |

To measure how much of your code the tests cover, install the `pytest-cov` plugin and run `pytest --cov`.

---

## Best Practices
- **Test one behavior per test**, and give the test a name that describes it, like `test_withdraw_insufficient_funds`.
- **Follow Arrange-Act-Assert** so every test reads the same way.
- **Keep tests independent.** One test should never rely on another having run first.
- **Test edge cases**, not just the happy path: empty input, zero, negative numbers, very large values, wrong types.
- **Test failures too**, using `pytest.raises` to confirm errors are raised when they should be.
- **Mock external dependencies** like networks, databases, and the clock, so tests stay fast and reliable.
- **Run tests often**, ideally automatically on every push using a CI service such as GitHub Actions.
- **Don't chase 100% coverage.** Coverage shows what code ran, not whether your assertions are meaningful.

---

## Conclusion
In this post we covered:

- What a **unit test** is and the qualities of a good one
- The **Arrange-Act-Assert** pattern
- Installing pytest and how it **discovers** tests by naming convention
- Writing tests with plain `assert`, and using `pytest.approx` for floats
- Testing errors with `pytest.raises`
- Running many inputs through one test with **parametrize**
- Reusing setup code with **fixtures** and `conftest.py`
- Isolating code from external services using **mocks**
- **Markers** like `skip`, `skipif` and `xfail`, and handy command-line options

Writing tests may feel like extra work at first, but it pays for itself quickly: you can change your code with confidence, catch bugs before your users do, and know right away when something breaks.