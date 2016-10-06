#  [python设计模式之状态模式](http://dongweiming.github.io/python-state.html "Permalinkto python设计模式之状态模式" )

## State模式

状态模式是一种常用的模式，和名字一样，可以在调用时修改其内部属性，看起来就像改变了类的状态

## python的例子

    
    
    class NetworkCardState:
        """基类"""
        def send(self):
            raise NotImplementedError
    
        def receive(self):
            raise NotImplementedError
    
    class Online(NetworkCardState):
        """在线"""
        def send(self):
            print "sending Data"
    
        def receive(self):
            print "receiving Data"
    
    class Offline(NetworkCardState):
        """离线"""
        def send(self):
            print "cannot send...Offline"
    
        def receive(self):
            print "cannot receive...Offline"
    
    class NetworkCard:
        def __init__(self):
            self.online = Online()
            self.offline = Offline()
            # 修改内部属性currentState，默认是离线，直接传入类
            self.currentState = self.offline
    
        def startConnection(self):
            # 修改状态成在线
            self.currentState = self.online
    
        def stopConnection(self):
            self.currentState = self.offline
    
        def send(self):
            # 去掉用这个可变的属性的方法，达到看起来是操作了类的属性的改变
            self.currentState.send()
    
        def receive(self):
            self.currentState.receive()
    
    def main():
        myNetworkCard = NetworkCard()
        print "without connection:"
        myNetworkCard.send()
        myNetworkCard.receive()
        print "starting connection"
        myNetworkCard.startConnection()
        myNetworkCard.send()
        myNetworkCard.receive()
    
    if __name__ == '__main__':
        main()
    


