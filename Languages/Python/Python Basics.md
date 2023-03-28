# Python Objects
## List
列表（lists）是python中的对象，可以包含有序的**数字序列**和**字符串**。
- 创建列表
```Python
list_x = [3, "hello", 1]
print (list_x)

# [3, 'hello', 1]
```
- 添加元素
```Python
list_x.append(7)
print (list_x)

# [3, 'hello', 1, 7]
```
- 访问元素
```Python
print ("list_x[0]: ", list_x[0])
print ("list_x[1]: ", list_x[1])
print ("list_x[2]: ", list_x[2])
print ("list_x[-1]: ", list_x[-1]) # 索引值为-1时，表示访问最后的元素
print ("list_x[-2]: ", list_x[-2]) # 索引值为-2时，表示访问倒数第二个元素

# list_x[0]:  3
# list_x[1]:  hello
# list_x[2]:  1
# list_x[-1]:  7
# list_x[-2]:  1
```
- **切片**
```Python
print ("list_x[:]: ", list_x[:])
print ("list_x[2:]: ", list_x[2:])
print ("list_x[1:3]: ", list_x[1:3])
print ("list_x[:-1]: ", list_x[:-1])

# list_x[:]:  [3, 'hello', 1, 7]
# list_x[2:]:  [1, 7]
# list_x[1:3]:  ['hello', 1]
# list_x[:-1]:  [3, 'hello', 1]
```
- 组合列表
```Python
list_y = [2.4, "world"]
list_z = list_x + list_y
print (list_z)

# [3, 'hi', 1, 7, 2.4, 'world']
```
- 列表的长度
```Python
len(list_x)

# 4
```

## Tuple
元组（tuples）也是python中可以保存数据但是不能替换值的对象（因此，元组称为不可变，而列表称为可变）。
- 创建元组
```Python
tuple_x = (3.0, "hello")
print (tuple_x)

# (3.0, 'hello')
```
- 添加元素到元组
```Python
tuple_x = tuple_x + (5.6,)
print (tuple_x)

# (3.0, 'hello', 5.6)
```
- 试图改变元组的值（事实上不可行）
```Python
tuple_x[1] = "world"

# TypeError: 'tuple' object does not support item assignment
```

## Dict
字典（dictionaries） 是包含键值对（key-value）的python对象。在下面的字典示例中，键是“name”和“eye_color”变量。它们各自都有与之相关的值。 字典不能有两个相同的键。
- 创建字典
```Python
goku = {"name": "Goku",
        "eye_color": "brown"}
print (goku)
print (goku["name"])
print (goku["eye_color"])

# {'name': 'Goku', 'eye_color': 'brown'} 
# Goku 
# brown
```
- 改变value的值
```Python
goku["eye_color"] = "green"
print (goku)

# {'name': 'Goku', 'eye_color': 'green'}
```
- 添加新的键值对
```Python
goku["age"] = 24
print (goku)

# {'name': 'Goku', 'eye_color': 'green', 'age': 24}
```

# Conditional Statement
## If
```Python
# If 语句
x = 4
if x < 1:
    score = "low"
elif x <= 4:
    score = "medium"
else:
    score = "high"
print (score)

# 带有布尔值的if语句
x = True
if x:
    print ("it worked")
```

# Loops
## For
```Python
# 遍历0~2
x = 1
for i in range(3): # 从 i=0 到 i=2
    x += 1 # 等价于 x = x + 1
    print ("i={0}, x={1}".format(i, x)) # 打印多个变量

# 遍历列表中的元素
x = 1
for i in [0, 1, 2]:
    x += 1
    print ("i={0}, x={1}".format(i, x))

# i=0, x=2 
# i=1, x=3
# i=2, x=4
```

## While
```Python
x = 3
while x > 0:
    x -= 1 # 等价于 x = x - 1
    print (x)

# 2 1 0
```
# Function
函数（functions）是模块化可重用代码块的一种方法。
- 创建函数
```Python
def add_two(x):
    x += 2
    return x
```
- 调用函数
```Python
score = 0
score = add_two(x=score)
print (score)

# 2
```
# Class
类（classes） 是面向对象的Python编程的基础部分。
- 创建类
```Python
class Pets(object):
  
    # 类初始化
    def __init__(self, species, color, name):
        self.species = species
        self.color = color
        self.name = name

    # 用于打印
    def __str__(self):
        return "{0} {1} named {2}.".format(self.color, self.species, self.name)

    # 示例函数
    def change_name(self, new_name):
        self.name = new_name
```
- 创建类的实例：对象
```Python
my_dog = Pets(species="dog", color="orange", name="Guiness",)
print (my_dog)
print (my_dog.name)

# orange dog named Guiness. 
# Guiness
```
- 调用类的函数
```Python
my_dog.change_name(new_name="Charlie")
print (my_dog)
print (my_dog.name)

# orange dog named Charlie. 
# Charlie
```
