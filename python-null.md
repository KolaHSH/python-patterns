#  [python设计模式之Null模式](http://dongweiming.github.io/python-null.html)

## Null模式

我想每个人都有一种经历，为了获取某属性，但是有时候属性是None，那么需要你做异常处理，
而假如你想节省这样的条件过滤的代码，可以使用Null模式以减少对象是否为None的判断

## python的例子

我举个不是很通用的例子，只是为了让大家理解这个模式：我有很多类， 但是不是每个类都有类方法test，所以我调用类方法就要做个异常处理，类似这样

    
    
    class A(object):
        pass
    
    class B(object):
        b = 1
        @classmethod
        def test(cls):
            print cls.b
    
    def get_test(x):
        try:
            return x.test
        except AttributeError: 
            return None
    
    # 我这里只写了2个类，但是其实有很多类
    for i in [A, B]:
        test = get_test(i)
        # 我要判断以下是否获得了这个类方法才能决定是否可以执行
        if test:
            test()
    

但是我用Null方法就可以这样

    
    
    class Null(object):
    
        def __init__(self, *args, **kwargs):
            "忽略参数"
            return None
    
        def __call__(self, *args, **kwargs):
            "忽略实例调用"
            return self
    
        def __getattr__(self, mname):
            "忽略属性获得"
            return self
    
        def __setattr__(self, name, value):
            "忽略设置属性操作"
            return self
    
        def __delattr__(self, name):
            '''忽略删除属性操作'''
            return self
    
        def __repr__(self):
            return "<Null>"
    
        def __str__(self):
            return "Null"
    

还是上面的功能

    
    
    def get_test_with_null(x):
        try:
            return x.test
        # 异常处理返回Null类
        except AttributeError:
            return Null()
    
    for i in [A, B]:
        # 直接调用了，不需要判断
        get_test_with_null(i)()
    


