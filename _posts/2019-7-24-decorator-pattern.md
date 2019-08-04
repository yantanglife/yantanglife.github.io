---
layout: post
title:  "Decorator Pattern"
date:   2019-7-23 15:00:00
categories: Design-Pattern
permalink: /archivers/decorator-pattern
nocomments: true
---
装饰模式可以在不改变一个对象本身功能的基础上给对象增加额外的新行为，在现实生活中，这种情况也到处存在.

例如一张照片，我们可以不改变照片本身，给它增加一个相框，使得它具有防潮的功能，
而且用户可以根据需要给它增加不同类型的相框，甚至可以在一个小相框的外面再套一个大相框.

装饰模式是一种用于替代继承的技术，它通过一种无须定义子类的方式来给对象动态增加职责，
使用对象之间的关联关系取代类之间的继承关系。在装饰模式中引入了装饰类，
在装饰类中既可以调用待装饰的原有类的方法，还可以增加新的方法，以扩充原有类的功能.
<!--more-->

## **定义**
**装饰模式(Decorator Pattern)：动态地给一个对象增加一些额外的职责，就增加对象功能来说，
装饰模式比生成子类实现更为灵活。装饰模式是一种对象结构型模式.**

![pic]({{ "/images/2019-7-24/decorator_pattern.png" | absolute_url }})

**<center>装饰模式结构图</center>**

● `Component`（抽象构件）：它是具体构件和抽象装饰类的共同父类，声明了在具体构件中实现的业务方法，
它的引入可以使客户端以一致的方式处理未被装饰的对象以及装饰之后的对象，实现客户端的透明操作.

● `ConcreteComponent`（具体构件）：它是抽象构件类的子类，用于定义具体的构件对象，
实现了在抽象构件中声明的方法，装饰器可以给它增加额外的职责（方法）.

● `Decorator`（抽象装饰类）：它也是抽象构件类的子类，用于给具体构件增加职责，
但是具体职责在其子类中实现。它维护一个指向抽象构件对象的引用，通过该引用可以调用装饰之前构件对象的方法，
并通过其子类扩展该方法，以达到装饰的目的.

● `ConcreteDecorator`（具体装饰类）：它是抽象装饰类的子类，负责向构件添加新的职责。
每一个具体装饰类都定义了一些新的行为，它可以调用在抽象装饰类中定义的方法，
并可以增加新的方法用以扩充对象的行为.
* * *
## **例子**
Python实现:

```python
from abc import ABC, abstractmethod

class Component(ABC):
    @abstractmethod
    def display(self):
        pass

class Window(Component):
    def __init__(self):
        self.name = "Windows"
    def display(self):
        print("Show {}.".format(self.name))

class TextBox(Component):
    def __init__(self):
        self.name = "TextBox"
    def display(self):
        print("Show {}.".format(self.name))

class ListBox(Component):
    def __init__(self):
        self.name = "ListBox"
    def display(self):
        print("Show {}.".format(self.name))

class ComponentDecorator(Component):
    def __init__(self, component):
        self.component = component
    def display(self):
        self.component.display()

class ScrollBarDecorator(ComponentDecorator):
    def __init__(self, component):
        self.component = component

    def display(self):
        self.set_scrollbar()
        super().display()

    def set_scrollbar(self):
        self.component.name += " + ScrollBar"
        # print("Add ScrollBar.")

com = Window()
dec = ScrollBarDecorator(com)
dec.display()

```

***在Python中没有对象的声明？ 对象在创建时就被实例化了.***

## **透明装饰模式和半透明装饰模式**
### **透明装饰模式**
在透明装饰模式中，要求客户端完全针对抽象编程，
装饰模式的透明性要求客户端程序不应该将对象声明为具体构件类型或具体装饰类型，
而应该全部声明为抽象构件类型。对于客户端而言，具体构件对象和具体装饰对象没有任何区别.
也就是应该使用如下代码：
```
Component  c, c1; //使用抽象构件类型定义对象
c = new ConcreteComponent()；
c1 = new ConcreteDecorator (c)；
```
透明装饰模式可以让客户端透明地使用装饰之前的对象和装饰之后的对象，无须关心它们的区别，
此外，还可以对一个已装饰过的对象进行多次装饰，得到更为复杂、功能更为强大的对象.

在实现透明装饰模式时，要求具体装饰类的`operation()`方法覆盖抽象装饰类的`operation()`方法，
除了调用原有对象的`operation()`外还需要调用新增的`addedBehavior()`方法来增加新行为.

### **半透明装饰模式**
透明装饰模式的设计难度较大，而且有时我们需要单独调用新增的业务方法。
为了能够调用到新增方法，我们不得不用具体装饰类型来定义装饰之后的对象，
而具体构件类型还是可以使用抽象构件类型来定义，这种装饰模式即为半透明装饰模式，
也就是说，对于客户端而言，具体构件类型无须关心，是透明的；但是具体装饰类型必须指定，这是不透明的.

半透明装饰模式可以给系统带来更多的灵活性，设计相对简单，使用起来也非常方便；
但是其最大的缺点在于不能实现对同一个对象的多次装饰，
而且客户端需要有区别地对待装饰之前的对象和装饰之后的对象.

在实现半透明的装饰模式时，我们只需在具体装饰类中增加一个独立的`addedBehavior()`方法来封装相应的业务处理，
由于客户端使用具体装饰类型来定义装饰后的对象，因此可以单独调用`addedBehavior()`方法来扩展系统功能.
* * *
***<center>为什么半透明装饰模式不能实现对同一个对象的多次装饰？</center>***

因为在半透明装饰模式中，使用具体装饰类来声明装饰之后的对象，具体装饰类中新增的方法并未在抽象构建类中声明，
这样做的优点在于装饰后客户端可以单独调用在具体装饰类中新增的业务方法，但是将导致无法调用到之前装饰时的方法，
只能调用到最后一次装饰时具体装饰类中新增加的方法，故对同一个对象实施多次装饰没有任何意义.
* * *
## **总结**
### **优点**
* 对于扩展一个对象的功能，装饰模式比继承更加灵活性，不会导致类的个数急剧增加.
* 可以通过一种动态的方式来扩展一个对象的功能，通过配置文件可以在运行时选择不同的具体装饰类，
从而实现不同的行为.
* 可以对一个对象进行多次装饰，通过使用不同的具体装饰类以及这些装饰类的排列组合，
可以创造出很多不同行为的组合，得到功能更为强大的对象.
* 具体构件类与具体装饰类可以独立变化，用户可以根据需要增加新的具体构件类和具体装饰类，
原有类库代码无须改变，符合“开闭原则”.

### **缺点**
* 使用装饰模式进行系统设计时将产生很多小对象，这些对象的区别在于它们之间相互连接的方式有所不同，
而不是它们的类或者属性值有所不同，大量小对象的产生势必会占用更多的系统资源，在一定程序上影响程序的性能.
* 装饰模式提供了一种比继承更加灵活机动的解决方案，但同时也意味着比继承更加易于出错，排错也很困难，
对于多次装饰的对象，调试时寻找错误可能需要逐级排查，较为繁琐.

### **适用场景**
* 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责.
* 当不能采用继承的方式对系统进行扩展或者采用继承不利于系统扩展和维护时可以使用装饰模式.
不能采用继承的情况主要有两类：第一类是系统中存在大量独立的扩展，
为支持每一种扩展或者扩展之间的组合将产生大量的子类，使得子类数目呈爆炸性增长；
第二类是因为类已定义为不能被继承.

* * *
内容来自【作者：刘伟 [http://blog.csdn.net/lovelion]([http://blog.csdn.net/lovelion)】