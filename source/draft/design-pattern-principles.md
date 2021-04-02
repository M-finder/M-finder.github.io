---
title: php 和设计模式 - 设计原则
date: 2021-03-21 14:35:02
categories:
- 设计模式
tags: 
---

设计模式是一套被反复使用、多数人知晓、经过分类编目的、代码设计经验的总结。使用设计模式是为了 `提高代码复用性` 和 `灵活性`，让代码更容易被他人理解、保证代码 `可靠性`。

为了实现代码的 `可复用性` 和 `灵活性`。`设计模式` 提出了一些关键的 `面向对象设计原则`。


#### 单一职责

其核心思想为：一个类，最好只做一件事，应该仅有一个引起它变化的原因。

可以理解为，一个类，应该是一组 `相关性很高` 的方法及数据的封装。

当一个类承担的职责过多时，就相当于把这些职责耦合在了一起，当其中一个职责发生变动，可能会对其他职责造成影响。

类的职责包括两个方面，数据职责和行为职责，数据职责通过类的属性实现，行为职责通过其方法实现。

单一职责是实现高内聚、低耦合的指导方针。它是最简单但又最难实现的原则，需要开发人员发现类的的不同职责并将其分离。

举个🌰：登陆模块显示登录页面，校验登录参数，连接数据库，查找用户，返回结果。

功能太过耦合，拆分成多个模块。


#### 开闭原则

开闭原则是面向对象中最重要的原则。

一个软件应当对扩展开放，对修改关闭。也就是说在设计一个模块的时候，应该使这个模块可以在不被修改的前提下进行扩展。

一个类一旦开发完成，后续新增的功能就不应该通过修改这个类来完成，而是应该通过继承增加新的类。为什么要对修改关闭呢？因为一旦修改某个类，就可能破坏了系统原有功能，就需要重新测试。

`抽象化` 是开闭原则的关键。什么是抽象化，就是把一个或多个类中的公共的、有共性的东西抽取出来。抽象的最大好处在于它是抽象的、稳定的，不容易发生改变的。实现开闭原则的核心思想就是 `面向接口编程`，而不是具体实现。

开闭原则可以用一个更加具体的原则来描述：`可变性封装`。也就是找到系统中的可变因素并把它封装起来。

上一篇中提到的灯是个绝佳的🌰。

#### 里氏替换
所有引用基类（父类）的地方必须能透明的使用其子类的对象。

这句话怎么理解呢？通俗来讲就是在软件中如果能够使用基类对象，那么一定也可以使用其子类对象。把基类都替换为它的子类对象，程序不会产生任何错误和异常。反过来则不成立。

里氏替换应该是开闭原则的一个扩展，由于使用基类对象的地方都可以使用其子类对象，因此在程序中尽量以基类类型来对对象进行定义，而在运行时再用其子类对象替换基类对象。

其中有一点很关键，里氏替换原则强调子类尽量使用基类中的方法，而不是重写，除非子类有其特殊性。

举个🌰，依然是上一篇提到的{% post_link https://www.m-finder.com/2021/03/21/design-pattern-object/  灯 %}，但是加了一点改动：
```php
class Light
{
    function show()
    {
        echo '灯光随机', PHP_EOL;
    }
}

class BlueLight extends Light
{
    function show()
    {
        echo '蓝色', PHP_EOL;
    }
}

class RedLight extends Light
{
    private bool $power = false;

    public function hasPower(): bool
    {
        return $this->power;
    }

    /**
     * @throws Exception
     */
    function show()
    {
        if(!$this->hasPower()){
            throw new Exception('没电了，亮不起来');
        }
        echo '红色', PHP_EOL;
    }
}

class User
{
    function openLight(Light $light)
    {
        $light->show();
    }
}

$user = new User();
$light = new Light();
$blueLight = new BlueLight();
$redLight = new RedLight();

$user->openLight($light);
$user->openLight($blueLight);
$user->openLight($redLight);
```

![uml](/images/uml.png)

根据里氏替换原则，子类必须能够替代父类。也就是说，虽然子类重写了父类的方法，但是在能够使用父类的场景里面，也一定要能够使用子类。很显然，BlueLight 类符合原则，RedLight 类虽然也实现了父类方法，但是抛出了父类没有的异常，所以违反了里氏替换原则，在 User 类中，我们无论是传入 Light 基类对象，还是 BlueLight 类对象，都是没有任何错误和异常的。

由以上实例，我们可以总结出，里氏替换原则本质是对继承的约束。

#### 依赖倒置

依赖于抽象层，不依赖于具体。即高层次的模块不应该依赖低层次模块。
抽象不应该依赖于细节，细节应该依赖于抽象。

要面向接口编程，而不是面向实现编程。

一般情况下，我们认为调用者是高层模块，被调用者是底层模块。

实现开闭原则的关键是抽象化，如果说实现开闭原则是面向对象编程的目标，那么依赖倒置就是面向对象编程的主要手段。常用的手段为在代码中使用抽象类，将具体实现放入元数据。

再强调一遍，抽象是相对稳定的，不容易发生改变。

再举个🌰：
```php
class Book
{
    public function getContent()
    {
        echo 'long long ago, 遥远的东方有一个特别英俊帅气的小伙', PHP_EOL;
    }
}

class Mother
{
    public function narrate(Book $book)
    {
        $book->getContent();
    }
}

class Paper
{
    public function getContent()
    {
        echo '上周五，中国首次实现经济反超美国称为世界第一经济体', PHP_EOL;
    }
}
$mother = new Mother();
$mother->narrate(new Book());
```
在上边的例子中，麻麻看书讲故事，如果有一天，书看烦了，想看个报纸，但是麻麻做不到，因为要读报纸首先要把麻麻改掉。这就有点荒谬。显示不是一个好的设计，原因就是麻麻和书之间的耦合程度太高了，必须降低他们之间的耦合度。

我们来做下调整：
```php
interface Reader
{
    public function getContent();
}

class Book implements Reader
{
    public function getContent()
    {
        echo 'long long ago, 遥远的东方有一个特别英俊帅气的小伙', PHP_EOL;
    }
}

class Paper implements Reader
{
    public function getContent()
    {
        echo '上周五，中国首次实现经济反超美国称为世界第一经济体', PHP_EOL;
    }
}

class Mother
{
    public function narrate(Reader $reader)
    {
        $reader->getContent();
    }
}

$mother = new Mother();
$mother->narrate(new Book());
$mother->narrate(new Paper());
```
首先，我们抽取出一个接口类 `Reader`，然后 Book 和 Paper 分别去实现它。然后将 Mother 调整为依赖于接口。
现在，无论是想看书还是想看报纸，又或者想看连环画，我们只要去实现 Reader 即可，再也不用改动 Mother 了。

上边里氏替换中灯的例子其实已经符合依赖倒置原则，但是看到这个例子更生动，更容易理解，所以就赘述了一下。

> 传递依赖关系的方式有三种，上边的例子中使用的接口传递，还有构造函数传递和 setter 传递。


#### 接口隔离

客户端不应该依赖它不需要的接口。

使用多个专门的接口，而不是一个大的单一的接口。

看上去似乎和单一职责很像，但是不是，单一职责针对的是类的职责，接口隔离针对的则是接口。

举个🌰：
```php
interface WorkerInterface
{
    public function work();

    public function sleep();
}

class HumanWorker implements WorkerInterface
{

    public function work()
    {
        echo 'I like working', PHP_EOL;
    }

    public function sleep()
    {
        echo 'I like sleeping', PHP_EOL;
    }
}

class RobotWorker implements WorkerInterface
{
    public function work()
    {
        echo 'I like working', PHP_EOL;
    }

    public function sleep()
    {
        echo 'robot never sleep', PHP_EOL;
    }
}
```
上边的例子中，有个很明显的缺点，机器人不需要睡觉，但是它却必须实现睡觉的方法，这显然违反了接口隔离。

那么，我们再调整一下：
```php
interface WorkInterface
{
    public function work();

}

interface SleepInterface
{
    public function sleep();
}

class HumanWorker implements WorkInterface, SleepInterface
{

    public function work()
    {
        echo 'I like working', PHP_EOL;
    }

    public function sleep()
    {
        echo 'I like sleeping', PHP_EOL;
    }
}

class RobotWorker implements WorkInterface
{
    public function work()
    {
        echo 'I like working', PHP_EOL;
    }
}
```
调整后，接口一分为二，实际使用时各取所需，不再被迫实现自己不需要的接口。

#### 合成复用

#### 迪米特法则




