# 阅读 itchat 源码的总结

## 如何实现架构与实现的分离

架构与具体实现的分离有利于代码的维护，例如主要的架构在 `core.py` 文件中，而其组件在 component 文件夹的各个模块中。现在在 `core.py` 中需要使用 login 的功能，而 `login.py` 位于component 文件夹下，配置文件在当前文件夹的 `config.py` 中，文件结构如下：
