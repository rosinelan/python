## Queue
> 队列是线程最常用的交换数据形式. 

```python
#coidng:utf-8

import time
import Queue
import logging
import threading

class thread(threading.Thread):
    def __init__(self, name, q):
        threading.Thread.__init__(self)
        self.name = name
        self.q = q

    def run(self):
        if self.name == 'collect':
            self.collectLoop() 
        elif self.name == 'sendjson':
            self.sendloop()

    def collectLoop(self):
        for x in range(100):
            self.q.put(x)
            logging.debug('put %s', x)
            time.sleep(0.5)

    def sendloop(self):
        while True:
            if not self.q.empty():
                data = self.q.get()
                logging.debug('get %s', data)
            time.sleep(2)

def main():
    q = Queue.Queue(10)

    q1 = thread('collect', q)
    q1.start()

    time.sleep(1)

    q2 = thread('sendjson', q)
    q2.start()

    q1.join()
    q2.join()

if __name__ == '__main__':
    FORMAT = '%(asctime)-15s %(threadName)5s %(levelname)s %(message)s '
    logging.basicConfig(level=logging.DEBUG, format=FORMAT)
    main()
```

> Queue类方法  

```
>>> q = Queue.Queue(maxsize=10)
创建一个队列并指定一个队列的长度

>>> q.put()
写队列，timeout等待时间

>>> q.get()
读队列，timeout等待时间
默认是堵塞的方式读取数据，队列为空时，不会抛出except Queue.Empty，而是进入了堵塞直至超时，
加上参数block=False可以解决这个问题.

>>> q.maxsize
查看创建队列时maxsize的值

>>> q.qsize()
查看当前队列的长度

>>> q.empty()       
如果队列长度为空 返回True，反之False.

>>> q.full()
如果队列满了 返回True 反之False.

>>> q.queue.clear()
从队列中移除所有的item.
```
