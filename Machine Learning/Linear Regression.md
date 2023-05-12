使用 NumPy 从头开始实现线性回归，然后使用 PyTorch再实现一次。
# Overview
$$\hat{y} = XW + b$$

- **目标**：过线性模型的输入 $X$ 去预测 $\hat{y}$。模型将会寻找一条最优的线使得我们的预测值和目标值最为接近。训练数据($X$,$y$)用来训练这个模型并且通过**随机梯度下降**(SGD)学习权重 $W$。 
- **优点:**
    -   计算简单。
    -   解释性强。
    -   可用于连续（continuous）和无序的类别（categorical）特征。
-   **缺点:**
    -   线性模型只能用于线性可分的数据(针对于分类任务).
    -   但是通常来讲不会用于分类任务，仅仅用于回归问题。
-   **其他:** 当然你也可以使用线性回归去做二分类任务，如果预测出的连续数值高于一个阈值它就属于一个特定的分类。但是我们在未来的课程中将会介绍可用于做二分类任务更好的模型，所以我们本次课程只会集中在怎么用线性回归去做回归任务。

# Progress
1.  随机初始化模型的权重$W$。
2.  **前向传播**：将输入值 $X$ 传入模型并且得到预测值$\hat{y}$。
3.  通过**损失函数**来计算预测值$\hat{y}$和真实值$\hat{y}$之间的差距，从而得到损失值$J(\theta)$。普遍在线性回归中用到的损失函数是**均方误差(MSE)**。这个函数计算出预测值和真实值之间的差距的平方($\frac12$没有数学意义，只是在求导的时候可以正好和平方抵消，方便计算)。
$$J(\theta) = \frac{1}{N} \sum_i (y_i - \hat{y}_i)^2 = \frac{1}{N}\sum_i (y_i - X_iW)^2$$
排除偏差项 （$b$）以避免拥挤符号
4.  计算损失$J(θ)$的**梯度**
$$\frac{\partial{J}}{\partial{W}} = -\frac{2}{N} \sum_i (y_i - X_iW) X_i = -\frac{2}{N} \sum_i (y_i - \hat{y}_i) X_i$$
$$\frac{\partial{J}}{\partial{b}} = -\frac{2}{N} \sum_i (y_i - X_iW)1 = -\frac{2}{N} \sum_i (y_i - \hat{y}_i)1$$
5. 使用较小的学习率$\alpha$更新权重$W$
$$W = W - \alpha\frac{\partial{J}}{\partial{W}}$$
$$b = b - \alpha\frac{\partial{J}}{\partial{b}}$$
6. 重复步骤 2 - 5 以最大程度地减少损失并训练模型。
# Generate data
我们将生成一些简单的虚拟数据来应用线性回归。它将创建大致线性的数据（）;添加随机噪声以创建无法完全对齐在一条线上的真实数据。我们的目标是让模型收敛到一个类似的线性方程（由于我们添加了一些噪声，所以会有轻微的方差）。`y = 3.5X + noise`
```Python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

SEED = 1234
NUM_SAMPLES = 50

# Set seed for reproducibility
np.random.seed(SEED)

# Generate synthetic data
def generate_data(num_samples):
    """Generate dummy data for linear regression."""
    X = np.array(range(num_samples))# 50个间隔均匀的值[0,50)
    # random.uniform(low=0.0, high=1.0, size=None)从参数化的均匀分布中抽取样本。
    # low：输出间隔的下边界；high：输出间隔的上限。
    random_noise = np.random.uniform(-10, 20, size=num_samples)
    y = 3.5*X + random_noise # add some noise
    return X, y
```

产生随机的线性数据
```Python
# Generate random (linear) data
X, y = generate_data(num_samples=NUM_SAMPLES)
data = np.vstack([X, y]).T# 按顺序垂直堆叠数组（按行）.T表示转置
# 转置后shape：[50,2];转置前shape：[2,50](2个50个数据的一维数组叠加)
print (data[:5])

"""
[[ 0.         -4.25441649]
 [ 1.         12.16326313]
 [ 2.         10.13183217]
 [ 3.         24.06075751]
 [ 4.         27.39927424]]
"""
```

导入pandas中的数据帧
```Python
# Load into a Pandas DataFrame
df = pd.DataFrame(data, columns=["X", "y"])
X = df[["X"]].values
y = df[["y"]].values
df.head()
```
| index | X | y         |
|-------|---|-----------|
| 0     | 0 | -4.254416 |
| 1     | 1 | 12.163263 |
| 2     | 2 | 10.131832 |
| 3     | 3 | 24.060758 |
| 4     | 4 | 27.399274 |

散点图
```Python
# Scatter plot
plt.title("Generated data")
plt.scatter(x=df["X"], y=df["y"])
plt.show()
```

![](../Image/Machine%20Learning/Linear%20regression%20generated%20data.png)

# Numpy
现在我们已经准备好了数据，我们将首先使用 NumPy 实现线性回归。这将让我们真正了解底层操作。
## Split data
由于我们的任务是回归任务，我们将数据集随机分为三组：训练、验证和测试数据拆分。

-   `train`：用于训练我们的模型。
-   `val`：用于在训练期间验证模型的性能。
-   `test`：用于评估我们完全训练的模型。
```Python
TRAIN_SIZE = 0.7
VAL_SIZE = 0.15
TEST_SIZE = 0.15

# 打乱数据
indices = list(range(NUM_SAMPLES))
np.random.shuffle(indices)
X = X[indices]
y = y[indices]
```

```Python
# Split indices
train_start = 0
train_end = int(0.7*NUM_SAMPLES)
val_start = train_end
val_end = int((TRAIN_SIZE+VAL_SIZE)*NUM_SAMPLES)
test_start = val_end

# Split data
X_train = X[train_start:train_end]
y_train = y[train_start:train_end]
X_val = X[val_start:val_end]
y_val = y[val_start:val_end]
X_test = X[test_start:]
y_test = y[test_start:]
print (f"X_train: {X_train.shape}, y_train: {y_train.shape}")
print (f"X_val: {X_val.shape}, y_test: {y_val.shape}")
print (f"X_test: {X_test.shape}, y_test: {y_test.shape}")

"""
X_train: (35, 1), y_train: (35, 1)
X_val: (7, 1), y_test: (7, 1)
X_test: (8, 1), y_test: (8, 1)
"""
```

## Standardize data
我们需要标准化数据（零均值和单位方差），以便特定特征的量级不会影响模型学习其权重的方式。
$$z = \frac{x_i - \mu}{\sigma}$$
```Python
def standardize_data(data, mean, std):
    return (data - mean)/std
# Determine means and stds
X_mean = np.mean(X_train)
X_std = np.std(X_train)
y_mean = np.mean(y_train)
y_std = np.std(y_train)
```
我们需要将验证集和测试集视为隐藏数据集。因此，我们只使用训练集来确定均值和标准，以避免偏差我们的训练过程。
```Python
# Standardize
X_train = standardize_data(X_train, X_mean, X_std)
y_train = standardize_data(y_train, y_mean, y_std)
X_val = standardize_data(X_val, X_mean, X_std)
y_val = standardize_data(y_val, y_mean, y_std)
X_test = standardize_data(X_test, X_mean, X_std)
y_test = standardize_data(y_test, y_mean, y_std)

# Check (means should be ~0 and std should be ~1)
# Check (means should be ~0 and std should be ~1)
print (f"mean: {np.mean(X_test, axis=0)[0]:.1f}, std: {np.std(X_test, axis=0)[0]:.1f}")
print (f"mean: {np.mean(y_test, axis=0)[0]:.1f}, std: {np.std(y_test, axis=0)[0]:.1f}")

"""
mean: -0.4, std: 0.9
mean: -0.3, std: 1.0
"""
```

## Weights
`Step 1`：随机初始化模型的权重$W$
```Python
INPUT_DIM = X_train.shape[1] # X is 1-dimensional
OUTPUT_DIM = y_train.shape[1] # y is 1-dimensional

# Initialize random weights
W = 0.01 * np.random.randn(INPUT_DIM, OUTPUT_DIM)
b = np.zeros((1, 1))
print (f"W: {W.shape}")# 
print (f"b: {b.shape}")

"""
W: (1, 1)
b: (1, 1)
"""
```

## Model
`Step 2`： 输入$W$进入模型以接收预测$\hat{y}$
```Python
# Forward pass [NX1] · [1X1] = [NX1]
y_pred = np.dot(X_train, W) + b# 35个训练数据每个乘以W，再加上b
print (f"y_pred: {y_pred.shape}")

"""
y_pred: (35, 1)
"""
```

## Loss
`Step 3`：比较预测和$\hat{y}$与实际目标值$y$和使用目标（成本）函数确定损失$J$.线性回归的常见目标函数是均方误差 （MSE）。此函数计算预测值和目标值之间的差异并将其平方。
$$J(\theta) = \frac{1}{N} \sum_i (y_i - \hat{y}_i)^2 = \frac{1}{N}\sum_i (y_i - X_iW)^2$$

排除偏差项 （$b$）以避免拥挤符号
```Python
# Loss
N = len(y_train)
loss = (1/N) * np.sum((y_train - y_pred)**2)
print (f"loss: {loss:.2f}")

"""
loss: 0.99
"""
```

## Gradients
`Step 4`：计算损失梯度$J(θ)$
$$\frac{\partial{J}}{\partial{W}} = -\frac{2}{N} \sum_i (y_i - X_iW) X_i = -\frac{2}{N} \sum_i (y_i - \hat{y}_i) X_i$$
$$\frac{\partial{J}}{\partial{b}} = -\frac{2}{N} \sum_i (y_i - X_iW)1 = -\frac{2}{N} \sum_i (y_i - \hat{y}_i)1$$

```Python
# Backpropagation
dW = -(2/N) * np.sum((y_train - y_pred) * X_train)
db = -(2/N) * np.sum((y_train - y_pred) * 1)
```
梯度是函数的导数或变化率。 它是一个向量，指向函数最大增加的方向。 例如，我们损失函数$J(\theta)$的**梯度**（关于权重$W$的$J(\theta)$的偏导) 会告诉我们如何改变$W$使得我们可以最小化$J(\theta)$

## Update weights
`Step 5`：更新权重$W$使用较小的学习率$\alpha$
$$W = W - \alpha\frac{\partial{J}}{\partial{W}}$$
$$b = b - \alpha\frac{\partial{J}}{\partial{b}}$$
```Python
LEARNING_RATE = 1e-1
# Update weights
W += -LEARNING_RATE * dW
b += -LEARNING_RATE * db
```
学习率$\alpha$是一种控制我们更新权重多少的方法。如果我们选择较小的学习率，我们的模型可能需要很长时间才能训练。但是，如果我们选择较大的学习率，我们可能会超调，我们的训练永远不会收敛。具体的学习率取决于我们的数据和我们使用的模型类型，但通常在$[1e^{-8}, 1e^{-1}]$.

## Training
`Step 6`：重复步骤 2 - 5 以最大程度地减少损失并训练模型。
```Python
NUM_EPOCHS = 100
# 初始化随机权重
W = 0.01 * np.random.randn(INPUT_DIM, OUTPUT_DIM)
b = np.zeros((1, ))

# 循环训练
for epoch_num in range(NUM_EPOCHS):

    # 前向传播 [NX1] · [1X1] = [NX1]
    y_pred = np.dot(X_train, W) + b

    # Loss
    loss = (1/len(y_train)) * np.sum((y_train - y_pred)**2)

    # 每10轮输出一次loss
    if epoch_num%10 == 0:
        print (f"Epoch: {epoch_num}, loss: {loss:.3f}")

    # 反向传播
    dW = -(2/N) * np.sum((y_train - y_pred) * X_train)
    db = -(2/N) * np.sum((y_train - y_pred) * 1)

    # 更新参数(批量梯度下降，根据整个训练集更新参数)
    W += -LEARNING_RATE * dW
    b += -LEARNING_RATE * db

```

## Evaluation
现在，我们已准备好查看经过训练的模型在测试（保留）数据拆分方面的表现如何。这将是我们衡量模型在现实世界中表现的最佳指标，因为我们的数据集的分布接近于看不见的数据。
```Python
# Predictions
pred_train = W*X_train + b
pred_test = W*X_test + b
# Train and test MSE
train_mse = np.mean((y_train - pred_train) ** 2)
test_mse = np.mean((y_test - pred_test) ** 2)
print (f"train_MSE: {train_mse:.2f}, test_MSE: {test_mse:.2f}")

"""
train_MSE: 0.03, test_MSE: 0.01
"""
```

绘制图像
```Python
# Figure size
plt.figure(figsize=(15,5))

# Plot train data
plt.subplot(1, 2, 1)
plt.title("Train")
plt.scatter(X_train, y_train, label="y_train")
plt.plot(X_train, pred_train, color="red", linewidth=1, linestyle="-", label="model")
plt.legend(loc="lower right")

# Plot test data
plt.subplot(1, 2, 2)
plt.title("Test")
plt.scatter(X_test, y_test, label='y_test')
plt.plot(X_test, pred_test, color="red", linewidth=1, linestyle="-", label="model")
plt.legend(loc="lower right")

# Show plots
plt.show()
```
![](../Image/Machine%20Learning/Linear%20regression%20train%20and%20test%20Numpy.png)

## Interpretability
由于我们对输入和输出进行了标准化，因此我们的权重适合这些标准化值。因此，我们需要取消我们的权重标准化，以便将其与我们的真实权重（3.5）进行比较。
- 请注意，$W$和$y$被标准化。我们要找到$W_{unscaled}$和$b_{unscaled}$
$$\hat{y}_{scaled} = b_{scaled} + \sum_{j=1}^{k}{W_{scaled}}_j{x_{scaled}}_j$$
- $\hat{y}_{scaled} = \frac{\hat{y} - \bar{y}}{\sigma_y}$
- $x_{scaled} = \frac{x_j - \bar{x}_j}{\sigma_j}$

$$\hat{y}_{scaled} = \frac{\hat{y}_{unscaled} - \bar{y}}{\sigma_y} = {b_{scaled}} + \sum_{j=1}^{k} {W_{scaled}}_j (\frac{x_j - \bar{x}_j}{\sigma_j})$$
$$\hat{y}_{unscaled} = b_{scaled}\sigma_y + \bar{y} - \sum_{j=1}^{k} {W_{scaled}}_j(\frac{\sigma_y}{\sigma_j})\bar{x}_j + \sum_{j=1}^{k}{W_{scaled}}_j(\frac{\sigma_y}{\sigma_j})x_j$$
在上面的表达式中，我们可以看到表达式：
$$\hat{y}_{unscaled} = b_{unscaled} + W_{unscaled}x$$
- $W_{unscaled}={W}_j(\frac{\sigma_y}{\sigma_j})$
- $b_{unscaled} = b_{scaled}\sigma_y + \bar{y} - \sum_{j=1}^{k} {W}_j(\frac{\sigma_y}{\sigma_j})\bar{x}_j$
```Python
# Unscaled weights
W_unscaled = W * (y_std/X_std)
b_unscaled = b * y_std + y_mean - np.sum(W_unscaled*X_mean)
print ("[actual] y = 3.5X + noise")
print (f"[model] y_hat = {W_unscaled[0][0]:.1f}X + {b_unscaled[0]:.1f}")

"""
[actual] y = 3.5X + noise
[model] y_hat = 3.4X + 7.8
"""
```

# PyTorch
```Python
import torch
# Set seed for reproducibility
torch.manual_seed(SEED)
```

## Split data
这一次，我们不是使用索引拆分数据，而是使用`scikit-learn`内置的`train_test_split`函数。
```Python
from sklearn.model_selection import train_test_split
TRAIN_SIZE = 0.7
VAL_SIZE = 0.15
TEST_SIZE = 0.15
# Split (train)
X_train, X_, y_train, y_ = train_test_split(X, y, train_size=TRAIN_SIZE)
print (f"train: {len(X_train)} ({(len(X_train) / len(X)):.2f})\n"
       f"remaining: {len(X_)} ({(len(X_) / len(X)):.2f})")

"""
train: 35 (0.70)
remaining: 15 (0.30)
"""
# Split (val、test)
X_val, X_test, y_val, y_test = train_test_split(
    X_, y_, train_size=0.5)
print(f"train: {len(X_train)} ({len(X_train)/len(X):.2f})\n"
      f"val: {len(X_val)} ({len(X_val)/len(X):.2f})\n"
      f"test: {len(X_test)} ({len(X_test)/len(X):.2f})")
"""

train: 35 (0.70)
val: 7 (0.14)
test: 8 (0.16)
"""
```

## Standardize data
这次我们将使用`scikit-learn`的`StandardScaler`来标准化我们的数据。

以`y_train`为例，使用`fit`方法对`y_train`进行拟合操作，得到`y_train`的均值和方差等统计信息，并将这些信息存储在`y_scaler`对象中。接着，我们可以使用`y_scaler`对象的`transform`方法对`y_train`进行标准化处理，得到标准化后的`y_train`。

`ravel()`方法将`y_train`从二维数组转换为一维数组，然后`reshape(-1, 1)`将其转换为列向量的形式（-1表示行的数量未知）。这是因为`y_scaler.transform()`方法要求输入的数据是一个二维数组，其中行数表示样本数量，列数表示特征数量。
```Python
from sklearn.preprocessing import StandardScaler
# 训练数据进行标准化~N(0,1)
X_scaler = StandardScaler().fit(X_train)
y_scaler = StandardScaler().fit(y_train)

# 评估、测试数据进行标准化~N(0,1)
X_train = X_scaler.transform(X_train)
y_train = y_scaler.transform(y_train).ravel().reshape(-1, 1)
X_val = X_scaler.transform(X_val)
y_val = y_scaler.transform(y_val).ravel().reshape(-1, 1)
X_test = X_scaler.transform(X_test)
y_test = y_scaler.transform(y_test).ravel().reshape(-1, 1)

# Check (means should be ~0 and std should be ~1)
print (f"mean: {np.mean(X_test, axis=0)[0]:.1f}, std: {np.std(X_test, axis=0)[0]:.1f}")
print (f"mean: {np.mean(y_test, axis=0)[0]:.1f}, std: {np.std(y_test, axis=0)[0]:.1f}")

"""
mean: -0.3, std: 0.7
mean: -0.3, std: 0.6
"""
```

## Weights
我们将在MLP实现中使用PyTorch的线性层。这些层将充当出权重（和偏差）。
$$z = XW$$
```Python
# X_train: (35, 1), y_train: (35, 1)
INPUT_DIM = X_train.shape[1] # X is 1-dimensional
OUTPUT_DIM = y_train.shape[1] # y is 1-dimensional
```

举例子`nn.Linear()`的使用
1. `[3, 1]`大小的`x`矩阵
```Python
from torch import nn
# Inputs
N = 3 # num samples
x = torch.randn(N, INPUT_DIM)
print (x.shape)
print (x.numpy())

"""
torch.Size([3, 1])
[[ 0.04613046]
 [ 0.40240282]
 [-1.0115291 ]]
"""
```

2. `nn.Linear`
```Python
# Weights
m = nn.Linear(INPUT_DIM, OUTPUT_DIM)
print (m)
print (f"weights ({m.weight.shape}): {m.weight[0][0]}")
print (f"bias ({m.bias.shape}): {m.bias[0]}")

"""
Linear(in_features=1, out_features=1, bias=True)
weights (torch.Size([1, 1])): 0.35
bias (torch.Size([1])): -0.34
"""
```

```
torch.nn.Linear(in_features, out_features, bias=True, device=None, dtype=None)
Parameters:
	in_features: 输入数据的维度
	out_features: 输出数据的维度
	bias: True-学习bias，False-不学习bias
Shape:
	Input: (∗,in_features)
	Output: (∗,out_features)
Variables:
	weight: (out_features,in_features)
	bias: out_features
```

3. 将`x`传入`nn.Linear()`
```Python
# Forward pass
z = m(x)
print (z.shape)
print (z.detach().numpy())

"""
torch.Size([3, 1])
[[-0.32104054]
 [-0.19719592]
 [-0.68869597]]
"""
```

## Model
$$\hat{y} = XW + b$$
```Python
class LinearRegression(nn.Module):
    def __init__(self, input_dim, output_dim):
        super(LinearRegression, self).__init__()
        self.fc1 = nn.Linear(input_dim, output_dim)

    def forward(self, x_in):
        y_pred = self.fc1(x_in)
        return y_pred

# Initialize model
model = LinearRegression(input_dim=INPUT_DIM, output_dim=OUTPUT_DIM)
print (model.named_parameters)

"""
Model:
<bound method Module.named_parameters of LinearRegression(
  (fc1): Linear(in_features=1, out_features=1, bias=True)
)>
"""
```

## Loss
这次我们使用 PyTorch 的损失函数-`MSELoss`
```Python
loss_fn = nn.MSELoss()
y_pred = torch.Tensor([0., 0., 1., 1.])
y_true =  torch.Tensor([1., 1., 1., 0.])
loss = loss_fn(y_pred, y_true)
print("Loss: ", loss.numpy())# 3/4

"""
Loss:  0.75
"""
```

## Optimizer
当我们仅使用 `NumPy` 实现线性回归时，我们使用`批量梯度下降`来更新我们的权重（使用整个训练集）。但实际上有许多不同的梯度下降优化算法可供选择，这取决于情况。但是，在大多数情况下，`ADAM 优化器`已成为标准算法。
```Python
from torch.optim import Adam
# Optimizer
optimizer = Adam(model.parameters(), lr=LEARNING_RATE)
```

## Train
```Python
# Convert data to tensors
X_train = torch.Tensor(X_train)
y_train = torch.Tensor(y_train)
X_val = torch.Tensor(X_val)
y_val = torch.Tensor(y_val)
X_test = torch.Tensor(X_test)
y_test = torch.Tensor(y_test)

# 训练过程
for epoch in range(NUM_EPOCHS):
    # 前向传播
    y_pred = model(X_train)

    # Loss
    loss = loss_fn(y_pred, y_train)

    # Zero all gradients
    optimizer.zero_grad()

    # 反向传播更新参数
    loss.backward()

    # 使用adam优化器更新参数
    optimizer.step()

    if epoch%20==0:
        print (f"Epoch: {epoch} | loss: {loss:.2f}")

"""
Epoch: 0 | loss: 0.22
Epoch: 20 | loss: 0.03
Epoch: 40 | loss: 0.02
Epoch: 60 | loss: 0.02
Epoch: 80 | loss: 0.02
"""
```

## Evaluation
现在，我们已准备好评估经过训练的模型。
```Python
# Predictions
pred_train = model(X_train)
pred_test = model(X_test)

# Performance
train_error = loss_fn(pred_train, y_train)
test_error = loss_fn(pred_test, y_test)
print(f"train_error: {train_error:.2f}")
print(f"test_error: {test_error:.2f}")

"""
train_error: 0.02
test_error: 0.01
"""
```

由于我们只有一个特征，因此很容易目视检查模型。
```Python
# Figure size
plt.figure(figsize=(15,5))# 宽度为15英寸，高度为5英寸的图像。

# Plot train data
plt.subplot(1, 2, 1)# 创建一个1行2列的子图
plt.title("Train")
plt.scatter(X_train, y_train, label="y_train")
plt.plot(X_train, pred_train.detach().numpy(), color="red", linewidth=1, linestyle="-", label="model")
plt.legend(loc="lower right")

# Plot test data
plt.subplot(1, 2, 2)
plt.title("Test")
plt.scatter(X_test, y_test, label='y_test')
plt.plot(X_test, pred_test.detach().numpy(), color="red", linewidth=1, linestyle="-", label="model")
plt.legend(loc="lower right")

# Show plots
plt.show()
```
![](../Image/Machine%20Learning/Linear%20regression%20train%20and%20test%20Pytorch.png)
`.detach().numpy()`将`PyTorch张量pred_train`从计算图（[Computational Graph](Basic%20concept/Computational%20Graph.md)）中分离出来，并将其转换为一个`NumPy数组`
1.  `detach()`函数：将一个张量从计算图中分离出来，使其不再参与后续计算，返回一个新的张量。这个函数通常用于在反向传播(backpropagation)过程中，需要保留某些中间变量的梯度信息，但又不希望这些变量对计算图造成影响。
2.  `numpy()`函数：将一个PyTorch张量转换为NumPy数组。
当我们对一个PyTorch张量进行计算时，这个张量所依赖的计算图会被记录下来，并用于计算梯度信息。如果我们直接对这个张量调用`numpy()`函数将其转换为`NumPy数组`，那么这个张量的计算图信息会被丢失，从而导致后续的反向传播过程中无法计算这个张量的梯度信息。
## Inference
训练模型后，我们可以使用它来**预测新数据**。
```Python
# 使用自己想要的序号进行作为X_train
sample_indices = [10, 15, 25]
X_infer = np.array(sample_indices, dtype=np.float32)
X_infer = torch.Tensor(X_scaler.transform(X_infer.reshape(-1, 1)))
```

回想一下，我们需要对预测进行标准化。[Standardize data](Linear%20Regression.md#Standardize%20data)
$$\hat{y}_{scaled} = \frac{\hat{y} - \mu_{\hat{y}}}{\sigma_{\hat{y}}}$$
$$\hat{y} = \hat{y}_{scaled} * \sigma_{\hat{y}} + \mu_{\hat{y}}$$

```Python
# Unstandardize predictions

# y_scaler = StandardScaler().fit(y_train)
# 使用fit方法对y_train进行拟合操作，得到y_train的均值和方差等统计信息，并将这些信息存储在y_scaler对象中。
# model()返回y_pred
# df = pd.DataFrame(data, columns=["X", "y"])
pred_infer = model(X_infer).detach().numpy() * np.sqrt(y_scaler.var_) + y_scaler.mean_
# i表示每个元素在列表中的下标，从0开始递增；index则表示当前迭代的元素本身，pred_infer->[3,1]
for i, index in enumerate(sample_indices):
    print (f"{df.iloc[index]["y"]:.2f} (actual) → {pred_infer[i][0]:.2f} (predicted)")

"""
35.73 (actual) → 42.11 (predicted)
59.34 (actual) → 59.17 (predicted)
97.04 (actual) → 93.30 (predicted)
"""
```

线性回归具有高度可解释性的巨大优势。每个特征都有一个系数，表示其对输出变量 y 的重要性/影响。我们可以将我们的系数解释如下：通过将 X 增加 1 个单位，我们将 y 增加$W$（~3.65） 单位。
- $W_{unscaled}={W}_j(\frac{\sigma_y}{\sigma_j})$
- $b_{unscaled} = b_{scaled}\sigma_y + \bar{y} - \sum_{j=1}^{k} {W}_j(\frac{\sigma_y}{\sigma_j})\bar{x}_j$

```Python
# Unstandardize coefficients
W = model.fc1.weight.data.numpy()[0][0]
b = model.fc1.bias.data.numpy()[0]
W_unscaled = W * (y_scaler.scale_/X_scaler.scale_)
b_unscaled = b * y_scaler.scale_ + y_scaler.mean_ - np.sum(W_unscaled*X_scaler.mean_)
print ("[actual] y = 3.5X + noise")
print (f"[model] y_hat = {W_unscaled[0]:.1f}X + {b_unscaled[0]:.1f}")

```