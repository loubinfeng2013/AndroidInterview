# Android基础
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

