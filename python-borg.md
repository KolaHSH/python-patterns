#  [python设计模式之Borg模式](http://dongweiming.github.io/python-borg.html)

## Borg模式

[activestate](http://code.activestate.com)上面有篇文章[Singleton? We don't need no
stinkin' singleton: the Borg design
pattern](http://code.activestate.com/recipes/66531/), 告诉我们Borg模式要比单例模式要好。
为什么这样说：单例模式生成的单例一旦生成就一直存在，但是Borg确实和源共享的能修改

## python的例子

    
    
    class Config(object):
        _we_are_one = {}
        _myvalue = ""
    
        def __new__(cls, *p, **k):
            self = object.__new__(cls, *p, **k)
            self.__dict__ = cls._we_are_one
            return self
    
        def myvalue(self, value=None):
            if value:
            self._myvalue = value
            return self._myvalue
    
    if __name__ == '__main__':
        conf = Config()
        conf.myvalue("Hello")
        conf2 = Config()
        print conf2.myvalue()
    

不要惊讶，其实是因为在修改myvalue的时候不仅修改了conf.__dict__还修改了Config._we_are_one，所以…

然后还有个装饰器版本：

    
    
    def borg(cls):
        cls._state = {}
        orig_init = cls.__init__
        def new_init(self, *args, **kwargs):
            self.__dict__ = cls._state
            orig_init(self, *args, **kwargs)
        cls.__init__ = new_init
        return cls
    
    @borg
    class TestBorg(object):
        def say_borg(self):
            print "i am borg"
    


