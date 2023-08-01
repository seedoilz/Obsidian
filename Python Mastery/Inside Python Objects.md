#input 
#language/python 

## Dicts, Classes, Instances
>Each instance gets its own private dictionary to store its member variables
>Each class gets its own dictionary to store its methods

### Instances and Classes
Instances and classes are linked together by the **__class__** attribute.

The instance dictionary holds data unique to each instance whereas the class dictionary holds data collectively shared by all instances
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20230801115530.png)

#### Attribute Access
Using the (.) operator helps to access data and methods, which is actually tied to the dictionaries.
When we read or write the attributes of an instance, we actually make actions to the dictionaries underlying.

- First check in local \_\_dict\_\_
- If not found, look in \_\_dict\_\_ of class
- If not found in class, look in base classes

## Inheritance
### How Inheritance Works
Bases classes are stored as a tuple in each class which provides a link to parent classes

![[#Attribute Access]]

### Single Inheritance

In inheritance hierarchies, attributes are found by walking up the inheritance tree.
With single inheritance, there is a single path to the top.
You stop with the first match.

#### The MRO
>Method Resolution Order

The inheritance chain is precomputed and stored in an "MRO" attribute on the class

![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20230801124328.png)


### Multiple Inheritance
>It is like Single Inheritance. But it needs rules to determine the path.
#### Rules
Rule 1: Children before parents
Rule 2: Parents go in order

### Why super()?
> Always use super () when overriding methods
> super () delegates to the next class on the MRO

### Designing for Inheritance
#### Rule 1
>Compatible Method Arguments

Overridden methods must have a compatible signature across the entire hierarchy

#### Rule 2
>Method chains must terminate

There must be a implemented class to terminate the search chain

