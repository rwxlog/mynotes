## 💻 Fundamental Programming Concepts

-----

### 🧩 1. Variables

**Definition:** A variable stores data that can change during program execution.

**Example (Python):**

```python
age = 25
name = "Alice"
```

-----

### 🔢 2. Data Types

**Definition:** Types of data that can be stored in variables.

| Type | Description | Example (Python) |
| :--- | :--- | :--- |
| `int` | Whole numbers | `x = 10` |
| `float` | Decimal numbers | `y = 3.14` |
| `str` | Text (string) | `name = "Bob"` |
| `bool` | True/False (Boolean) | `is_active = True` |
| Complex Types | `list`, `tuple`, `dict`, etc. | |

-----

### 🧮 3. Operators

**Definition:** Symbols that perform operations on data.

| Type | Examples | Use Case (Python) |
| :--- | :--- | :--- |
| **Arithmetic** | `+`, `-`, `*`, `/`, `%` | `result = 10 + 5`  \# 15 |
| **Comparison** | `==`, `!=`, `>`, `<`, `>=`, `<=` | `is_equal = (10 == 5)` \# False |
| **Logical** | `and`, `or`, `not` | `can_drive = (age >= 16) and (license == True)` |
| **Assignment** | `=`, `+=`, `-=` | `count += 1` \# Same as count = count + 1 |

-----

### 📚 4. Strings

**Definition:** A sequence of characters used to represent text.

**Example (Python):**

```python
message = "Hello, World!"
print(message.upper())  # Output: "HELLO, WORLD!"
print(message[0:5])     # Output: "Hello" (Slicing)
```

-----

### 🧱 5. Arrays / Lists

**Definition:** A collection of items stored in a single variable, typically ordered. (In Python, this is primarily the **List** type).

**Example (Python):**

```python
numbers = [1, 2, 3, 4, 5]
print(numbers[2])      # Output: 3 (Accessing by index)
numbers.append(6)      # Adding an item
print(numbers)         # Output: [1, 2, 3, 4, 5, 6]
```

-----

### 🔁 6. Loops

**Definition:** Used to repeat actions.

#### For Loop

Repeat for each item or range.

```python
for i in range(5):
    print(i) # Prints 0, 1, 2, 3, 4
```

#### While Loop

Repeat while a condition is true.

```python
x = 0
while x < 5:
    print(x) # Prints 0, 1, 2, 3, 4
    x += 1
```

-----

### ⚖️ 7. Conditionals

**Definition:** Execute code only if certain conditions are met, using `if`, `elif` (else if), and `else`.

**Example (Python):**

```python
age = 18
if age >= 18:
    print("Adult")
else:
    print("Minor")
```

-----

### ⚙️ 8. Functions

**Definition:** Blocks of code designed to perform a specific task; essential for code reuse.

**Example (Python):**

```python
def greet(name):
    """This function returns a greeting message."""
    return "Hello, " + name

print(greet("Mahdi")) # Output: Hello, Mahdi
```

-----

### 🧰 9. Methods

**Definition:** Functions that belong to an **object** (often accessed using dot notation).

**Example (Python):**

```python
text = "hello"
print(text.capitalize())  # Output: "Hello"
# capitalize() is a method of the string object 'text'
```

-----

### 📦 10. Classes and Objects

**Definition:** **Classes** define blueprints for **objects** (fundamental to Object-Oriented Programming - OOP).

**Example (Python):**

```python
class Dog:
    def __init__(self, name):
        # The constructor, runs when a new object is created
        self.name = name
    
    def bark(self):
        # A method of the Dog class
        print(self.name + " says woof!")

# Create an object (instance) of the Dog class
dog1 = Dog("Buddy")
dog1.bark() # Output: Buddy says woof!
```

-----

### 🧱 11. Arrays vs Lists vs Tuples

  * **List (Python):** **Mutable** (can change after creation).
  * **Tuple (Python):** **Immutable** (cannot change after creation).
  * **Array (General):** Usually fixed size/type (often used in languages like C, Java, or numpy in Python).

**Example (Python):**

```python
fruits = ["apple", "banana"]  # list (mutable)
coordinates = (10, 20)        # tuple (immutable)
```

-----

### 🗂️ 12. Dictionaries / Maps

**Definition:** A collection of **key-value pairs** for fast lookups (like a real-world dictionary).

**Example (Python):**

```python
person = {"name": "Alice", "age": 25}
print(person["name"])  # Output: "Alice"
```

-----

### 🧩 13. Input / Output (I/O)

**Definition:** How a program interacts with the outside world (e.g., getting input from a user, printing output to the console/screen).

**Example (Python):**

```python
# Output
print("Hello World")

# Input
name = input("Enter your name: ") 
print("Hello", name)
```

-----

### 🧠 14. Comments

**Definition:** Notes in code ignored by the computer, used for explanation and clarity.

**Example (Python):**

```python
# This is a comment - the interpreter ignores this line
result = 5 + 5 # This part is a comment too
```

-----

### 🧮 15. Type Casting

**Definition:** Explicitly converting one data type into another.

**Example (Python):**

```python
age_str = "25"
age_int = int(age_str) # Type casting the string "25" into the integer 25
print(type(age_int))   # Output: <class 'int'>
```

-----

### 🚫 16. Exceptions / Error Handling

**Definition:** Code mechanisms (`try...except`) to catch and handle **runtime errors** gracefully, preventing the program from crashing.

**Example (Python):**

```python
try:
    print(10 / 0)
except ZeroDivisionError:
    print("Cannot divide by zero") # Executed when the error occurs
```

-----

### 🔄 17. Recursion

**Definition:** A function calling itself. Requires a **base case** to stop the process.

**Example (Python: Factorial):**

```python
def factorial(n):
    # Base Case: Stops the recursion
    if n <= 1: 
        return 1
    # Recursive Step
    return n * factorial(n - 1) 

print(factorial(5)) # Calculates 5 * 4 * 3 * 2 * 1 = 120
```

-----

### 🧩 18. Modules / Libraries

**Definition:** Collections of pre-written code (functions, classes, variables) that you can import and reuse.

**Example (Python):**

```python
import math # Imports the math module

print(math.sqrt(16))  # Output: 4.0 (Uses the square root function from math)
```

-----

### ⚙️ 19. Scope

**Definition:** Determines where a variable can be accessed within a program.

**Example (Python):**

```python
x = 10  # global variable (accessible everywhere)

def func():
    y = 5  # local variable (only accessible inside func)
    print(x, y)

func() # Output: 10 5
# print(y) # This would cause an error because y is out of scope
```

-----

### 🧩 20. Data Structures (Overview)

**Definition:** Ways to store and organize data efficiently.

  * **List / Array:** Ordered, mutable collection.
  * **Tuple:** Ordered, **fixed** (immutable) collection.
  * **Set:** Unordered collection of **unique** elements.
  * **Dictionary / Map:** Key-value pairs for lookups.

-----

