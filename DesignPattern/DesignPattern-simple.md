---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 设计模式

设计模式被分为3类，创建型模式、结构型模式、行为型模式。

## 创建型模式

创建型模式就是创建对象的模式，抽象了实例化的过程。它帮助一个系统独立于如何创建、组合和表示它的那些对象。关注的是对象的创建。

社会化的分工越来越细，自然在软件设计方面也是如此。因此，对象的创建和对象的使用分开也就成为了必然趋势。因为对象的创建会消耗掉系统的很多资源，所以单独对对象的创建进行研究，从而能够高效地创建对象就是创建型模式要探讨的问题。

### 简单工厂模式(Simple Factory)

当需要针对上下文的具体情况创建指定类型的实例时，可以将创建实例的过程放到某个统一的代码处（可以是类的方法或者函数），对应的上下文的地方只需要调用该方法/函数就可以获取到它们希望的实例，工厂模式的优点：

- 统一管理，便于修改
- 调用代码简洁

再次强调：工厂模式生产出来的可以是函数、实例甚至是类。生产这些的可以是类方法、实例方法、函数。

例子：
```python {class="line-numbers"}
# 操作符工厂
import operator
import re
        
def getOptFunc(opt_name):
    opt_dict = {
        '+' : operator.add,
        '-' : operator.sub,
        '*' : operator.mul,
        '/' : operator.truediv,
        '^' : lambda a,b:a**b,
    }
    return opt_dict[opt_name]
    
    
# 应用例子 : 计算字符表达式
def calc_str_expression(str_express):
    # 创建符号的正则表达式
    opt_rank = {')':0, '+':1, '-':1, '*':2, '/':2, '^':3, '(':10}
    opt_reg = r'([\+|\-|\*|/|\^|\(|\)])'
    num_reg = r'(\d+\.?\d*)'
    item_reg = re.compile(opt_reg+'|'+num_reg)
    opt_stack = []
    num_stack = []
    str_express = f'({str_express})'
    for i in item_reg.finditer(str_express):
        i = i.groups()
        if i[1]:
            # 匹配到操作符处理
            num_stack.append(float(i[1]))
        else:
            while (len(opt_stack) and opt_stack[-1]!='('
                   and opt_rank[i[0]]<=opt_rank[opt_stack[-1]]):
                b = num_stack.pop()
                a = num_stack.pop()
                # 使用工厂模式获取操作函数
                opt_fun = getOptFunc(opt_stack.pop())
                num_stack.append(opt_fun(a, b))
            opt_stack.append(i[0])
            if opt_stack[-1]==')':
                del opt_stack[-2:]

    return num_stack[-1]
calc_str_expression("11+(7+ 1.5*4^2-5 )/2")
```
上面代码涉及到的知识点：
- 正则表达式的使用
- 字符串表达式的计算算法(主要是栈)，算法主要是逆波兰式
- 工厂模式创建操作符函数

### 工厂方法模式(Factory Method)

### 抽象工厂模式(Abstract Factory)

### 创建者模式(Builder)

### 原型模式(Prototype)

### 单例模式(Singleton)

## 结构型模式

结构型模式是为了解决怎样组装现有的类，设计它们的交互方式，从而达到一定的功能目的。结构型模式包容了对很多问题的解决，如扩展性（外观、组成、代理、装饰）、封装（适配器、桥接）。

在解决了对象的创建问题后，对象的组成以及对象之间的依赖关系就成了开发人员关注的焦点，因为任何设计对象的结构、继承和依赖关系会影响到后续程序的维护性、代码的健壮性、耦合性等。对象结构的设计很容易体现出设计人员的水平。

### 外观模式/门面模式(Facade)

### 适配器模式(Adapter)

### 代理模式(Proxy)

### 装饰模式(Decorator)

### 桥接模式(Bridge)

### 组合模式(Composite)

### 享元模式(Flyweight)

## 行为型模式

行为型模式涉及到算法和对象之间的职责分配，行为模式描述了对象和类的模式，以及它们之间的通信模式，行为模式刻画了在程序运行时难以跟踪的复杂的控制流，可以分为行为类模式和行为对象模式。
1. 行为类模式：使用继承机制在类之间分派任务；
1. 行为对象模式：使用对象聚合来分配行为；

一些行为对象模式描述了一组对等的对象如何相互协作以完成其中任意一个对象都无法完成的任务。

### 模板方法模式(Template Method)

### 观察者模式(Observer)

### 状态模式(State)

### 策略模式(Strategy)

要求：写一个商场收银函数，输入产品价格和数量，输出需要收取的费用。

```python
def calcExpenditrue(unit_price, amount):
    return unit_price * amount
```

现在来了新的要求：
- 有会员卡打 8 折
- 节日的话，100元以内部分打7折，高于100元部分打九折
- 上述两者优惠同时成立时，选择优惠力度大的

```python
def calcExpenditure(unit_price, amount, user, date):
def calcExpenditure(unit_price, amount, user, date):
    origin_expenditure = unit_price*amount

    vip_expenditure = origin_expenditure*0.8

    if origin_expenditure <= 100:
        holiday_expenditure = origin_expenditure * 0.7
    else:
        holiday_expenditure = 70+(origin_expenditure-100)*0.9
    final_expenditure = origin_expenditure
    
    if user.isvip() and not date.isholiday():
        final_expenditure = vip_expenditure
    if not user.isvip() and date.isholiday():
        final_expenditure = holiday_expenditure
    if user.isvip() and date.isholiday():
        final_expenditure = min(vip_expenditure, holiday_expenditure)
    
    return final_expenditure
```
看起来代码还是有点复杂，不简洁，也还能将就，不过现在又来了新的策略：妇女节的时候全体女士可以享受折上折的9折，这下好了，要大改了！


### 职责链模式(Chain of Responsibility)

### 命令模式(Command)

### 访问者模式(Visitor)

### 调停者模式(Mediator)

### 备忘录模式(Memento)

### 迭代器模式(Iterator)

### 解释器模式(Interpreter)



