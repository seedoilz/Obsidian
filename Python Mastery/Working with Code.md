---
aliases: 
title: Working with Code
date created: August 8th 2023, 2:51:16 pm
date modified: August 8th 2023, 4:53:23 pm
---
#input
#language/python 

## Function Attributes
```python
def func(a, b):
	'This function does something.'
	...
func.threadsafe = False
func.blah = 42
```

## eval ()  and exec ()
```python
# eval()
>>> x = 10
>>> eval('3*x - 2')
28
# exec()
>>> exec('for i in range(5): print(i)')
0
1
2
3
4
```

