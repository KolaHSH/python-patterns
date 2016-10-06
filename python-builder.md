#  [python设计模式之Builder模式](http://dongweiming.github.io/python-builder.html)

## Builder模式

Builder模式帮你把一个不同类的大工程分开成不同的部分，最后在把这些合并起来

## python的例子

制造一辆车的工序是很繁琐和浩大的，我们可以定一个车辆的描述

    
    
    class Vehicle(object):
    
        def __init__(self, type_name):
            self.type = type_name
            self.wheels = None
            self.doors = None
            self.seats = None
    
        # 不同的车子类型，齿轮，车门数，座位数都不尽相同
        def view(self):
            print(
                "This vehicle is a " +
                self.type +
                " with; " +
                str(self.wheels) +
                " wheels, " +
                str(self.doors) +
                " doors, and " +
                str(self.seats) +
                " seats."
                )
    

这里我要把制造齿轮，车门，座位的工序抽象出来，用不同的制造部门去做

    
    
    class VehicleBuilder(object):
        __metadata__ = abc.ABCMeta
        # 装饰器定义抽象方法（只能用在 metaclass 为 abc.ABCMeta 或其子类的 class 中）
        # 这里其实当然可以用pass，或者raise一个基类未实现的异常
        @abc.abstractmethod
        def make_wheels(self):
            raise
    
        @abc.abstractmethod
        def make_doors(self):
            raise
    
        @abc.abstractmethod
        def make_seats(self):
            raise
    

然后设想我要制造汽车和自行车， 每个都要继承这个VehicleBuilder基类，但是齿轮，车门，座位都是不一样的

    
    
    class CarBuilder(VehicleBuilder):
    
        def __init__(self):
            self.vehicle = Vehicle("Car ")
    
        def make_wheels(self):
            self.vehicle.wheels = 4
    
        def make_doors(self):
            self.vehicle.doors = 3
    
        def make_seats(self):
            self.vehicle.seats = 5
    
    class BikeBuilder(VehicleBuilder):
    
        def __init__(self):
            self.vehicle = Vehicle("Bike")
    
        def make_wheels(self):
            self.vehicle.wheels = 2
    
        def make_doors(self):
            self.vehicle.doors = 0
    
        def make_seats(self):
            self.vehicle.seats = 2
    

下面是最重要的环节：制造的Director

    
    
    class VehicleManufacturer(object):
        def __init__(self):
            self.builder = None
        # 根据传进来的builder制造它提供的产品规格
        def create(self):
            assert not self.builder is None, "No defined builder"
            self.builder.make_wheels()
            self.builder.make_doors()
            self.builder.make_seats()
            return self.builder.vehicle
    

看起来是不是有点和以前的模式不好分辨？ 主要下面的调用方式

    
    
    if __name__ == "__main__":
        manufacturer = VehicleManufacturer()
        # 重要的是这样的调用方式，每次修改builder方法传入不同的要求，然后在create里面去制造，
        # 整个过程都是在VehicleManufacturer完成
        manufacturer.builder = CarBuilder()
        car = manufacturer.create()
        car.view()
    
        manufacturer.builder = BikeBuilder()
        bike = manufacturer.create()
        bike.view()
    


