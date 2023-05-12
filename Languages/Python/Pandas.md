# Set up
```Python
import numpy as np
import pandas as pd
import urllib
# 重现性
np.random.seed(seed=1234)
```
# Download Data
首先，我们要获得一些数据。我们将从下面的公共链接中下载titanic数据集。
## Downloading
```Python
# 将数据从GitHub下载到笔记本电脑的本地磁盘
url = "https://raw.githubusercontent.com/GokuMohandas/Made-With-ML/main/datasets/titanic.csv"
response = urllib.request.urlopen(url)
html = response.read()
with open('titanic.csv', 'wb') as f:
    f.write(html)
```

## Check
```Python
!ls -l

"""
total 88
drwxr-xr-x 1 root root 4096 Mar 28 13:52 sample_data -rw-r--r-- 1 root root 85153 Mar 30 06:59 titanic.csv
"""
```

# Load Data
现在我们有一些数据可以使用，让我们加载到Pandas数据帧（dataframe）中。Pandas是一个很棒的python数据库分析库。
```Python
# 从CSV读取到Pandas DataFrame
df = pd.read_csv(url, header=0)
# 前3项
df.head(3)
```
| index | pclass | name                           | sex    | age    | sibsp | parch | ticket | fare     | cabin   | embarked | survived |
|-------|--------|--------------------------------|--------|--------|-------|-------|--------|----------|---------|----------|----------|
| 0     | 1      | Allen, Miss. Elisabeth Walton  | female | 29     | 0     | 0     | 24160  | 211.3375 | B5      | S        | 1        |
| 1     | 1      | Allison, Master. Hudson Trevor | male   | 0.9167 | 1     | 2     | 113781 | 151.55   | C22 C26 | S        | 1        |
| 2     | 1      | Allison, Miss. Helen Loraine   | female | 2      | 1     | 2     | 113781 | 151.55   | C22 C26 | S        | 0        |

他们有不同的特征:
-   P舱：旅行舱位
-   姓名：乘客的全名
-   性别：性别
-   年龄：数字年龄
-   兄弟姐妹：#兄弟姐妹/配偶
-   帕奇：船上的父母/儿童人数
-   票号：票号
-   票价：机票费用
-   小屋：房间位置
-   标记：乘客登船的港口（C - 瑟堡，S - 南安普敦，Q = 皇后镇）
-   幸存：生存指标（0 - 死亡，1 - 幸存）
# Exploratory data analysis (EDA)
```Python
import matplotlib.pyplot as plt
```
我们可以用`.describe()`来提取一些关于数值特征的标准细节
```Python
# 描述性统计
df.describe()
```
|        | pclass   | age       | sibsp    | parch    | fare      | survived |
|--------|----------|-----------|----------|----------|-----------|----------|
| count  | 1309     | 1046      | 1309     | 1309     | 1308      | 1309     |
| mean   | 2.294882 | 29.881135 | 0.498854 | 0.385027 | 33.295479 | 0.381971 |
| std    | 0.837836 | 14.4135   | 1.041658 | 0.86556  | 51.758668 | 0.486055 |
| min    | 1        | 0.1667    | 0        | 0        | 0         | 0        |
| 25%    | 2        | 21        | 0        | 0        | 7.8958    | 0        |
| 50%    | 3        | 28        | 0        | 0        | 14.4542   | 0        |
| 75%    | 3        | 39        | 1        | 0        | 31.275    | 1        |
| max    | 3        | 80        | 8        | 9        | 512.3292  | 1        |

## Correlation matrix
```Python
# 相关矩阵
plt.matshow(df.corr())
continuous_features = df.describe().columns
plt.xticks(range(len(continuous_features)), continuous_features, rotation="45")
plt.yticks(range(len(continuous_features)), continuous_features, rotation="45")
plt.colorbar()
plt.show()

```
![](../../Image/Machine%20Learning/Correlation%20matrix.png)

## Histograms
我们还可以用`.hist()`来查看每个特征的值的直方图。
```Python
# age的直方图
df["age"].hist()
```
![](../../Image/Machine%20Learning/Pandas_Hist.png)
```Python
# 唯一值
df["embarked"].unique()

"""
array(['S', 'C', nan, 'Q'], dtype=object)
"""
```

# Filtering
我们可以根据特征，甚至根据特定特征中的特定值(或值范围)过滤数据。
## Selecting data
```Python
# 通过特征选取数据
df["name"].head()

"""
0                      Allen, Miss. Elisabeth Walton
1                     Allison, Master. Hudson Trevor
2                       Allison, Miss. Helen Loraine
3               Allison, Mr. Hudson Joshua Creighton
4    Allison, Mrs. Hudson J C (Bessie Waldo Daniels)
Name: name, dtype: object
"""
```

## Filtering data
```Python
# 通过特征过滤数据
df[df["sex"]=="female"].head() # only the female data appear
```
| index | pclass | name                                            | sex    | age | sibsp | parch | ticket | fare     | cabin   | embarked | survived |
|-------|--------|-------------------------------------------------|--------|-----|-------|-------|--------|----------|---------|----------|----------|
| 0     | 1      | Allen, Miss. Elisabeth Walton                   | female | 29  | 0     | 0     | 24160  | 211.3375 | B5      | S        | 1        |
| 2     | 1      | Allison, Miss. Helen Loraine                    | female | 2   | 1     | 2     | 113781 | 151.55   | C22 C26 | S        | 0        |
| 4     | 1      | Allison, Mrs. Hudson J C (Bessie Waldo Daniels) | female | 25  | 1     | 2     | 113781 | 151.55   | C22 C26 | S        | 0        |
| 6     | 1      | Andrews, Miss. Kornelia Theodosia               | female | 63  | 1     | 0     | 13502  | 77.9583  | D7      | S        | 1        |
| 8     | 1      | Appleton, Mrs. Edward Dale (Charlotte Lamson)   | female | 53  | 2     | 0     | 11769  | 51.4792  | C101    | S        | 1        |

# Sorting
我们可以对特征进行升序或降序排序
```Python
# Sorting 
df.sort_values("age", ascending=False).head()
```
| index | pclass | name                                              | sex    | age | sibsp | parch | ticket   | fare    | cabin | embarked | survived |
|-------|--------|---------------------------------------------------|--------|-----|-------|-------|----------|---------|-------|----------|----------|
| 14    | 1      | Barkworth, Mr. Algernon Henry Wilson              | male   | 80  | 0     | 0     | 27042    | 30      | A23   | S        | 1        |
| 61    | 1      | Cavendish, Mrs. Tyrell William (Julia Florence... | female | 76  | 1     | 0     | 19877    | 78.85   | C46   | S        | 1        |
| 1235  | 3      | Svensson, Mr. Johan                               | male   | 74  | 0     | 0     | 347060   | 7.775   | NaN   | S        | 0        |
| 135   | 1      | Goldschmidt, Mr. George B                         | male   | 71  | 0     | 0     | PC 17754 | 34.6542 | A5    | C        | 0        |
| 9     | 1      | Artagaveytia, Mr. Ramon                           | male   | 71  | 0     | 0     | PC 17609 | 49.5042 | NaN   | C        | 0        |

# Grouping
我们可以通过特定的组别来获得统计数字。我们想要看到获救的人和未获救的人之间的特征平均值。
```Python
# Grouping（数据聚合与分组运算）
survived_group = df.groupby("survived")
survived_group.mean()
```
| survived | pclass   | age       | sibsp    | parch    | fare      |
|----------|----------|-----------|----------|----------|-----------|
| 0        | 2.500618 | 30.545369 | 0.521632 | 0.328801 | 23.353831 |
| 1        | 1.962    | 28.918228 | 0.462    | 0.476    | 49.361184 |

# Indexing
`iloc`在索引中的特定位置获取行（或列）
```Python
# Selecting row 0
df.iloc[0, :]
"""
pclass                                  1
name        Allen, Miss. Elisabeth Walton
sex                                female
age                                    29
sibsp                                   0
parch                                   0
ticket                              24160
fare                              211.338
cabin                                  B5
embarked                                S
survived                                1
Name: 0, dtype: object
"""
```

```Python
# Selecting a specific value 
df.iloc[0, 1]
"""
'Allen, Miss. Elisabeth Walton'
"""
```

# Preprocessing
数据预处理
## At least one NaN value
```Python
# 具有至少一个NaN值的行
df[pd.isnull(df).any(axis=1)].head()# any按照列遍历，返回的index是行的
```
| index | pclass | name                         | sex    | age | sibsp | parch | ticket   | fare    | cabin | embarked | survived |
|-------|--------|------------------------------|--------|-----|-------|-------|----------|---------|-------|----------|----------|
| 9     | 1      | Artagaveytia, Mr. Ramon      | male   | 71  | 0     | 0     | PC 17609 | 49.5042 | NaN   | C        | 0        |
| 13    | 1      | Barber, Miss. Ellen "Nellie" | female | 26  | 0     | 0     | 19877    | 78.85   | NaN   | S        | 1        |
| 15    | 1      | Baumann, Mr. John D          | male   | NaN | 0     | 0     | PC 17318 | 25.925  | NaN   | S        | 0        |
| 23    | 1      | Bidois, Miss. Rosalie        | female | 42  | 0     | 0     | PC 17757 | 227.525 | NaN   | C        | 1        |
| 25    | 1      | Birnbaum, Mr. Jakob          | male   | 25  | 0     | 0     | 13905    | 26      | NaN   | C        | 0        |

```
pandas.isnull(obj)
Parameters:
	obj: 标量或类似数组，要检查空值或缺失值的对象
return: bool
	对于标量输入，返回标量布尔值。对于数组输入，返回一个布尔数组，指示每个布尔值是否缺少相应的元素。
```

```
pandas.DataFrame.any(*, axis=0, bool_only=None, skipna=True, **kwargs)
Parameters:
	axis: 指示应减小哪个轴，0-index，1-columns
	A  B  C 若axis=0，对行遍历，返回的index为列的index，A True，B True C False
	1  0  0
    2  2  0
	bool_only: bool值，1时仅包含布尔列。
	skipna: bool值，排除 NA/空值。
	**kwargs: any，接收其他关键字
return: Series or DataFrame
	指定了级别->DataFrame.
	返回True->有可能位于轴上的元素
	
```

## Drop rows
```Python
# 删除具有Nan值的行
df = df.dropna() # 删除具有NaN值的行
df = df.reset_index() # 重置行索引
df.head()
```

```Python
# 删除多行
df = df.drop(["name", "cabin", "ticket"], axis=1) # 删除标签所在列
df.head()
```

## Map feature values
```Python
# 映射特征值
df['sex'] = df['sex'].map( {'female': 0, 'male': 1} ).astype(int)
df["embarked"] = df['embarked'].dropna().map( {'S':0, 'C':1, 'Q':2} ).astype(int)
df.head()
```

# Feature engineering
## Create new features
我们将使用特征工程增加一个称为`family_size`的列，首先定义一个`get_family_size`函数，返回父母和子女的人数之和。
```Python
def get_family_size(sibsp, parch):
    family_size = sibsp + parch
    return family_size
```
然后可以使用`lambda`来在每一行上使用这一个函数
```Python
# lambda表达式创建新特征
df["family_size"] = df[["sibsp", "parch"]].apply(lambda x: get_family_size(x["sibsp"], x["parch"]), axis=1)# 增加一列
df.head()
```

```
DataFrame.apply(func, axis=0, raw=False, result_type=None, args=(), **kwargs)
Parameters:
	func: 应用于每行或列的函数
	axis: 0-index，对行便利，对每一列应用函数; 1-column对列遍历，对每一行应用函数
	raw: bool, False: 将每一行或每一列作为Series传递给func; True: 传递的函数将接收ndarray对象
	result_type: {‘expand’, ‘reduce’, ‘broadcast’, None}仅在axis=1起作用
	args=(): 除了array/series之外，要传递给func的位置参数。
Returns: 
	Series or DataFrame，沿着axis应用func的结果
```

## Reorganize headers
```Python
# 重新组织标题
df = df[['pclass', 'sex', 'age', 'sibsp', 'parch', 'family_size', 'fare', 'embarked', 'survived']]
df.head()
```

# Save data
```Python
# 保存数据帧（dataframe）到 CSV
df.to_csv("processed_titanic.csv", index=False)
```

```Python
# 看你一下你保持的文件
!ls -l

"""
total 96
-rw-r--r-- 1 root root  6975 Dec  3 17:36 processed_titanic.csv
drwxr-xr-x 1 root root  4096 Nov 21 16:30 sample_data
-rw-r--r-- 1 root root 85153 Dec  3 17:36 titanic.csv
"""
```

# Scaling
当处理非常大的数据集时，我们的 Pandas 数据帧可能会变得非常大，并且对它们进行操作可能非常慢或不可能。这是可以分发工作负载或在更高效的硬件上运行的软件包可以派上用场的地方。
-  [Dask](https://dask.org/)：并行计算，可在一台/多台机器上扩展Numpy，Pandas和scikit-learn等软件包。
-  [cuDF](https://github.com/rapidsai/cudf)：在 GPU 上高效的数据帧加载和计算。
当然，我们可以将它们组合在一起（[Dask-cuDF](https://github.com/rapidsai/cudf/tree/main/python/dask_cudf)）以在GPU上的数据帧分区上运行。