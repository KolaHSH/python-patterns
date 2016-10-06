#  [python设计模式之Adapter模式](http://dongweiming.github.io/python-adapter.html"Permalink to python设计模式之Adapter模式" )

## 适应模式

Adapter模式是一个很实用的模式，比如在[zope](http://grok.zope.org/doc/current/grok_overview.html#adapters)中。
以前说了抽象工厂模式，你会发现每个产品类的接口方法名都是一样的，比如eat，他们都要吃。这个Adapter模式的区别呢？
各个产品类的操作不尽相同，但是怎么样兼容这些不一样的方法呢？举个能想到的例子，我们渐进的来理解

## 想要通过不同的动物返回他们的声音和类型

我想很多人会这么写

    
    
    class Dog(object):
        def __init__(self, name)
            self.name = name
        def bark(self):
            return "wangwang"
    
    def Animal(animal):
        return animal.name, animal.bark()
    

但要是有很类型的动物，其它动物可不会bark(吠), 所以上面的bark这个方法名字就很不好，因为不够通用 ，好吧 我们换个名字make_noise,
这个时候Cat类出现，Adapter模式也第一次出现，为了兼容Dog

    
    
    class Animal(object):
        '''动物类的基类'''
        # 基类不需要实现make_noise
        def make_noise(self):
            raise NotImplementedError
    
    class Dog(Animal):
        def __init__(self, name):
            self.name = name
    
        def make_noise(self):
            return “wangwang”
    
    # 这里的适应， 也继承了Dog类
    class CatClassAdapter(Animal, Dog):
        def __init__(self, name):
            Dog.__init__(self, name)
    
        def miaow(self):
            return 'miaomiao'
    
        def make_noise(self):
            return self.miaow()
    

这样它们为了兼容都是通过make_noise方法返回不同的相应方法，很多人会问cat类为啥不直接
把这个方法叫做make_noise？其实这个只是个例子，实际工作中很多存在这样的不兼容的代码，
而现在接口一致了，但是新加一个动物就要这么搞一段代码…,而且奇怪的是它们都要继承Dog类，好吧 我们抽象上面的成这样：

    
    
    class CatObjectAdapter(Animal):
        def __init__(self, cat):
            self.cat = cat
    
        def make_noise(self):
            return self.cat.miaow()
    
        # 属性委派，拦截属性调用
        def __getattr__(self, attr):
            return getattr(self.cat, attr)
    

这样好一点了，但是make_noise还是不灵活，它只能用来处理cat，其实想想也没必要从Animal)继承。 继续抽象：

    
    
    class AnimalAdapter(object):
        # 这次把make_noise的规则也传进来
        def __init__(self, animal, make_noise):
            self.animal = animal
            self.make_noise = make_noise
        # 继续委派属性
        def __getattr__(self, attr):
            return getattr(self.animal, attr)
    

集合起上面的东西 然后这样使用：

    
    
    class Dog(object):
        def __init__(self, name):
            self.name = name
        def bark(self):
            return "wangwang"
    
    class Cat(object):
        def __init__(self, name):
            self.name = name
        def meow(self):
            return "meow"
    
    class AnimalAdapter(object):
        def __init__(self, animal, make_noise):
            self.animal = animal
            self.make_noise = make_noise
    
        def __getattr__(self, attr):
            return getattr(self.animal, attr)
    
    if __name__ == '__main__':
        fido = Dog("Fido")
        # 都是这个适应类帮助你转换，怎么转换是你设计好的，他只是个模式
        canine = AnimalAdapter(fido, fido.bark)
        whiskers = Cat("Whiskers")
        feline = AnimalAdapter(whiskers, whiskers.meow)
    
        for i in (canine, feline):
            print i.name, 'says:', i.make_noise()
    

而用抽象工厂模式，大概可以这样:

    
    
    class CatNoise(object):
    def __init__(self, context):
            self.context = context
    def make_noise(self): 
            return self.context.meow()
    class DogNoise(object):
        def __init__(self, context):
            self.context = context
        def make_noise(self):
            return self.context.bark()
    def noise_adapter(context):
        if isinstance(context, Cat):
            return CatNoise(context) 
        elif isinstance(context, Dog):
            return DogNoise(context)
        else:
            raise AdapterLookupError("Could not find adapter")
    

发现了嘛？新增的动物类都要放在判断里面，就没有这种模式感觉舒服了

然后再写的高端一点:

    
    
    noise_adapter_lookup = {Cat: CatNoise, Dog: DogNoise}
    
    def noise_adapter(context):
        try:
            return noise_adapter_lookup[context.__class__](context)
        except KeyError:
            raise AdapterLookupError
    

至于感觉怎样，需要你自己评价了


