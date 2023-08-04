---
aliases: 
title: Functions
date created: August 4th 2023, 10:14:42 am
date modified: August 4th 2023, 10:14:44 am
---
#language/python 
#input 

## Overview
### Default Arguments
>Default arguments must appear last in definition

#### Keyword Arguments
With default arguments in function definition, keywords should be pointed out for passing optional arguments.

In addition, people can force the use of keyword arguments.
```python
def read_data(filename, *, debug=False):
	...
```

#### Default Values

**Don't use mutable values as defaults**
The default value is only created once for the whole program--mutations are "sticky"

```python
def func(a, items=[]):
	items.append(a)
	return items
>>> func(1)
[1]
>>> func(2)
[1, 2]
>>> func(3)
[1, 2, 3]
```

**Advice**: Only use immutable values such as None, True, False, numbers, or strings