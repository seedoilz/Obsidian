#language/python 
#input 

## Generators

### Reusable Generators
```python
class Countdown:
    def __init__(self, n):
        self.n = n
        
    def __iter__(self):
        n = self.n
        while n > 0:
            yield n
            n -= 1
            
countdown_instance = Countdown(5)
# 第一次迭代
for value in countdown_instance:
    print(value)
# 创建一个新的迭代器并进行第二次迭代
for value in countdown_instance:
    print(value)
```

## Generator Pipelines

![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20230814155509.png)

