# NumPy Basics

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

"""
x:  6
x ndim:  0
x shape: ()
x size:  1
x dtype:  int64
"""
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

"""
x:  [1.3 2.2 1.7]
x ndim:  1
x shape: (3,)
x size:  3
x dtype:  float64
"""
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

"""
x:
 [[[1 2 3]
  [4 5 6]
  [7 8 9]]]
x ndim:  3
x shape: (1, 3, 3)
x size:  9
x dtype:  int64
"""
```

## Function
```Python
print ("np.zeros((2,2)):\n", np.zeros((2,2)))
print ("np.ones((2,2)):\n", np.ones((2,2)))
# np.eye()主对角线为1，2为输出的行数
print ("np.eye((2)):\n", np.eye((2)))
# np.random.random()返回[0.0, 1.0)
print ("np.random.random((2,2)):\n", np.random.random((2,2)))

"""
np.zeros((2,2)):
 [[0. 0.]
  [0. 0.]]
np.ones((2,2)):
 [[1. 1.]
  [1. 1.]]
np.eye((2)):
 [[1. 0.]
  [0. 1.]]
np.random.random((2,2)):
 [[0.19151945 0.62210877]
  [0.43772774 0.78535858]]
"""
```

# Index
## Indexing
```Python
# 索引（indexing）
x = np.array([1, 2, 3])
print ("x[0]: ", x[0])
x[0] = 0
print ("x: ", x)

"""
x[0]:  1
x:  [0 2 3]
"""
```

## Slicing
[切片slicings](Python%20Basics.md#^c9f3a8)

```Python
# 切片（slicing）
x = np.array([[1,2,3,4], [5,6,7,8], [9,10,11,12]])
print (x)
print ("x column 1: ", x[:, 1]) 
print ("x row 0: ", x[0, :]) 
print ("x rows 0,1,2 & cols 1,2: \n", x[:3, 1:3]) 

"""
[[ 1  2  3  4]
 [ 5  6  7  8]
 [ 9 10 11 12]]
x column 1:  [ 2  6 10]
x row 0:  [1 2 3 4]
x rows 0,1,2 & cols 1,2: 
[[ 2  3]
 [ 6  7]
 [10 11]]
"""
```

## Integer_arr Index
```Python
print (x)
# np.arange()返回一个有终点和起点的固定步长的排列。一个参数时默认为参数是终点。
rows_to_get = np.arange(len(x))
print ("rows_to_get: ", rows_to_get)
# np.array一维数组
cols_to_get = np.array([0, 2, 1])
print ("cols_to_get: ", cols_to_get)
print ("indexed values: ", x[rows_to_get, cols_to_get])

"""
[[ 1  2  3  4]
 [ 5  6  7  8]
 [ 9 10 11 12]]
rows_to_get:  [0 1 2]
cols_to_get:  [0 2 1]
indexed values:  [1 7 10](rows,cols各自取个数值)
"""
```

## Bool_arr Index
```Python
x = np.array([[1,2], [3, 4], [5, 6]])
print ("x:\n", x)
# 返回下标及其对应的布尔类型
print ("x > 2:\n", x > 2)
# 输出符合条件的下标下的内容
print ("x[x > 2]:\n", x[x > 2])

"""
x:
 [[1 2]
  [3 4]
  [5 6]]
x > 2:
 [[False False]
  [ True  True]
  [ True  True]]
x[x > 2]:
 [3 4 5 6]
"""
```
# Array Basics
## Math
### Add、Sub、Mult
```Python
# 默认数据类型为浮点(np.float64)
# 可以使用dtype关键字明确指定要使用的数据类型
x = np.array([[1,2], [3,4]], dtype=np.float64)
y = np.array([[1,2], [3,4]], dtype=np.float64)
print ("x + y:\n", np.add(x, y)) # or x + y
print ("x - y:\n", np.subtract(x, y)) # or x - y
print ("x * y:\n", np.multiply(x, y)) # or x * y

"""
x + y:
 [[2. 4.]
  [6. 8.]]
x - y:
 [[0. 0.]
  [0. 0.]]
x * y:
 [[ 1.  4.]
  [ 9. 16.]]
"""
```
### Dot Product
```Python
# 2×3 matrix
a = np.array([[1,2,3], [4,5,6]], dtype=np.float64)
# 3×2 matrix
b = np.array([[7,8], [9,10], [11, 12]], dtype=np.float64)
print (a.dot(b))

"""
[1,2,3]   [7,8]
[4,5,6] * [9,10]
          [11, 12]
1*7+2*9+3*11 = 7+18+33 = 58
[[ 58.  64.]
 [139. 154.]]
"""
```

### Sum
```Python
# 跨维度求和
x = np.array([[1,2],[3,4]])
print (x)
print ("sum all: ", np.sum(x)) # 将所有元素相加
print ("sum by col: ", np.sum(x, axis=0)) # 向下拍平，对每一列的值求和
print ("sum by row: ", np.sum(x, axis=1)) # 向右拍平，对每一行的值求和

"""
[[1 2]
 [3 4]]
sum all:  10
sum by col:  [4 6](4代表第1列的值相加)
sum by row:  [3 7](3代表第1行的值相加)
"""
```

### Transposition
```Python
print ("x:\n", x)
print ("x.T:\n", x.T)

"""
x:
 [[1 2]
  [3 4]]
x.T:
 [[1 3]
  [2 4]]
"""
```

# Array Advanced
## Dimension
### Repeating
进行重复维度的扩充
```Python
x = np.array([[1,2], [3,4]])
y = np.array([5, 6])
# np.tile()：重复维度
addent = np.tile(y, (len(x), 1))
print ("addent: \n", addent)
z = x + addent
print ("z:\n", z)

"""
addent: 
 [[5 6]
  [5 6]]
z:
 [[6 8]
  [8 10]]
"""
```

### Broadcasting
维度不匹配时，维度小的自动使用np.tile()
```Python
# 广播（broadcasting）
x = np.array([[1,2], [3,4]])
y = np.array([5, 6])
z = x + y
print ("z:\n", z)

"""
z:
 [[6  8]
  [8 10]]
"""
```

### Reshape 
```Python
x = np.array([[1,2], [3,4], [5,6]])
print (x)
print ("x.shape: ", x.shape)
y = np.reshape(x, (2, 3))
print ("y.shape: ", y.shape)
print ("y: \n", y)

"""
[[1 2]
 [3 4]
 [5 6]]
x.shape:  (3, 2)
y.shape:  (2, 3)
y: 
 [[1 2 3]
  [4 5 6]]
"""
```
