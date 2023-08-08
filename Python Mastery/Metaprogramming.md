---
aliases: 
title: Metaprogramming
date created: August 8th 2023, 6:27:52 pm
date modified: August 8th 2023, 6:28:29 pm
---

#language/python 
#input 

## Decorators
Add new behaviors to an existing method

```python
@logged
def add(x, y):
	return x + y
```

### Applications
- Debugging and diagnostics
- Avoiding code replication
- Enabling/disabling optional features
