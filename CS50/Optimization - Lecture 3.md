#input 

## state-space landscape
> find the max or min value based on the objective function or the cost function

### Hill Climbing
```
function Hill-Climb(problem):
	current = initial state of problem
	repeat:
		neighbor = highest valued neighbor of current
		if neighbor not better than current:
			return current
		current = neighbor
```

Sometimes, we may get stuck in local maxima. How to solve?
![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20230621221029.png)
