#  [python设计模式之桥接模式](http://dongweiming.github.io/python-bridge.html"Permalink to python设计模式之桥接模式" )

## 桥接模式

这个模式其实就是把产品类的实现和抽象类分离，能够灵活的变化，假如你记得[状态模式](http://dongweiming.github.io/python-
state.html),它是修改内部属性，
而桥接模式是指定好内部属性，每个产品类指定这个属性被桥接模式类调用，适用于产品类可能经常调整变化，这样还能减少了产品类之间的耦合

## python的例子

这里实现一个打印操作系统名字的功能

    
    
    class Bridge(object):
    
        def __init__(self):
            self.__implementation = None
    
        def someFunctionality(self):
            raise NotImplemented()
    
    class UseCase1(Bridge):
        # 根据初始化参数传入实现的产品类
        def __init__(self, implementation):
            self.__implementation = implementation
        # 根据传入的产品类的属性打印结果
        def someFunctionality(self):
            print "UseCase1: ",
            self.__implementation.anotherFunctionality()
    
    
    class UseCase2(Bridge):
        def __init__(self, implementation):
            self.__implementation = implementation
    
        def someFunctionality(self):
            print "UseCase2: ",
            self.__implementation.anotherFunctionality()
    
    
    class ImplementationInterface:
    
        def anotherFunctionality(self):
            raise NotImplemented
    
    # 这里其实才是实现的产品类
    class Linux(ImplementationInterface):
    
        # 它定义了这个方法，回应操作系统的名字
        def anotherFunctionality(self):
            print "Linux!"
    
    
    class Windows(ImplementationInterface):
        def anotherFunctionality(self):
            print "Windows."
    
    
    def main():
        linux = Linux()
        windows = Windows()
    
        useCase = UseCase1(linux)
        useCase.someFunctionality()
    
        useCase = UseCase1(windows)
        useCase.someFunctionality()
    
        useCase = UseCase2(linux)
        useCase.someFunctionality()
    
        useCase = UseCase2(windows)
        useCase.someFunctionality()
    
    
    if __name__ == "__main__":
        main()
    


