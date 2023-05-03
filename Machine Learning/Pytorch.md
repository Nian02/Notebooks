# Set up
我们将导入` PyTorch `并为可重复性设置种子。请注意，`PyTorch `还需要一个种子，因为我们将生成随机张量。
```Python
import numpy as np
import torch
SEED = 1234
# Set seed for reproducibility
np.random.seed(seed=SEED)
torch.manual_seed(SEED)
```

# Tensor Basics
我们将首先介绍 `PyTorch` 的一些基础知识，例如创建张量以及从常见数据结构（列表、数组等）转换为张量。
## Tensor
张量的概念：[Tensor](Basic%20concept/Tensor.md)
```Python
# Creating a random tensor
x = torch.randn(2, 3) # torch.randn对应于正态分布，而rand(2,3)对应于均匀分布
print(f"Type: {x.type()}")
print(f"Size: {x.shape}")
print(f"Values: \n{x}")

"""
Type: torch.FloatTensor
Size: torch.Size([2, 3])
Values:
tensor([[ 0.0461,  0.4024, -1.0115],
        [ 0.2167, -0.6123,  0.5036]])
"""
```

```Python
# 0和1张量
x = torch.zeros(2, 3)
print (x)
x = torch.ones(2, 3)
print (x)
```

## List → Tensor
```Python
# 列表（List） → 张量
x = torch.Tensor([[1, 2, 3],[4, 5, 6]])
print("Size: {}".format(x.shape)) 
print("Values: \n{}".format(x))

"""
Size: torch.Size([2, 3])
Values: 
tensor([[1., 2., 3.],
        [4., 5., 6.]])
"""
```

## NumPy array → Tensor
```Python
# NumPy 数组 → 张量
x = torch.from_numpy(np.random.rand(2, 3))
print("Size: {}".format(x.shape)) 
print("Values: \n{}".format(x))

"""
Size: torch.Size([2, 3])
Values: 
tensor([[0.0632, 0.4443, 0.9658],
        [0.4391, 0.9068, 0.6760]], dtype=torch.float64)
"""
```

```Python
# 改变张量类型（张量默认为float类型）
x = torch.Tensor(3, 4)
print("Type: {}".format(x.type()))
x = x.long()
print("Type: {}".format(x.type()))

"""
Type: torch.FloatTensor
Type: torch.LongTensor
"""
```

# Tensor Operations
## Add
```Python
# 加法
x = torch.randn(2, 3)
y = torch.randn(2, 3)
z = x + y
print("Size: {}".format(z.shape)) 
print("Values: \n{}".format(z))

"""
Size: torch.Size([2, 3])
Values: 
tensor([[ 0.7976,  0.3528,  2.3146],
        [-1.6895,  1.9033, -1.0576]])
"""
```

## Dot product
```Python
# 向量点积
x = torch.randn(2, 3)
y = torch.randn(3, 2)
z = torch.mm(x, y)
print("Size: {}".format(z.shape)) 
print("Values: \n{}".format(z))

"""
Size: torch.Size([2, 2])
Values: 
tensor([[ 0.3005, -2.8694],
        [ 1.5858, -3.1363]])
"""
```

## Transpose
```Python
# 转置
x = torch.randn(2, 3)
print("Size: {}".format(x.shape)) 
print("Values: \n{}".format(x))
y = torch.t(x)
print("Size: {}".format(y.shape)) 
print("Values: \n{}".format(y))

"""
Size: torch.Size([2, 3])
Values: 
tensor([[ 0.2164,  0.5022,  0.8684],
        [-0.7384,  0.9556,  1.0076]])
Size: torch.Size([3, 2])
Values: 
tensor([[ 0.2164, -0.7384],
        [ 0.5022,  0.9556],
        [ 0.8684,  1.0076]])
"""
```

## Reshape
```Python
# Reshape
z = x.view(3, 2)
print("Size: {}".format(z.shape)) 
print("Values: \n{}".format(z))

"""
Size: torch.Size([3, 2])
Values: 
tensor([[ 0.2164,  0.5022],
        [ 0.8684, -0.7384],
        [ 0.9556,  1.0076]])
"""
```

```Python
# reshaping的危险（意外后果）
x = torch.tensor([
    [[1,1,1,1], [2,2,2,2], [3,3,3,3]],
    [[10,10,10,10], [20,20,20,20], [30,30,30,30]]
])
print("Size: {}".format(x.shape)) 
print("Values: \n{}\n".format(x))
a = x.view(x.size(1), -1)
print("Size: {}".format(a.shape)) 
print("Values: \n{}\n".format(a))
b = x.transpose(0,1).contiguous()
print("Size: {}".format(b.shape)) 
print("Values: \n{}\n".format(b))
c = b.view(b.size(0), -1)
print("Size: {}".format(c.shape)) 
print("Values: \n{}".format(c))

"""
Size: torch.Size([2, 3, 4])
Values: 
tensor([[[ 1,  1,  1,  1],
         [ 2,  2,  2,  2],
         [ 3,  3,  3,  3]],

        [[10, 10, 10, 10],
         [20, 20, 20, 20],
         [30, 30, 30, 30]]])

Size: torch.Size([3, 8])
Values: 
tensor([[ 1,  1,  1,  1,  2,  2,  2,  2],
        [ 3,  3,  3,  3, 10, 10, 10, 10],
        [20, 20, 20, 20, 30, 30, 30, 30]])

Size: torch.Size([3, 2, 4])
Values: 
tensor([[[ 1,  1,  1,  1],
         [10, 10, 10, 10]],

        [[ 2,  2,  2,  2],
         [20, 20, 20, 20]],

        [[ 3,  3,  3,  3],
         [30, 30, 30, 30]]])

Size: torch.Size([3, 8])
Values: 
tensor([[ 1,  1,  1,  1, 10, 10, 10, 10],
        [ 2,  2,  2,  2, 20, 20, 20, 20],
        [ 3,  3,  3,  3, 30, 30, 30, 30]])
"""
```

## Dimensional operations
```Python
# 维度操作
x = torch.randn(2, 3)
print("Values: \n{}".format(x))
y = torch.sum(x, dim=0) # 为每列添加各行叠加的值
print("Values: \n{}".format(y))
z = torch.sum(x, dim=1) # 为每行添加各列叠加的值
print("Values: \n{}".format(z))

"""
Values: 
tensor([[-2.2913,  0.1027, -0.3427],
        [ 0.7189,  0.4223, -0.0364]])
Values: 
tensor([-1.5724,  0.5250, -0.3791])
Values: 
tensor([-2.5313,  1.1048])
"""
```

# Indexing
现在我们将了解如何从张量中提取、分离和连接值。
```Python
x = torch.randn(3, 4)
print("x: \n{}".format(x))
print ("x[:1]: \n{}".format(x[:1]))# 第一行，index：1
print ("x[:1, 1:3]: \n{}".format(x[:1, 1:3]))# 第一行的序号[1,3)

"""
x: 
tensor([[ 0.5660,  1.3207,  0.6355,  0.4770],
        [-1.5738, -1.6227,  0.1413, -1.8778],
        [-0.0666, -0.2320, -0.3943, -0.4921]])
x[:1]: 
tensor([[0.5660, 1.3207, 0.6355, 0.4770]])
x[:1, 1:3]: 
tensor([[1.3207, 0.6355]])
"""
```

# Slicing
```Python
# 选择维度索引
x = torch.randn(2, 3)
print("Values: \n{}".format(x))
col_indices = torch.LongTensor([0, 2])
chosen = torch.index_select(x, dim=1, index=col_indices) # 第0和第2列的值
print("Values: \n{}".format(chosen)) 
row_indices = torch.LongTensor([0, 1])
chosen = x[row_indices, col_indices] # 来自（0,0）和（1,2）的值
print("Values: \n{}".format(chosen))

"""
Values: 
tensor([[ 1.7797, -1.2580, -0.6378],
        [-1.3466, -0.2311,  0.8040]])
Values: 
tensor([[ 1.7797, -0.6378],
        [-1.3466,  0.8040]])
Values: 
tensor([1.7797, 0.8040])
"""
```

# Joining
我们还可以通过连接或堆叠操作组合张量，这也与NumPy的连接函数的行为一致。
```Python

x = torch.randn(2, 3)
print (x)
print (x.shape)

"""
tensor([[-1.5944, -0.4218, -1.8219],
        [ 1.7446,  1.2058, -0.7753]])
torch.Size([2, 3])
"""
```

## Concatenation
```Python
y = torch.cat([x, x], dim=0) # 按行堆叠（dim = 1按列堆叠）
print (y)
print (y.shape)

"""
tensor([[-1.5944, -0.4218, -1.8219],
        [ 1.7446,  1.2058, -0.7753],
        [-1.5944, -0.4218, -1.8219],
        [ 1.7446,  1.2058, -0.7753]])
torch.Size([4, 3])
"""
```
## Stacking
```Python
z = torch.stack([x, x], dim=0) # stack on new dimension
print (z)
print (z.shape)

tensor([[[-1.5944, -0.4218, -1.8219],
         [ 1.7446,  1.2058, -0.7753]],

        [[-1.5944, -0.4218, -1.8219],
         [ 1.7446,  1.2058, -0.7753]]])
torch.Size([2, 2, 3])
```
# Gradients
梯度的概念：[Gradients](Basic%20concept/Gradients.md)

$$ y = 3x + 2 $$
$$ z = \frac{\sum{y}}{N} $$
$$ \frac{\partial(z)}{\partial(x)} = \frac{\partial(z)}{\partial(y)} \frac{\partial(y)}{\partial(x)} = \frac{1}{N} * 3 = \frac{1}{12} * 3 = 0.25 $$

```Python
# 带有gradient bookkeeping的张量
x = torch.rand(3, 4, requires_grad=True)
y = 3*x + 2
z = y.mean()
z.backward() # z是标量
print("Values: \n{}".format(x))
print("x.grad: \n", x.grad)

"""
Values: 
tensor([[0.9651, 0.9546, 0.2183, 0.5656],
        [0.2327, 0.4397, 0.7654, 0.8600],
        [0.5148, 0.8122, 0.1790, 0.7555]], requires_grad=True)
x.grad: 
 tensor([[0.2500, 0.2500, 0.2500, 0.2500],
        [0.2500, 0.2500, 0.2500, 0.2500],
        [0.2500, 0.2500, 0.2500, 0.2500]])
"""
```

# CUDA
我们还将张量加载到 GPU 上，使用 CUDA（来自 Nvidia 的并行计算平台和 API）进行并行计算。
```Python
# CUDA可用吗？
print (torch.cuda.is_available())
"""
False
"""
```
如果上面的代码返回False，那么请转到菜单栏上的`Runtime`→`Change runtime type`并在`Hardware accelerator`下选择`GPU`。

Google Colab：代码执行工具→查看资源->更改运行时类型->GPU

## CPU
```Python
# 创建一个CPU版的0张量
x = torch.Tensor(3, 4).to("cpu")
print("Type: {}".format(x.type()))
"""
Type: torch.FloatTensor
"""
```

## GPU
```Python
# 创建一个CUDA版的0张量
x = torch.Tensor(3, 4).to("cuda")
print("Type: {}".format(x.type()))

"""
Type: torch.cuda.FloatTensor
"""
```