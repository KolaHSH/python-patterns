#  [python设计模式之命令调度模式](http://dongweiming.github.io/python-command.html"Permalink to python设计模式之命令调度模式" )

## 命令调度模式

设想一个问题，假如你经常搞web框架，你知道GET/POST/PUT/DELETE，当你想确定使用那种方式工作你需要大概这样:

    
    
    if command == 'get':
        get()
    elif command == 'put':
        put()
    else:
        error()
    

抑或写一个字典作为调度的方式：

    
    
    dispatch_table = {
        'get': get,
        'put': put,
    }
    
    if dispatch_table.has_key(command):
        func = dispatch_table[command]
        func()
    else:
        error()
    

其实呢？python已经自带了很多这样的功能的东西，Guido van Rossum把这个模式叫做Command Dispatch

喏 这就是,只是你没有意识到

    
    
    def greet(who):
        print "Hello %s" % who
    greet_command = lambda: greet("World")
    greet_command()
    

还有python自带的库[cmd](http://pymotw.com/2/cmd/index.html#module-cmd) 还是上面的例子

    
    
    class Dispatcher:
    
    def do_get(self): ...
    
    def do_put(self): ...
    
    def error(self): ...
    
    def dispatch(self, command):
        mname = 'do_' + command
        if hasattr(self, mname):
            method = getattr(self, mname)
            method()
        else:
            self.error()
    

## python的例子

我这里实现了一个封装pymongo的类，通过工厂方法模式执行insert/update/find操作，我这里只实现个效果

    
    
    from pymongo import MongoClient
    
    class MongoPack(object):
    
        def __init__(self, db):
            self.db = db
    
        # 通过指定kind标识操作的种类:find/insert/update
        def operation(self, coll, kind, **kwargs):
            return getattr(self, kind)(coll, **kwargs)
    
        # 当指定kind为'find'执行这个方法
        def find(self, coll, **kwargs):
            return self.db[coll].find(kwargs)
    
    if __name__ == '__main__':
    
        db = MongoClient()['test']
        d = MongoPack(db)
        print d.operation('test', 'find', id=1)[0]
    


