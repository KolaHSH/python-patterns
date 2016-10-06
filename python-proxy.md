#  [python设计模式之代理模式](http://dongweiming.github.io/python-proxy.html)

## 代理模式

Proxy模式是一种常用的设计模式，它主要用来通过一个对象(比如B)给一个对象(比如A)
提供'代理'的方式方式访问。比如一个对象不方便直接引用，代理就在这个对象和访问者之间做了中介

## python的例子

你先设想：一个对象提供rgb三种颜色值，我想获得一个对象的rgb三种颜色，但是我不想让你获得蓝色属性，怎么办？

    
    
    class Proxy(object):
        def __init__(self, subject):
            self.__subject = subject
        # 代理其实本质上就是属性的委托
        def __getattr__(self, name):
            return getattr(self.__subject, name)
    
    class RGB:
        def __init__(self, red, green, blue):
            self.__red = red
            self.__green = green
            self.__blue = blue
        def Red(self):
            return self.__red
        def Green(self):
            return self.__green
        def Blue(self):
            return self.__blue
    
    class NoBlueProxy(Proxy):
        # 我在这个子代理类拦截了blue的访问，这样就不会返回被代理的类的Blue属性
        def Blue(self):
            return 0
    
    if __name__ == '__main__':
        rgb = RGB(100, 192, 240)
        print rgb.Red()
        proxy = Proxy(rgb)
        print proxy.Green()
        noblue = NoBlueProxy(rgb)
        print noblue.Green()
        print noblue.Blue()
    


