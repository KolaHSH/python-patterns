#  [python设计模式之抽象工厂模式](http://dongweiming.github.io/python-abstract-factory.html)

## 抽象工厂模式

和简单工厂/工厂方法不同，抽象工厂可能最好理解，举个例子:  
_ 比如2个动物，猫和狗，他们都有speak和eat的function，但是很明显他们执行的结果是不同的 _
有个'工厂'(抽象工厂里面一个类型就是一个工厂,这点和其他模式的不同)专门帮助我们找到对应的动物做正确的操作 _
有个类/函数可以通过参数帮助我们找到上面的这个工厂 _ 这就是抽象工厂

## python的例子

    
    
    from abc import ABCMeta
    
    class StandardFactory(object):
        '''这就是那个抽象工厂'''
        @staticmethod
        def get_factory(factory):
            '''根据参数找到对实际操作的工厂'''
            if factory == 'cat':
                return CatFactory()
            elif factory == 'dog':
                return DogFactory()
            raise TypeError('Unknown Factory.')
    

    # 这里帮助dog这个产品类找到应该的属性的工厂
    class DogFactory(object):
        def get_pet(self):
            return Dog();
    
    
    class CatFactory(object):
        # 注意这个方法和上面的名字一样，但是返回的类不同，这就是工厂的作用
        def get_pet(self):
            return Cat();
    
    
    # 可以认为dog和cat都是动物的一种，可以有个基类
    class Pet(object):
        # ABCMeta会让这个类在注册后添加很多基础抽象基类，可以看[ABCMeta](http://docs.python.org/2/library/abc.html#abc.ABCMeta)
        __metaclass__ = ABCMeta
        def eat(self):
            pass
    
    
    # Dog应该做什么就是这里
    class Dog(Pet):
        def eat(self):
            return 'Dog food...'
    
    
    class Cat(Pet):
        # 这里的eat依然是同名，她们都是同样的操作，只是返回不同
        def eat(self):
            return 'Cat food...'
    
    
    if __name__ =="__main__":
        factory = StandardFactory.get_factory('cat')
        pet = factory.get_pet()
        print pet.eat()
    
        # 注意这里，你只需要修改抽象工厂传入的那个参数，其他什么都不用改
        factory = StandardFactory.get_factory('dog')
        pet = factory.get_pet()
        print pet.eat()
    


