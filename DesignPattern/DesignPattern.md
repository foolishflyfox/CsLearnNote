---
export_on_save:
 html: true
---
@import "/blog_head.md"

> 《设计模式，可复用面向对象软件的基础》 笔记

# 第一章 引言

有经验的面向对象设计者会告诉你，要一下子就得到复用性和灵活性好的设计，即使不是不可能的，至少也是非常困难的。一个设计在最终完成之前常要被复用好多次，而且每次都有所修改。

内行的设计者找到：不是解决任何问题都要从头开始做起。他们更愿意复用以前使用过的解决方案。找到一个好的解决方案，他们就会一遍又一遍都使用。这些经验是他们成为内行的部分原因。

以下类比可以帮助说明这一点。小说家和剧本作家很少从头开始设计剧情。他们总是沿袭一些业已存在的模式，像“悲剧性英雄”模式（《麦克白》、《哈姆雷特》）或是浪漫小说模式。同样地，面向对象设计员也沿袭了一些模式，像“用对象表示状态” 和 “修饰对象以便于你更容易地添加/删除属性”等。一旦懂了模式，许多设计决策自然而然就产生了。

设计模式使人可以更加简单方便地复用成功的设计和体系结构。将已经证实的技术表述成设计模式也会使新系统开发者更容易理解其设计思路。

## 什么是设计模式

> 每一个模式描述了一个在我们周围不断重复发生的问题以及该问题的解决方案的核心。这样，你就能一次次使用该方案而不必做重复劳动。——Christopher Alexander

一般而言，一个模式有4个基本要素：
1. **模式名称（pattern name）**：一个助记名，它用一两个词来描述模式的问题、解决方案和效果。命名一个新的模式增加了我们的设计词汇。设计模式允许我们在较高的抽象层次上进行设计。基于一个模式词汇表，我们自己以及同事之间就可以讨论设计模式，并在编写文档时使用它们。模式名可以帮助我们思考，便于我们与其他人交流设计思想及设计结果。找到恰当的模式名也是我们设计模式编目工作的难点之一。
1. **问题（problem）**：描述应该在合适使用模式。它解释了设计问题和问题存在之间的前因后果，它可能描述了特定的设计问题，如这样用对象表示算法等。也可能描述了导致不灵活设计的类或对象结构。有时候，问题部分会包括使用模式必须满足的一系列先决条件。

1. **解决方案（solution）**：描述了设计的组成成分，它们之间的相互关系及各自的职责和协作方式。因为模式就像一个模板，可应用于多种场合，所以解决方案并不描述一个特定而具体的设计或实现，而是提供设计问题的抽象描述和怎样用一个具有一般意义的元素组合（类或对象组合）来解决这个问题。
1. **效果（consequences）**：描述了模式应用的效果及使用模式应该权衡的问题。尽管我们描述设计模式时，并不总是提到模式效果，但它们对评价设计选择和理解使用模式的代价及好处具有重要意义。软件效果大多关注对时间和空间的衡量，它们也表述了语言和实现问题。因为复用是面向对象的要素之一，所以模式效果包括它对系统的灵活性、扩充性或可移植性的影响，显示地列出这些效果对理解和评价这些模式很有帮助。

