# Android体系
## 1.Activity的启动模式（同Activity栈的问题）（Vivo，快手）
standard：标准启动，Activity依次入栈</br>
singleTop：帧顶复用，判断栈顶是否是目标Activity，是直接复用调用onNewIntent，否创建新实例入栈</br>
singleTask：栈内复用，判断栈内是否有目标Activity，是直接复用调用onNewIntent，并且顶出在它之上的其他实例，否创建新实例入栈</br>
singleInstance：独享模式，此Activity独享一个Activity栈
### singleInstance如果不指定栈名，是怎么分配的?
singleInstance是否指定栈名，效果是一样的，都是独享一个Activity栈。原因是因为singleInstance优先级更高（PS：使用taskAffinity可以指定栈名，但是只对singleTask和singleInstance有效，栈名不指定默认使用包名，只要指定栈名，singleTask也能达到独享栈的效果）

## 2.Android的类加载机制（快手）
Java类加载机制：父类优化策略，即加载之前会先代理给自己的父类去加载，父类加载不到才自己加载</br>
Java类加载过程：加载（类加载器负责加载）->链接（验证->准备->解析）->初始化（调用静态块，初始化静态成员）</br>
虚拟机和类加载：JVM使用类加载器加载的是Class文件，DVM使用类加载器加载的是Dex文件</br>
Android常用类加载器：</br>
PathClassLoader：继承于BaseDexClassLoader，主要负责加载系统类，应用类（已安装的）</br>
DexClassLoader：继承于BaseDexClassLoader,主要负责加载jar，dex，apk中的类（可以从SD卡上获取）</br>

## 3.Android生命周期（快手）
onCreate/onDestroy：创建销毁周期</br>
onStart/onStop：可见周期</br>
onResume/onPause：前后台周期</br>
### 如何摧毁一个Activity?（美团）
如果要指定一个Activity销毁，可以创建一个单例的ActivityManager管理所有创建的Activity实例，就可以指定Activity销毁。需要注意的是这种方式使用不当，
会导致Activity无法正常内存回收。</br>
如果要销毁当前Activity，直接调用finish方法，最终调用AMS的finishActivity方法，finish方法的作用会将该Activity从栈内移除，并且回调onDestroy释放一些开发者需要释放的资源，最后系统
负责把该Activity内存资源回收

## 4.Intent显示跳转与隐式跳转，如何使用
显式跳转：需要在intent中显式设置跳转的class对象。在清单文件中注册Activity</br>
隐式跳转：需要在intent中设置Action（执行的动作，不设置就会随机进入一个设置了Action的Activity），或者Category（执行的环境）。在清单文件中注册Activity之外还需要设置Intent-filter的Action（必须设置，否则无法使用）和Category（至少一个，不特殊就设置默认的）

## 5.Activity A跳转B，B跳转C，A不能直接跳转到C，A如何传递消息给C
方案1：Handler方式，在基类中定义一个公共的handler，这样在C中使用该handler，A中也能接收处理</br>
方案2：EventBus方式</br>
方案3：Android广播方式</br>
方案4：观察者模式实践

## 6.Activity如何保存状态的
Activity被回收有两种情况，一种是主动回收，这种情况一般没必要保存状态，另一种是被动回收，比如内存不足时，此情况可以重写onSaveInstanceState方法，将需要保存的数据存入bundle的，下次该Activity再次被创建时，onCreate方法中会带入数据，
也可能重写onRestoreInstanceState处理保存数据。

## 7.请描诉Activity的启动流程，从点击图标开始
创建进程：Launcher进程->(Binder方式)system_server进程中的AMS->(Socket方式)Zogote孵化进程->(Socket方式)APP进程</br>
启动过程：APP进程->(Binder方式)system_server进程中的AMS去attach Application->(Binder方式)App进程(ApplicationThread)->(Handler方式)ActivityThread->Activity.onCreate()
