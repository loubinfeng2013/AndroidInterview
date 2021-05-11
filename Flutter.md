# Flutter基础
## 1.Dart 是不是单线程模型？是如何运行的？
主要是依赖消息循环机制，Dart中用isolate表示线程，一个isolate中包含一个消息循环（event loop）和两个消息队列（microtask queue和
event queue），当主isolate启动，消息循环就开始工作，会优先从microtask queue取消息处理，无任务需要执行后，再从event queue取消息
处理，执行完一个会再去询问是否有microtask queue任务，永远都是优先microtask queue中的任务执行。</br>
microtask queue（微任务队列）:isolate内部事件处理</br>
event queue（事件队列）:io事件，绘制事件，手势事件，接收其他isolate消息的外部事件</br>
PS：不能将耗时操作添加到微任务队列中，这会影响绘制导致卡顿

## 2.Dart 是如何实现多任务并行的
主要是依赖isolate，与Thread不同，每个isolate拥有独立的内存，isolate是由线程和独立内存构成。由于不共享内存，所以就不存在线程安全的
问题。两个isolate之间的消息通信主要依赖isolate port的数据交换。isolate不是传统意义上的线程概念，而是更像进程。
