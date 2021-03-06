迭代是数据处理的基石。扫描内存中放不下的数据集时，我们要找到一种惰性获取数据项的方法。即按需一次获取一个数据项，这就是**迭代器模式（iterator pattern）**。

`yield` 关键字用于构建生成器（generator），其作用于迭代器一样。所有生成器都是迭代器，因为生成器完全实现了迭代器接口。不过根据《设计模式》一书中的定义：迭代器用于从集合中取出元素，而生成器用于“凭空”生成元素。

内置的 iter 函数有以下作用：
1. 检查对象是否实现了 `__iter__` 方法，如果实现了就调用它，获取一个迭代器；
1. 如果没有实现 `__iter__` 方法，但是实现了 `__getitem__` 方法，Python 会创建一个迭代器，尝试按顺序（从索引0开始）获取元素；
1. 如果尝试崇拜，Python 抛出 `TypeError` 异常；

