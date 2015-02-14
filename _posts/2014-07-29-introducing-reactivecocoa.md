---
layout: post
title: 复杂的ReactiveCocoa
---

 
关于 ReactiveCocoa的 文章有很多，但是能够从根本上详细介绍它的太少了，基本上没有。甚至看完了都不知道什么叫side effect。关于字面上方法的介绍这里不做讨论，我们具体来分析它的原理和代码的结构分析，看它监听是如何实现的。帮助我们从根本上理解它。
关于它的组成有这几个RACSignal, RACSubscriber,RACDisposable这三个主要对象，这是三个基础类，其他基本上都是这三个的子类(当然RACSignal是继承RACStream),其实RACSignal是信号流，是RAC的主要类，RACSubscriber是接受者，RACSignal
会存储它所有的RACSubscriber,一旦有新的变化RACSignal会通知所有的RACSubscriber，最后RACDisposable是用来清理和释放内存的。
  我们来简单分析一下代码：
  
  ![code1](/assets/2014-07-29-01.png)

我们可以看到，其实RACSignal创建的是它的子类RACDynamicSignal。我们再跟进去它的另一个方法
![code1](/assets/2014-07-29-02.png)


我们看到了如下的方法，首先通过nextblock 创建一个RACSubscriber对象，然后进行了subscribe操作，那么这个操作是什么作用了？应该是对RACSubscriber的相关处理，在RACSignal 中subcribe方法是空，因为创建实际上穿件的是RACDynamicSignal，所以我们跟进到它的sbucribe方法中。

![code1](/assets/2014-07-29-03.png)


我们主要看标注的地方，上面的_subscribers储存所有加入的RACSubscriber,然后下面的方法，我们仔细观察它调用了self.didSubscribe(subscriber),来回调我们刚刚创建RACSignal的那个didsubcribe的block。接下来我们进行关键的步骤，分析它是如何监听的：
\#define RACObserve(TARGET, KEYPATH) \    [(id)(TARGET) rac_valuesForKeyPath:@keypath(TARGET, KEYPATH) observer:self]
监听的宏定义，在NSObject+RACPropertySubscribing.h中,然后一直跟进去，由于调用太频繁了，具体跟踪就不写了。最后我们可以在NSObject+RACKVOWrapper的里面发现这样一个方法

![code1](/assets/2014-07-29-04.png)

然后我们再跟进RACKVOTrampoline中,会发现一个特别的东西里面的原理了，原来也是通过调用KVO来实现的。在RACKVOTrampoline的
observeValueForKeyPath:ofObject:change:context:
会调用传进来的block ，block 里面调用RACSubscriber的sendNext方法。里面会调用RACSubscriber next block。整个流程就是这样了。分析了具体原理，以后的具体方法我们应该都可以结合注释看懂了。

关于这篇是仓促之下写下的，以后会吧图片替换成code，并且修缮这篇blog。