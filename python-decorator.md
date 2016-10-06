#  [python设计模式之装饰器模式](http://dongweiming.github.io/python-decorator.html"Permalink to python设计模式之装饰器模式" )

## 装饰器模式

在python中装饰器已经算是代码级别的模式了，装饰器这个@符号会深入你我心，而这个技能也是写代码必备之大技.增加额外的功能，装饰器其实就是在你执行类或者函数之前给你封装了一层或者多层(多个装饰器，这在web框架代码中很常见)操作，z但是不影响你最后执行代码
其实原理就是：有一些函数或者类里面有一些步骤是一样的(比如对数据缓存，比如加锁和释放)，那么你就可以把这些重复的东西拿出来放在装饰器里面，这样每个函数或者类就不需要写这样多的代码，而抽象出来。而通过这样的额外功能可以还能让你执行的函数或者类计数等

## python的例子

一时间还想不起来干啥，好吧写了个缓存数据到redis的装饰器

    
    
    from functools import wraps
    from redis import Redis
    redis = Redis()
    
    def cached(timeout=5 * 60):
        def decorator(f):
            # wraps帮你将函数原来的属性复制过来
            @wraps(f)
            def decorated_function(*args, **kwargs):
                # 以执行函数的参数为键
                key = str(args)
                rv = redis.get(key)
                # 发现缓存过直接返回
                if rv is not None:
                    print 'Has match: ', rv
                    return rv
                rv = f(*args, **kwargs)
                redis.setex(key, rv, time=timeout)
                return rv
            return decorated_function
        return decorator
    
    @cached()
    def printNumber(num):
        return num
    
    if __name__ == '__main__':
        # 注意列表数据有重复
        for i in [1, 2, 5, 11, 2, 7, 9, 1]:
            print printNumber(i)
    

说到装饰器就要提一个模块:
[decorator](http://micheles.googlecode.com/hg/decorator/documentation.html) 它是
Michele Simionato 为简化python的decorator的使用难度而开发的，强烈建议深入学习


