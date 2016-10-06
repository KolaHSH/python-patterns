#  [python设计模式之策略模式](http://dongweiming.github.io/python-strategy.html)

## 策略模式

Strategy 模式和抽象工厂可能最难分辨，抽象工厂帮助我们根据我们的参数找到对应的操作工厂，而每个操作工厂提供了相同的接口函数
操作工厂所以可能是从一个基类继承的不同实现，举个例子，有个鸭子的基类，然后有很多品种的鸭子是不同的操作工厂，本来根据抽象工厂会返回这个操作工厂来获得这个特定的鸭子的类型，比如一个fly的方法，问题出现了：
有些鸭子会飞，有些不会飞。在实际中有Mixin的角度，但是我们经常能看见这样的实现：

    
    
    class Duck(object)
        # 明确不能直接访问基类的这个方法，他们是具体类去实现
        # kind 标识能不能飞, 可以设置yes和no
        def fly(self, kind):
            raise NotImplementedError('Exception raised, Duck is supposed to be an interface / abstract class!')
    
    class Duck1(Duck):
        def fly(selfi, kind):
            return 'Duck1 fly kind: ' + kind
    
    class Duck2(Duck):
        def fly(self), kind:
            return 'Duck2 fly kind: ' + kind
    

看到了吧，这种差异化的问题派生不是最好的办法，因为你不能因为某个个体的功能就去给基类添加这个功能，比如上面的fly，Duck2其实不需要有这个方法
策略模式就是_分开这些易于变化的部分_

## python的例子

还是上面的例子，上面的代码简单的继承了基类，我们可以写的更高端，并且显示调用的次数

    
    
    class Duck(object):
        # 上面使用继承，这里通用的使用参数方式，传入的就是操作工厂的类
        def __init__(self, strategy=None):
            self.action = None
            self.count = 0
            if strategy:
                # 指定策略，那么执行action就是用这个类的实例
                self.action = strategy()
    
        def fly(self, kind):
            if self.action:
                self.count += 1
                # 这里的第二个参数self，算是炫技吧，就是为了让操作的方法获得这里计算好的count
                return self.action.fly(kind, self)
    
            else:
                raise UnboundLocalError('Exception raised, no strategyClass supplied to Duck!')
    
    # 注意这里没有继承Duck，因为是以参数的方式传入类名
    class Duck1(object):
    
        def fly(self, kind, instance):
            return 'Duck1 fly kind: ' + kind + '#' + str(instance.count)
    
    
    class Duck2(object):
    
        def fly(self, kind, instance):
            return 'Duck2 fly kind: ' + kind + '#' + str(instance.count)
    
    
    if __name__ == '__main__':
        duckfly = Duck()
        duck1fly = Duck(strategy=Duck1)
        duck2fly = Duck(strategy=Duck2)
    
        try:
            print duckfly.fly('yes')
        except Exception as e:
            print "The following exception was expected:"
            print e
    
        print duck1fly.fly('yes')
        print duck1fly.fly('no')
        print duck1fly.fly('yes')
        print duck2fly.fly('yes')
        print duck2fly.fly('no')
    


