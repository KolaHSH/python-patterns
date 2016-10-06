#  [python设计模式之对象池模式](http://dongweiming.github.io/python-object-pool.html)

## 对象池模式

在开发中，我们总是用到一些和'池'相关的东西，比如 内存池，连接池，对象池，线程池..
这里说的对象池其实也就是一定数量已经创建好的对象的集合。为什么要用对象池？ 创建对象是要付出代价的(我暂时还没有研究过底层，只说我工作中体会的)，
比如pymongo就自带线程池，这样用完就放回到池里再被重用，岂不是节省了创建的花费？

## python的例子

我这里实现了个线程安全的简单的对象池

    
    
    import Queue
    import types
    import threading
    from contextlib import contextmanager
    
    class ObjectPool(object):
    
        def __init__(self, fn_cls, *args, **kwargs):
            super(ObjectPool, self).__init__()
            self.fn_cls = fn_cls
            self._myinit(*args, **kwargs)
    
        def _myinit(self, *args, **kwargs):
            self.args = args
            self.maxSize = int(kwargs.get("maxSize",1))
            self.queue = Queue.Queue()
        def _get_obj(self):
            # 因为传进来的可能是函数，还可能是类
            if type(self.fn_cls) == types.FunctionType:
                return self.fn_cls(self.args)
            # 判断是经典或者新类
            elif type(self.fn_cls) == types.ClassType or type(self.fn_cls) == types.TypeType:
                return apply(self.fn_cls, self.args)
            else:
                raise "Wrong type"
    
        def borrow_obj(self):
            # 这个print 没用，只是在你执行的时候告诉你目前的队列数，让你发现对象池的作用
            print self.queue._qsize()
            # 要是对象池大小还没有超过设置的最大数，可以继续放进去新对象
            if self.queue.qsize()<self.maxSize and self.queue.empty():
                self.queue.put(self._get_obj())
            # 都会返回一个对象给相关去用
            return self.queue.get() 
        # 回收
        def recover_obj(self,obj):
            self.queue.put(obj)
    
    # 测试用函数和类
    def echo_func(num):
        return num
    
    class echo_cls(object):
        pass
    
    # 不用构造含有__enter__, __exit__的类就可以使用with，当然你可以直接把代码放到函数去用
    @contextmanager
    def poolobj(pool):
        obj = pool.borrow_obj()
        try:
            yield obj
        except Exception, e:
            yield None
        finally:
            pool.recover_obj(obj)
    
    obj = ObjectPool(echo_func, 23, maxSize=4)
    obj2 = ObjectPool(echo_cls, maxSize=4)
    
    class MyThread(threading.Thread):
    
        def run(self):
            # 为了实现效果，我搞了个简单的多线程，2个with放在一个地方了，只为测试用
            with poolobj(obj) as t:
                print t
            with poolobj(obj2) as t:
                print t
    
    if __name__ == '__main__':
        threads = []
        for i in range(200):
            t = MyThread()
            t.start()
            threads.append(t)
        for t in threads:
            t.join(True)
    


