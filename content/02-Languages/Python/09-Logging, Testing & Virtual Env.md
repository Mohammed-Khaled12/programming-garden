
## `__name__` and `__main__`

`__name__` is a built-in variable that exists in every Python file.

**C++ comparison:** the compiler always looks for `int main()` to start the program. Python has no mandatory entry point: when you run a file, its code executes line by line from top to bottom.

### The problem

Say a helper file contains functions, plus a couple of `print` calls at the bottom to test them. The moment another file does `import` on it, Python runs those test lines too, without you asking. This gets serious once you split a project into modules.

### The solution

Python sets `__name__` differently depending on **how the file is being used**:

| How the file is used | Value of `__name__` |
|---|---|
| Run directly (`python rocket_math.py`) | `"__main__"` |
| Imported from another file (`import rocket_math`) | The module's name, `"rocket_math"` |

```python
# File 1: rocket_math.py (helper module)

def calculate_thrust(mass, acceleration):
    return mass * acceleration


# This block only runs when THIS file is executed directly.
# When the file is imported, __name__ is "rocket_math", so the condition is False.
if __name__ == "__main__":
    print("Running rocket_math.py directly for testing")
    test_result = calculate_thrust(1000, 9.8)
    print(f"Test Thrust: {test_result}")
```

```python
# File 2: nasa_api.py (main project file)

# Importing runs rocket_math.py, but its __main__ block is skipped
import rocket_math

print("Starting NASA API Server...")

actual_thrust = rocket_math.calculate_thrust(5000, 12.5)
print(f"Actual Thrust for API: {actual_thrust}")
```

Running `python rocket_math.py` prints the test lines. Running `python nasa_api.py` prints only:

```
Starting NASA API Server...
Actual Thrust for API: 62500.0
```

### Notes

- `if __name__ == "__main__":` is a **convention**, not a language requirement, and it isn't a real `main()`. A common clean pattern is to put your logic in a `def main():` function and call it under this guard.
- **FastAPI:** `uvicorn main:app` *imports* your `main` module, so `__name__` is `"main"` there and the guard block does **not** run. Code under the guard (such as a `uvicorn.run(...)` call) only runs when you start the file with `python main.py`.

---

## Timeit

**C++ comparison:** in C++ you'd benchmark with `<chrono>` by subtracting the start time from the end time. Python has the `time` module for that, but also a dedicated tool for measuring small snippets: **`timeit`**.

### Why `timeit` is better than subtracting two timestamps

1. **Garbage collector:** Python may pause your code to clean memory, and that pause would be counted against your code. `timeit` temporarily **disables the garbage collector** while it measures.
2. **System noise:** CPU speed varies with whatever else is running. `timeit` runs your snippet **many times** (1,000,000 by default) so one-off noise averages out.

### Parameters

```python
timeit.timeit(stmt, setup, timer, number)
```

| Parameter | Meaning | Default |
|---|---|---|
| `stmt` | The code to measure (a string, or a callable) | `"pass"` |
| `setup` | Code that runs once before measuring (imports, creating data) and isn't timed | `"pass"` |
| `timer` | The timer function to use | The platform's best timer |
| `number` | How many times to run `stmt` | 1,000,000 |

### Example: for loop vs list comprehension

```python
import timeit

# Method 1: standard for loop (C++ style), code stored as a multi-line string
loop_code = """
my_list = []
for i in range(1000):
    my_list.append(i)
"""

# Method 2: list comprehension (Pythonic style)
comprehension_code = """
my_list = [i for i in range(1000)]
"""

# Run each one 10,000 times
time_loop = timeit.timeit(stmt=loop_code, number=10000)
time_comp = timeit.timeit(stmt=comprehension_code, number=10000)

print(f"For Loop Time: {time_loop:.5f} seconds")
print(f"List Comprehension Time: {time_comp:.5f} seconds")

# Sample output (your numbers will differ):
# For Loop Time: 0.21141 seconds
# List Comprehension Time: 0.17631 seconds
```

The list comprehension is typically noticeably faster, because the loop version pays for an `append` method lookup and call on every iteration.

### Things worth knowing

- `timeit.timeit()` returns the **total** time for all `number` runs, not the average. Divide by `number` to get the time per run.
- To reduce noise further, use `timeit.repeat(stmt, repeat=5, number=10000)`, which returns a list of totals. Take the **minimum**, since slower runs are the ones disturbed by other processes.
- `stmt` can be a callable, so you don't have to write code inside a string: `timeit.timeit(lambda: [i for i in range(1000)], number=10000)`.
- Command line: `python -m timeit "[i for i in range(1000)]"`.
- For timing one specific block manually, use `time.perf_counter()`, not `time.time()`. It has higher resolution and doesn't jump when the system clock is adjusted.

---

## Logging

**C++ comparison:** simple C++ projects use `std::cout`, and real servers use an external library such as `spdlog`. Python ships the **`logging`** module built in, and it's the professional replacement for `print()`.

### Why `logging` beats `print()` in a backend

1. **Permanent record:** `print()` goes to the screen and is gone when the server stops. Logging can write to a file, so if the server crashes while you're asleep, you can see exactly when and where the error happened.
2. **Severity levels:** you can tell the logger to ignore minor messages and keep only the important ones.
3. **Automatic formatting:** time, date, level, and module name are added to every message for you.

### The 5 levels

| Level | Value | Use |
|---|---|---|
| `DEBUG` | 10 | Fine detail while developing, to check values |
| `INFO` | 20 | Normal events confirming things work (server started, user logged in) |
| `WARNING` | 30 | Something odd happened but the code keeps running (disk space getting low) |
| `ERROR` | 40 | A specific operation failed (database connection failed) |
| `CRITICAL` | 50 | A disaster that may stop the program (out of memory) |

> ⚠️ The **default level is `WARNING`**. Without configuring it, `DEBUG` and `INFO` messages are silently dropped. That's the usual reason "my `logging.info` prints nothing".

### Basic setup

```python
import logging

# Configure HOW and WHERE to log
logging.basicConfig(
    # File where logs are saved (created automatically if missing)
    filename="nasa_api_server.log",

    # Minimum level to capture: DEBUG (10) and everything above it
    level=logging.DEBUG,

    # Message format: [time] - [level] - [message]
    format="%(asctime)s - %(levelname)s - %(message)s",

    # Date format
    datefmt="%Y-%m-%d %H:%M:%S",
)


def connect_to_database():
    logging.info("Attempting to connect to the main database...")

    try:
        db_status = False   # simulating a failed connection

        if not db_status:
            logging.error("Database connection failed! Invalid credentials.")

    except Exception as e:
        logging.critical(f"System crash during database initialization: {e}")


logging.debug("Starting thrust calculation module...")
logging.warning("API key will expire in 2 days!")

connect_to_database()
```

The file `nasa_api_server.log` now contains lines like:

```
2026-10-22 11:15:00 - DEBUG - Starting thrust calculation module...
2026-10-22 11:15:00 - WARNING - API key will expire in 2 days!
2026-10-22 11:15:00 - INFO - Attempting to connect to the main database...
2026-10-22 11:15:00 - ERROR - Database connection failed! Invalid credentials.
```

### A better version for real projects

```python
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s - %(levelname)s - %(name)s - %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S",
    # Log to a file AND to the console
    handlers=[logging.FileHandler("app.log"), logging.StreamHandler()],
)

# One logger per module, named after the module
logger = logging.getLogger(__name__)

logger.debug("Starting thrust calculation module...")
logger.warning("API key will expire in %s days!", 2)

try:
    result = 1 / 0
except ZeroDivisionError:
    # Logs at ERROR level AND includes the full traceback
    logger.exception("Calculation failed")
```

Why this is better:

- `logging.getLogger(__name__)` gives each module its own named logger (the `%(name)s` field shows which module a message came from). This is where `__name__` shows up again.
- `logger.exception(...)` inside an `except` block records the **full traceback**. `logging.critical(f"... {e}")` only records the message and loses where the error happened.
- Passing values as arguments (`"... %s days", 2`) instead of building an f-string lets logging skip the formatting work when that level is disabled.

### Gotchas

- `basicConfig()` does nothing if the root logger already has handlers (for example when a framework configured logging first), unless you pass `force=True` (Python 3.8+).
- **In Docker, log to the console (stdout), not only to a file.** Container platforms such as Azure Container Apps collect what your app prints to stdout and stderr. A log file inside the container disappears when the container is replaced.
- The request lines you see when running a FastAPI app (such as `GET /users 200 OK`) come from **Uvicorn**, the server, which uses this same `logging` system.

---

## Unit Testing with `unittest`

**C++ comparison:** professional C++ testing means external frameworks like Google Test or Catch2. Python ships **`unittest`** built in.

Unit testing means writing code that tests code. Instead of running the program and typing inputs by hand, you write a class containing all the cases (correct, wrong, edge cases) and run them all at once. Python reports what passed and what failed.

### How `unittest` works

It's built on the OOP you just studied:

1. **Inheritance:** your test class must inherit from `unittest.TestCase`.
2. **Naming convention:** only methods whose names start with `test_` are run as tests. Any other name is ignored.
3. **Assertions:** instead of `if/else`, you call ready-made methods such as `assertEqual`, `assertTrue`, and `assertRaises`.

```python
import unittest


# 1. The logic we want to test
def calculate_payload_weight(mass, gravity):
    if mass < 0 or gravity < 0:
        raise ValueError("Mass and gravity cannot be negative!")
    return mass * gravity


# 2. The test class: inherits from unittest.TestCase
class TestPayloadCalculations(unittest.TestCase):

    # Test case 1: normal behavior (the method name MUST start with test_)
    def test_calculate_payload_normal(self):
        result = calculate_payload_weight(10, 9.8)
        self.assertAlmostEqual(result, 98.0)

    # Test case 2: zero values (edge case)
    def test_calculate_payload_zero(self):
        result = calculate_payload_weight(0, 9.8)
        self.assertEqual(result, 0)

    # Test case 3: error handling (does the exact exception get raised?)
    def test_calculate_payload_negative(self):
        with self.assertRaises(ValueError):
            calculate_payload_weight(-5, 9.8)


# 3. Run the tests when this file is executed directly
if __name__ == "__main__":
    unittest.main()
```

Running it prints one dot per passing test, then a summary:

```
...
Ran 3 tests in 0.000s

OK
```

(Python also prints a separator line before `Ran 3 tests`; it's left out here.)

### Common assertions

| Assertion | Checks that |
|---|---|
| `assertEqual(a, b)` | `a == b` |
| `assertNotEqual(a, b)` | `a != b` |
| `assertTrue(x)` / `assertFalse(x)` | `x` is truthy / falsy |
| `assertIn(a, b)` | `a` is in `b` |
| `assertIsNone(x)` | `x` is `None` |
| `assertAlmostEqual(a, b)` | two floats are equal to about 7 decimal places |
| `assertRaises(Error)` | the code inside the `with` block raises that exception |

> **Use `assertAlmostEqual` for floats.** Floating-point arithmetic is inexact (`0.1 + 0.2 != 0.3`), so exact `assertEqual` on float results can fail for no good reason. (`10 * 9.8 == 98.0` happens to pass, but don't count on that.)

### Real project layout

In practice the logic and its tests live in **separate files**:

```
project/
    payload.py          # the logic
    test_payload.py     # imports from payload.py and holds the TestCase classes
```

From the project folder, run everything with test discovery. It finds files named `test*.py` automatically, so you don't need the `unittest.main()` block:

```bash
python -m unittest            # discover and run all tests
python -m unittest -v         # verbose: print each test name and result
```

`setUp()` and `tearDown()` methods on the class run before and after **every** test, which is how you prepare and clean up shared test data.

### unittest vs pytest

FastAPI's own documentation tests with **pytest** (together with FastAPI's `TestClient`). It's not built in, so install it with `pip install pytest`. The concepts are identical, but tests are plain functions with plain `assert`:

```python
import pytest


def test_normal():
    assert calculate_payload_weight(10, 9.8) == pytest.approx(98.0)


def test_negative():
    with pytest.raises(ValueError):
        calculate_payload_weight(-5, 9.8)
```

---

## Virtual Environments

**C++ comparison:** C++ either links libraries into the executable (static linking) or relies on system DLLs / shared objects. With a package manager like `vcpkg` or `Conan`, each project keeps its own isolated set of dependencies.

Python's default is dangerous. If you run `pip install fastapi` in a terminal, the package installs **globally** on the OS. If one project needs an old version and another needs a new one, they conflict immediately (**dependency hell**). The fix is a **virtual environment (`venv`)**.

### What happens under the hood

`venv` creates a folder inside your project containing a copy (or symlink) of the `python` binary and of `pip`. When you **activate** it, the terminal session's `$PATH` is changed so that `python` and `pip` resolve to the ones in your project folder first, instead of the system ones. Your project lives in a closed box.

### Commands (Linux / Zsh)

```bash
# 1. Create the virtual environment
# The folder is usually called .venv (the leading dot hides it on Linux)
python3 -m venv .venv

# 2. Activate it (changes $PATH for this terminal session only)
# You'll see (.venv) at the start of your prompt
source .venv/bin/activate
# On Windows (PowerShell): .venv\Scripts\Activate.ps1

# 3. Confirm you're using the environment's Python
which python

# 4. Install packages (isolated from the OS)
pip install fastapi uvicorn

# 5. Save the dependencies so teammates can reproduce your setup
pip freeze > requirements.txt

# A teammate who pulls your code builds their own environment with:
pip install -r requirements.txt

# 6. Leave the environment (restores the original $PATH)
deactivate
```

Treat this as **step zero** before writing any line of a backend project.

### Important notes

- **Never commit `.venv` to GitHub.** It grows with every package and it's machine-specific. Put it in `.gitignore` and commit only your code and `requirements.txt`:

  ```
  .venv/
  __pycache__/
  ```

- **Debian/Ubuntu protects the system Python.** On recent versions (including Python 3.12 setups), running `pip install` outside a venv fails with an `externally-managed-environment` error. A virtual environment is the right fix; don't force the install with `--break-system-packages`. If `python3 -m venv` itself fails, install the `python3-venv` package with `apt`.
- **Activation is optional.** You can call the environment's interpreter directly, for example `.venv/bin/python script.py`, which is handy in scripts and CI.
- **Docker:** inside a Docker image you usually don't need a venv, because the container is already an isolated environment. You normally just run `pip install -r requirements.txt` in the Dockerfile. The `requirements.txt` you generate here is what feeds it.
- `pip freeze` lists **every** installed package, including the dependencies of your dependencies, pinned to exact versions. That's good for reproducible builds.