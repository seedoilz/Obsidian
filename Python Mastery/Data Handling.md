#input 

## Tuple
- can be used as an array
```python
s = ('Goog', 100, 490.1)
name = s[0]
cost = s[1] * s[2]
```
- unpack into separate variables
```python
name, shares, price = s
```
- **Immutable (can not be modified)**

## Slots
### Effect
- Saves memory
```python
class Stock:
	__slots__ = ('name', 'shares', 'price')
	def __init__(self, name, shares, price):
		self.name = name
		self.shares = shares
		self.price = price
```
don't need extra space to store the additional attribute
However, the son class will not inherit the slots from the father class

## Dataclasses
### Effect
This can reduce the amount of code that must be written

```python
from dataclasses import dataclass

@dataclass
class Stock:
	name : str
	shares : int
	price: float
```

### Points
Types are NOT enforced (the type of the variables in the class can be changed)

## Named Tuples
### Effect
- Immutability/tuple behavior
- another variant on class definition

```python
from collections import namedtuple # Define a named tuple type called "Point" with fields "x" and "y" Point = namedtuple('Point', 'x y')
```