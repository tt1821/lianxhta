
> **作者：** 连玉君 (中山大学)    
> **邮箱：** <arlionn@163.com> 

&emsp;


- **Title**: Python 解惑：什么是面向对象的编程？
- **Keywords**: OOP, pandas, 面向对象, class, 类, 对象, 封装, 继承, 多态, Python基础, Python语法, Python入门

&emsp; 

## 0. 引言

在 AI 辅助编程的今天，虽然 Python 编程的入门门槛大幅降低，但很多初学者在借助 AI 编写代码时，往往忽视了语言的底层逻辑和语法规则。长此以往，这会导致编写代码的能力无法提升，调试和修改代码的效率低下，甚至会形成对 AI 的过度依赖，无法真正掌握编程技能。

对于 Python 初学者而言，常常会看到「**Python** 是一门**面向对象**的**编程语言**」这样的介绍。但“**面向对象**”究竟意味着什么，尤其是其中的“**对象**”概念，许多人却往往感到困惑。对于像 Stata 用户这样的转型者而言，当他们初次见到如下 Python 代码时，往往会陷入困惑：

```python
import statsmodels.api as sm
import pandas as pd

# 加载示例数据
mtcars_df = sm.datasets.get_rdataset('mtcars', 'datasets').data

# 线性回归
X = mtcars_df[['wt']]
X = sm.add_constant(X)
y = mtcars_df['mpg']
model = sm.OLS(y, X).fit()

model.summary()
```

在 `mtcars_df = sm.datasets.get_rdataset('mtcars', 'datasets').data` 这条语句中，呈现出 `sm.A.B().C` 的语法结构。初学者难免产生如下疑问：

- 这里面哪些是函数 (function)？哪些是方法 (method)？哪些又是对象的属性呢？
- 要搞清楚这些语句的编写规则，需要学习哪些概念呢？

类似让人费解的语句还有 `model = sm.OLS(y, X).fit()` 。

本文的目的就是解答这些疑问，深入理解 Python 的面向对象编程 (OOP) 概念。通过对类 (class)、对象 (object)、封装 (encapsulation)、继承 (inheritance) 和多态 (polymorphism) 等核心概念的讲解，你将能够更好地理解 Python 代码的逻辑和结构关系，从而提升编写 Python 代码的能力。

&emsp;

## 1. 面向对象编程 (OOP) 的核心概念

面向对象编程 (OOP) 是一种编程范式，它将现实世界中的事物抽象为「对象」，通过「类」来定义对象的属性和行为。

OOP 的核心概念包括类 (Class)、对象 (Object)、封装 (Encapsulation)、继承 (Inheritance) 和多态 (Polymorphism)。下面我们将逐一介绍这些概念。

### 1.1 类 (Class) 

类是对象的蓝图或模板，它定义了对象的属性和方法，可将其视为一种自定义的数据类型。以 `Person` 类为例：

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def introduce(self):
        print(f"我叫 {self.name}，今年 {self.age} 岁。")
```

在这个例子中，`Person` 是一个类，它利用两个函数定义了一个人的基本属性和行为。其中，

- `__init__()` 是一个特殊的函数，也被称为构造函数，用于初始化对象的属性。本例中，`__init__()` 定义了 `Person` 类的两个属性，即 `name` 和 `age`。
- `introduce()` 函数则用于定义 `Person` 类**方法**。本例中，其功能是按照特定格式打印出对象的 `name` 和 `age` 信息。

这里要着重说明一下 `self` 这个参数。在 Python 的类中，`self` 是一个约定俗成的参数名，它代表类的实例对象本身。当你调用类的方法时，Python 会自动将调用该方法的对象作为第一个参数传递给 `self`。借助 `self`，方法能够访问和修改对象的属性。例如，在 `__init__()` 方法里，`self.name = name` 这一操作把传入的 `name` 参数值赋给了对象的 `name` 属性；在 `introduce()` 方法中，`self.name` 和 `self.age` 用于获取对象的 `name` 和 `age` 属性值。

### 1.2 对象 (Object) 

对象是类的实例。通过类能够创建多个不同的对象，每个对象都有自己独立的属性值。例如：

```python
p1 = Person("张三", 20)
p2 = Person("李四", 25)

print(p1.name)  # 输出: 张三
print(p2.name)  # 输出: 李四

p1.introduce()  # 输出: 我叫 张三，今年 20 岁。
p2.introduce()  # 输出: 我叫 李四，今年 25 岁。
```

本例中，`p1` 和 `p2` 是 `Person` 类的两个不同对象。`p1.name` 的值是 "张三"，`p2.name` 的值是 "李四"，即不同对象的属性值可以不同。

### 1.3 封装 (Encapsulation) 

封装是面向对象编程的一个重要特性，它指的是将数据 (**属性**) 和操作数据的**方法**捆绑在一起，并且对外部隐藏对象的内部实现细节。

封装的作用主要体现在以下几个方面：

- **数据保护**：防止外部代码直接访问和修改对象的属性，从而避免数据被意外修改或破坏。
- **简化接口**：只向外部暴露必要的方法，隐藏内部实现细节，使代码更易于维护和扩展。

在 Python 中，可以通过**访问控制**来实现封装。虽然 Python 没有像其他语言那样严格的访问修饰符 (如 `private`、`protected`)，但可以通过约定来表示属性或方法的访问级别：

- 以单下划线 `_` 开头的属性或方法被视为受保护的
- 以双下划线 `__` 开头的属性或方法被视为**私有的**

例如：

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance

    def deposit(self, amount):
        self.__balance += amount

    def withdraw(self, amount):
        if amount <= self.__balance:
            self.__balance -= amount
        else:
            print("余额不足")

    def get_balance(self):
        return self.__balance

account = BankAccount(1000)
account.deposit(500)
account.withdraw(200)
print(account.get_balance())  # 输出: 1300
```

本例中，`__balance` 是一个私有属性，外部不能直接访问，只能通过 `deposit`、`withdraw` 和 `get_balance` 等方法来操作。这样就保证了账户余额的安全性，外部代码无法随意修改余额。

### 1.4 继承 (Inheritance) 

继承是指一个类可以继承另一个类的属性和方法。被继承的类称为父类 (基类)，继承的类称为子类 (派生类)。子类可以扩展父类的功能，也可以重写父类的方法。这样可以实现代码的复用和扩展。

例如：

```python
class Student(Person):
    def __init__(self, name, age, student_id):
        super().__init__(name, age)
        self.student_id = student_id

    def study(self):
        print(f"{self.name} 正在学习。")

s = Student("王五", 18, "001")
s.introduce()  # 输出: 我叫 王五，今年 18 岁。
s.study()  # 输出: 王五 正在学习。
```

本例中，

- `Student` 类继承了 `Person` 类的属性和方法，并添加了自己的属性 `student_id` 和方法 `study`。
- `super().__init__(name, age)` 则调用了父类的构造函数，对 `name` 和 `age` 属性进行初始化。

### 1.5 多态 (Polymorphism)

多态是指不同的对象可以对同一个操作做出不同的响应。下面通过几个例子来帮助你理解多态的概念和应用。

#### 1.5.1 简单数据类型示例

```python
a = 5
b = "apple"
print(a * 3)  # 输出: 15
print(b * 3)  # 输出: appleappleapple
```

本例中，`*` 运算符对于整数 `a` 和字符串 `b` 有不同的行为。对于整数，`*` 表示乘法运算；对于字符串，`*` 表示重复字符串。由此可见，所谓「多态」，指的是同一个运算符在不同的数据类型上有不同的操作，它可以「看碟下菜」。

#### 1.5.2 类的方法重写示例

为了更深入地理解多态，我们可以通过类的继承和方法重写来实现。下面是一个关于形状 (Shape) 的例子，展示了如何使用多态来计算不同形状的面积。

```python
class Shape:
    def area(self):
        pass

class Rectangle(Shape):
    def __init__(self, length, width):
        self.length = length
        self.width = width

    def area(self):
        return self.length * self.width

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius * self.radius

shapes = [Rectangle(4, 5), Circle(3)]
for shape in shapes:
    print(shape.area())
```

本例中，`Rectangle` 和 `Circle` 类都继承自 `Shape` 类，并且重写了 `area` 方法。当我们遍历 `shapes` 列表并调用每个对象的 `area` 方法时，不同的对象会根据自己的实现方式计算并返回面积：

- 对于 `Rectangle` 对象，调用 `area()` 方法会返回长方形的面积，即 $area = a \times b$ ($a$ 和 $b$ 分别表示长方形的长和宽)。
- 对于 `Circle` 对象，调用 `area()` 方法会返回圆的面积，即 $area = \pi r^2$ ($r$ 表示圆的半径)。

#### 1.5.3 Python 支持多态的原因

Python 能够支持多态，主要得益于其动态类型的特性。在 Python 中，变量在声明时不需要预先指定数据类型，变量的类型是在运行时动态确定的。这意味着,一个变量可以在不同的时刻引用不同类型的对象。

当调用一个函数或方法时，Python 不会关心对象的具体类型，只要对象实现了所需的方法或属性，就可以正常调用。这种特性使得 Python 代码更加灵活，能够轻松实现多态。例如，在上面的 `shapes` 列表中，虽然 `Rectangle` 和 `Circle` 对象的类型不同，但都实现了 `area` 方法，因此可以统一调用 `area` 方法来计算面积。

&emsp;

## 2. OOP 在具体包和函数中的应用

在实际的 Python 编程中，面向对象编程的概念广泛应用于各种包和函数中。下面以 `pandas` 和 `matplotlib` 这两个常用的包为例进行说明。

### 2.1 在 pandas 中的应用

`pandas` 是一个用于数据处理和分析的强大包，其中大量使用了类和对象的概念。例如，`DataFrame` 是 `pandas` 中最常用的一个类，用于表示二维表格数据。

```python
import pandas as pd

# 创建一个 DataFrame 对象
data = {
    'Name': ['张三', '李四', '王五'],
    'Age': [20, 25, 18]
}
df = pd.DataFrame(data)

# 使用 DataFrame 的方法
df.head()      # 查看数据集行数
df.describe()  # 查看数据的统计信息
```

本例中，`pd.DataFrame` 是一个类，`df` 是 `DataFrame` 类的一个具体对象。我们可以调用 `df` 对象的各种方法来对数据进行操作和分析，如 `head()` 和 `describe()`，写作 `df.head()` 和 `df.describe()`。

### 2.2 在 matplotlib 中的应用

`matplotlib` 是一个用于数据可视化的包，也广泛应用了面向对象编程的思想。例如，我们可以使用 `Figure` 和 `Axes` 类来创建和定制图形。

```python
import matplotlib.pyplot as plt

# 创建一个 Figure 对象和一个 Axes 对象
fig, ax = plt.subplots()

# 绘制数据
x = [1, 2, 3, 4, 5]
y = [2, 4, 6, 8, 10]
ax.plot(x, y)

# 设置图形属性
ax.set_title('简单折线图')
ax.set_xlabel('X 轴')
ax.set_ylabel('Y 轴')

# 显示图形
plt.show()
```

本例中，`fig` 是 `Figure` 类的对象，`ax` 是 `Axes` 类的对象。我们可以通过调用 `ax` 对象的方法，如 `plot()`、`set_title()`、`set_xlabel()` 和 `set_ylabel()` 来绘制和定制图形。

&emsp;

## 3. 理解复杂语句所需的基础概念

### 3.1 包 (Package) 和模块 (Module) 

在 Python 中，包和模块是组织代码的基本结构。模块是一个包含 Python 代码的 `.py` 文件，而包则是一个包含多个模块的**目录**，用于组织和管理相关模块。每个包必须包含一个特殊的 `__init__.py` 文件，来声明该目录为一个包。

举个例子，`statsmodels` 就是一个包，它提供了许多用于统计建模的功能。当我们执行 `import statsmodels.api as sm` 时，实际上是从 `statsmodels` 包中导入了 `api` 模块，并将其重命名为 `sm` 以便后续使用。

再看一例。`pandas` 是一个包，其目录结构如下：
    
```python
pandas/               # 根包
    __init__.py         # 包的初始化文件
    core/               # 子包
        __init__.py
        frame.py          # 模块（定义 DataFrame 类）
        series.py         # 模块（定义 Series 类）
    io/                 # 子包
        __init__.py
        excel.py          # 模块（处理 Excel 文件）
```

### 3.2 函数 (Function) 和方法 (Method) 

函数是一段完成特定任务的独立代码块，可以直接调用。而方法是与对象关联的函数，必须通过对象来调用。例如，

- 在 `sm.datasets.get_rdataset` 中，`get_rdataset` 是一个函数，它属于 `statsmodels` 包中 `datasets` 模块。
- 在 `model = sm.OLS(y, X).fit()` 中，`fit()` 是 `sm.OLS(y, X)` 创建的对象的方法。

### 3.3 类中的属性 (Attribute) 和方法 (Method) 

属性是类或对象的**数据成员**，而方法是类或对象的**函数成员**。

在 `mtcars_df = sm.datasets.get_rdataset('mtcars', 'datasets').data` 中，`.data` 就是 `get_rdataset` 函数返回对象的一个属性，用于获取数据。

&emsp;

## 4. 解析复杂语句

现在我们来详细解析引言中提到的复杂语句：

```python
import statsmodels.api as sm
mtcars_df = sm.datasets.get_rdataset('mtcars', 'datasets').data
```

### 4.1 import 语句解析

先看 `import` 语句：

```python
import statsmodels.api as sm
```

这条语句的作用是导入 `statsmodels` 包下的 `api` 模块，并将其重命名 (简写) 为 `sm`，方便后续调用。

- 这里的 `.api` 是 `statsmodels` 包为用户提供的统一接口模块，整合了常用的统计建模功能。详情参见 [statsmodels 官方文档](https://www.statsmodels.org/stable/api.html)。
- 通过 `sm`，可以直接访问 `statsmodels` 中的主要类和函数，无需分别导入多个子模块。

有关 `import` 语句的更多信息，可以参考 [Python 官方文档：import 语句](https://docs.python.org/zh-cn/3.13/reference/simple_stmts.html#the-import-statement)，以及 [Python：import 使用详解](https://book.lianxh.cn/ds/body/01_py_01_basic_04_import.html)。

### 4.2 数据集加载解析

再看数据集加载的语句：

```python
mtcars_df = sm.datasets.get_rdataset('mtcars', 'datasets').data

mtcars.head()
```

这行代码很长，为了便于理解，我们把这行代码拆解为以下几步，以便更好的理解 **模块**、**函数** 和 **属性** 这几个概念的区别：

```python
# 第一步：获取 datasets 子模块
datasets_module = sm.datasets

# 第二步：调用 get_rdataset 函数获取数据集对象
mtcars_object = datasets_module.get_rdataset('mtcars', 'datasets')
  # 获取数据集对象：'object = 模块.函数()' 格式
  #        本例中：'object = 模块.子模块.函数(参数1, 参数2)' 格式
  # 函数和参数说明：
    # 'get_rdataset' 是 datasets 子模块中的一个函数，用于获取 R 数据集。
    # 'mtcars' 是数据集的名称
    # 'datasets' 是数据集所在的包名

# 第三步：通过 data 属性获取实际的数据 (Data Frame)
mtcars_df = mtcars_object.data

# 第四步：查看数据集的前几行
mtcars_df.head()  
```

在上述拆解过程中，我们引入了两个中间变量: `datasets_module` 和 `rdataset`，使得每一步的操作更加清晰易懂。然而，在实际编程中，我们通常会将这些步骤合并为一行代码，以提高代码的简洁性和可读性：采用 `A.B().C` 的形式来反映模块、函数和属性之间的关系和工作流程。

下面是对代码的补充说明：

- `sm` 是 `statsmodels.api` 模块的别名。
- `datasets` 是 `sm` 下的一个子模块，专门用于访问内置或外部数据集。
- `get_rdataset('mtcars', 'datasets')` 是 `datasets` 子模块中的一个函数，用于从 R 的数据集仓库中的 **datasets** 包中获取名为 `'mtcars'` 的数据集。这个函数会返回一个包含数据集及其元信息 (meta information) 的**对象**，即 **mtcars_object**。
- `.data` 是对象 **mtcars_object** 的一个属性，用于提取实际的数据内容 (通常为一个数据框，即 `pandas.DataFrame`)。
  - 因此，最终得到的 `mtcars_df` 变量就是一个 `pandas.DataFrame` 对象，包含了 `'mtcars'` 数据集的实际数据。
- `mtcars_df.head()` 是数据框 `mtcars_df` 的方法，用于查看数据集的前几行数据。

需要注意的是，在上述语句中，`datasets` 这个关键词出现了两次，两者含义不同，不要混淆：
- 第一次是指 `sm` 下的 `datasets` 子模块；
- 第二次是作为参数传递给 `get_rdataset()` 函数，它是 R 语言中的一个扩展包的名称，而 `mtcars` 则是 **datasets** 包中的一个数据集。

### 4.3 模型拟合解析

接下来，我们来解析模型拟合的语句：

```python
X = mtcars_df[['wt']]
X = sm.add_constant(X)
y = mtcars_df['mpg']
model = sm.OLS(y, X).fit()

model.summary()
```

这段代码的作用是使用 `statsmodels` 包中的普通最小二乘回归 (OLS) 方法来拟合一个线性回归模型。我们同样可以将其拆解为几个步骤，以便更好地理解：

```python
# 第一步：选择自变量和因变量
X = mtcars_df[['wt']]  # 选择自变量 'wt' (重量)
y = mtcars_df['mpg']   # 选择因变量 'mpg' (每加仑英里数)

# 第二步：添加常数项
X = sm.add_constant(X)  # 添加常数项 (截距)

# 第三步：创建 OLS 模型对象
model = sm.OLS(y, X)  # 创建 OLS 模型对象，传入因变量 y 和自变量 X

# 第四步：拟合模型
model = model.fit()  # 拟合模型，计算模型参数

# 第五步：查看模型摘要
model.summary()  # 输出模型的统计摘要
``` 

在上述代码中，我们可以看到以下几个重要的概念：

- `X` 和 `y` 分别是自变量和因变量的数据框 (DataFrame)，它们从 `mtcars_df` 中提取。
- `sm.add_constant(X)` 是 `statsmodels` 包中的一个函数，用于在自变量 `X` 中添加一个常数项 (截距)，使得线性回归模型能够包含截距项。
- `sm.OLS(y, X)` 是 `statsmodels` 包中的一个类，用于创建一个普通最小二乘回归模型的对象。这里传入的参数 `y` 是因变量，`X` 是自变量。
- `.fit()` 是 `sm.OLS(y, X)` 创建的对象的方法，用于拟合模型，即根据输入的数据计算模型的参数。
- `model.summary()` 是 `model` 对象的方法，用于输出模型的统计摘要，包括系数、标准误差、t 值、p 值等信息。

最终结果为：

```python
                         OLS Regression Results                   
=================================================================
Dep. Variable:                 mpg   R-squared:             0.753
Model:                         OLS   Adj. R-squared:        0.745
Method:              Least Squares   F-statistic:           91.38
Date:             Sun, 29 Jun 2025   Prob (F-statistic): 1.29e-10
Time:                     11:08:52   Log-Likelihood:      -80.015
No. Observations:               32   AIC:                   164.0
Df Residuals:                   30   BIC:                   167.0
Df Model:                        1                               
Covariance Type:         nonrobust                               
=================================================================
              coef  std err        t    P>|t|    [0.025    0.975]
-----------------------------------------------------------------
const      37.2851    1.878   19.858    0.000    33.450    41.120
wt         -5.3445    0.559   -9.559    0.000    -6.486    -4.203
=================================================================
Omnibus:             2.988   Durbin-Watson:                 1.252
Prob(Omnibus):       0.225   Jarque-Bera (JB):              2.399
Skew:                0.668   Prob(JB):                      0.301
Kurtosis:            2.877   Cond. No.                       12.7
=================================================================

Notes:
[1] Standard Errors assume that the covariance matrix of 
    the errors is correctly specified.
```

&emsp;

## 5. 总结

本文通过对 Python 面向对象编程 (OOP) 的核心概念进行详细讲解，帮助初学者理解了类、对象、封装、继承和多态等重要概念。同时，通过对 `statsmodels` 包中复杂语句的解析，深入探讨了包、模块、函数、方法和属性之间的关系。

你可能会感觉本文写得很啰嗦，但如果能耐心阅读并理解这些内容，将会对你在 Python 编程中遇到的各种复杂语句有更深刻的理解。特别是对于那些从其他编程语言转型过来的用户，掌握这些概念将有助于你更快地适应 Python 的编程风格和习惯。



&emsp;

## 6. 参考资料
- [Python 100 Days - 18.面向对象编程入门](https://github.com/jackfrued/Python-100-Days/blob/master/Day01-20/18.%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%BC%96%E7%A8%8B%E5%85%A5%E9%97%A8.md)
- [Python：import 使用详解](https://book.lianxh.cn/ds/body/01_py_01_basic_04_import.html)
- Python 从入门到深入, [13. 面向对象编程](https://pythonhowto.readthedocs.io/zh-cn/latest/object.html)
- [一文看懂Python面向对象编程](https://pythondjango.cn/python/advanced/1-python-object-class-programming/)


&emsp;

## 7. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh python- 学习建议 常用包盘点 用户最爱 书籍推荐, nocat md`  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 

  - 李俊奇, 2025, [Python-EconML包：快速上手动态双重机器学习](https://www.lianxh.cn/details/1577.html), 连享会 No.1577.
  - 王卓, 2023, [知乎热议：经济学入门必读书籍推荐](https://www.lianxh.cn/details/1242.html), 连享会 No.1242.
  - 连享会, 2021, [司继春：Python学习建议和资源](https://www.lianxh.cn/details/563.html), 连享会 No.563.
  - 连小白, 2025, [Python常用包盘点：经济与金融领域的必备工具包](https://www.lianxh.cn/details/1568.html), 连享会 No.1568.
  - 连玉君, 2020, [Stata程序：是否有类似-Python-中的-zip()-函数](https://www.lianxh.cn/details/347.html), 连享会 No.347.
  - 连玉君, 2024, [从基础到 AI 助手：Python 用户最爱的 VScode 插件清单](https://www.lianxh.cn/details/1489.html), 连享会 No.1489.
  - 郭李鹏, 2020, [Stata交互：Python-与-Stata-对比](https://www.lianxh.cn/details/303.html), 连享会 No.303.
  - 高瑜, 2025, [书籍推荐：用Python实现可复现的数据科学](https://www.lianxh.cn/details/1551.html), 连享会 No.1551.
