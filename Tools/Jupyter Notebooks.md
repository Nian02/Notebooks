常见使用方法：

# Google Colab
## Colab 是什么
Colab 是谷歌内部类 Jupyter Notebook 的交互式 Python 环境，免安装快速切换 Python 2和 Python 3 的环境，支持 Google 全家桶（TensorFlow、BigQuery、GoogleDrive 等），支持 pip

网址：[Google Colab](https://colab.research.google.com)

## Colab 中安装和使用各种深度学习库
Colab 自带了 Tensorflow、Matplotlib、Numpy、Pandas 等深度学习基础库。如果还需要其他依赖，如 Keras，可以新建代码块，输入
```text
# 安装最新版本Keras
# https://keras.io/
!pip install keras
# 指定版本安装
!pip install keras==2.0.9
# 安装 OpenCV
# https://opencv.org/
!apt-get -qq install -y libsm6 libxext6 && pip install -q -U opencv-python
# 安装 Pytorch
# http://pytorch.org/
!pip install -q http://download.pytorch.org/whl/cu75/torch-0.2.0.post3-cp27-cp27mu-manylinux1_x86_64.whl torchvision
# 安装 XGBoost
# https://github.com/dmlc/xgboost
!pip install -q xgboost
# 安装 7Zip
!apt-get -qq install -y libarchive-dev && pip install -q -U libarchive
# 安装 GraphViz 和 PyDot
!apt-get -qq install -y graphviz && pip install -q pydot
```

## 第一个深度学习程序
### 检查版本
```python
# 2023/03/08
import tensorflow as tf
from tensorflow import keras
from keras.models import Sequential
print (tf.__version__)

# output is 2.11.0
import sklearn
import numpy as np
print (sklearn.__version__)
print (np.__version__)

#output is:
# 1.2.2 
# 1.22.4
```

### mnist数据集测试
```python
"""### 使用mnist数据集做测试"""

(train_images, train_labels), (test_images, test_labels) =  keras.datasets.mnist.load_data()
print (train_images.shape, test_images.shape)
print (train_labels.max(), test_labels.min())

# 打乱数据集, 因为keras validation_split的时候并不会随机抽取数据
#np.random.seed(1)
train_indexes = np.arange(0, train_images.shape[0])
np.random.shuffle(train_indexes)
train_images = train_images[train_indexes]
train_labels = train_labels[train_indexes]

# 设置网络结构, 设置损失函数,优化器,评估指标等等
def create_model():
  model = tf.keras.models.Sequential([
    keras.layers.Flatten(input_shape=(28,28)),
    keras.layers.Dense(128, activation=tf.nn.relu),
    keras.layers.Dense(10, activation=tf.nn.softmax)
    ])

  model.compile(
    optimizer = tf.keras.optimizers.Adam(),
    loss = tf.keras.losses.sparse_categorical_crossentropy,
    metrics=['accuracy'])
  return model

# 创建模型
model = create_model()
print (model.summary())

# 训练模型
history = model.fit(train_images, train_labels, epochs=100, batch_size=512, validation_split = 0.1) 

# 在测试集上评估模型
print (model.evaluate(test_images, test_labels))

# 保存模型,注意这里的模型保存在的是colab环境中,需要下载
model.save('mnist_model.h5')
```

### 模型保存
```python
from google.colab import files

with open('example.txt', 'w') as f:
  f.write('some content')
files.download('example.txt')
```

## Google Drive
我们还可以在colab中挂载google drive的目录,这样在colab中就可以直接读写google drive的目录,模型可以直接保存在dirve上非常方便。

需要进行登录。
```python
"""### 挂载到google drive上""" 
from google.colab import drive

# 使用工具colab的接口挂载google drive目录，这样可以从外部获取数据并且可以把训练好的模型保存在google drive上
drive.mount('/content/gdrive')
model.save('/content/gdrive/My Drive/colab_dir/mnist_model.h5')
```