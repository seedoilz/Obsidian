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

## Summary
From the exercise in python-mastery, **named tuples** and **classes with slots** can save a lot of memory. Dictionaries will cost much more memory than common classes.
$$
Slots < NamedTuple < Tuple < Class < Dictionary
$$
However, named tuples and slots are able to save memory since they are determined while others can change during running. In addition, I thought that Class will cost more memory than dictionary. BUT opposite.

## Dicts
### Composite Keys
Use tuples for multi-part keys
```python
prices = {
	('ACME','2017-01-01') : 513.25,
	('ACME','2017-01-02') : 512.10,
	('ACME','2017-01-03') : 512.85,
	('SPAM','2017-01-01') : 42.1,
	('SPAM','2017-01-02') : 42.34,
	('SPAM','2017-01-03') : 42.87,
}
```

### Lookup with default value
```python
p = prices.get('AAPL', 0.0) # Lookup with default value
```

### defaultdict
If I ask for a missing dict key, it will generate a default data type for me to use.
```python
from collections import defaultdict
d = defaultdict(list)
d = defaultdict(int)...
```

### Counter
>A dictionary specialized for counting items

```python
from collections import Counter
totals = Counter()
totals['IBM'] += 20
totals['AA'] += 50
```

### ChainMap
```python
from collections import ChainMap
allprices = ChainMap(dict1, dict2)
```


## deque
>Double-ended queue

```python
from collections import deque
q = deque()
q.append(1)
q.appendleft(3)
q.pop()
q.popleft()
```

### Keeping a History
>Keep a history of the last N things
```python
history = deque(maxlen = N)
```

## Iteration
### List
#### Wildcard unpacking
```python
prices = [  
    ['GOOG', 490.1, 485.25, 487.5 ],  
    ['IBM', 91.5],  
    ['HPE', 13.75, 12.1, 13.25, 14.2, 13.5 ],  
    ['CAT', 52.5, 51.2]  
]
for name, *values in prices:
	print(name, values)
```
