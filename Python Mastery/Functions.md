---
aliases: 
title: Functions
date created: August 4th 2023, 10:14:42 am
date modified: August 4th 2023, 4:55:53 pm
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

## Concurrency
### Concurrency
>Functions might execute concurrently (threads)

![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20230804124216.png)

### Futures

![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20230804125331.png)


## Lambda Functions
> One-expression functions can use lambda

### Usage
#### Alter function args
```python
def distance(x, y):
	return abs(x - y)
>>> dist_from10 = lambda y: distance(10, y)
```

```python
def map(func, values):  
    result = []  
    for x in values:  
       result.append(func(x))  
    return result  
def reduce(func, values, initial=0):  
    result = initial  
    for n in values:  
       result = func(n, result)  
    return result  
def sum(x, y):  
    return x + y  
def square(x):  
    return x * x

nums = [1, 2, 3, 4]  
result = reduce(sum, map(square, nums))
```

## Closures
Essential feature : A "closure" retains the values of all variables needed for the function to run properly later on