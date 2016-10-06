#  [python设计模式之单例模式](http://dongweiming.github.io/python-singleton.html)

## 单例模式

通过Singleton模式，全局保证只有一个实例，在python中怎么用呢？

## python的例子

我这里实现了一个pymongo连接的‘缓存’,为了保证在一个进程里面调用只产生一个连接数

    
    
    class ConnectionSingleton(object):
        '''通过重载实例化函数__new__缓存mongodb连接'''
        conn=None
        def __new__(cls,*args,**kwds):
            if cls.conn is None:
                cls.conn=pymongo.MongoClient()
            return cls.conn
    

调用的时候可以

    
    
    db = ConnectionSingleton()
    

## 和pymongo相关的温馨提示

  1. 请不要使用pymongo.Connection，官网不推荐，并且有个问题，当你close它的实例，连接数并没有立刻减少
  2. 做好数据库操作的类变量的封装，调用完毕要显式close/disconnect


