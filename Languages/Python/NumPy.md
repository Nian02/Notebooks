# NumPy Basics
- imort
```Python
import numpy as np
```
- 使得多次生成的随机数相同
```Python
np.random.seed(seed=1234)
```
## Scalar
```Python
# 标量（scalars）
x = np.array(6) # scalar
print ("x: ", x)
print("x ndim: ", x.ndim)
print("x shape:", x.shape)
print("x size: ", x.size)
print ("x dtype: ", x.dtype)

# x:  6
# x ndim:  0
# x shape: ()
# x size:  1
# x dtype:  int64
```

## Array
```Python
# 一维数组（array）
x = np.array([1.3 , 2.2 , 1.7])
print ("x: ", x)
print("x ndim: ", x.ndim)
print("x shape:", x.shape)
print("x size: ", x.size)
print ("x dtype: ", x.dtype) # notice the float datatype

# x:  [1.3 2.2 1.7]
# x ndim:  1
# x shape: (3,)
# x size:  3
# x dtype:  float64
```

## Matrix
```Python
# 三维数组（矩阵（matrix））
x = np.array([[[1,2,3], [4,5,6], [7,8,9]]])
print ("x:\n", x)
print("x ndim: ", x.ndim)
print("x shape:", x.shape)
print("x size: ", x.size)
print ("x dtype: ", x.dtype)

# x:
#  [[[1 2 3]
#   [4 5 6]
#   [7 8 9]]]
# x ndim:  3
# x shape: (1, 3, 3)
# x size:  9
# x dtype:  int64
```

## Function
[Python Basics](Python%20Basics.md)

