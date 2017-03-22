# UIViewController

## 加载页面方式

### Xib/SB 加载

生命周期: 通过xib/SB实例化视图 —> 连接outlet和action —> 调用loadView方法—> 调用awakFromNib—>viewDidLoad—>视图加载完毕—> viewWillAppear—>更新布局—> 视图显示在屏幕上—> viewDidAppear

### 代码加载

生命周期: init -> loadView —> viewDidLoad —> viewWillAppear —> viewDidAppear —> viewWillDisappear —> viewDidDisappear —> viewWillUnload —>销毁视图 —>  viewDidUnload —> dealloc



## 生命周期

### Tip

1.UIViewController的view是通过懒加载的，只有调用view属性的getter方法才会创建view。

2.init中不要创建view。 而应该只做相关数据的初始化，且是关键数据，并且也不要调用view属性。

3.init方法如果重载了loadView方法，则不会从xib/SB 中载入视图。调用initWithNib重载loadView只是继承super方法，且不对view属性做set操作则会从xib/SB中载入视图。

### loadView

调用时间：访问ViewController的view属性且该属性为nil时会调用loadView。不可手动调用。比如初始化的时候或者内存紧张在viewDidUnload中将view设为nil，再次访问该页面的view时则会通过该方法重建view。

作用：负责创建UIViewController的view属性

默认实现：即super方法的实现做了哪些工作： 会先查找UIViewController相关联的文件，通过加载xib文件创建view属性(ps: 加载时指定了xib文件，则从相应xib加载，没有则从与类名相同的xib文件加载， 如果没有找到xib文件则创建空白view—> 以前是创建空白view，现在如果没有设置view则会显示黑色view；还有通过init初始化视图，且存在同名xib文件时，如果没有实现loadView则会从xib加载，但实现了loadView，不论是否执行父类方法也无法显示xib内容，移除了loadView方法就可以正常显示xib内容)。

实践： 代码写UI时，则需重写loadView方法，且不要调用super方法。当然调用了也不会报错，只是造成了一些不必要的开销。

### viewDidLoad

调用时间：view创建完毕后

作用：初始化工作，界面元素的初始化比如添加一些子视图，从数据库或网络加载数据

### viewWillAppear

 调用时间：view被添加到superview之前，切换动画之前调用

作用:做一些显示前的处理，如键盘弹出，特殊的过程动画(比如状态调，导航栏的颜色)

### viewDidAppear

调用时间：切换动画之后调用

作用：切换动画后需要执行的操作。

 

### viewDidUnload

调用时间: 内存紧张时，系统会发出内存警告，UIViewController会受到didReceiveMemoryWarning消息，该消息默认实现会将不在视图层次结构中的view，即view的superview为nil的时候，将view释放，然后调用viewDidUnload方法.

作用: 释放资源，释放界面元素相关的资源。



## VS

### viewDidUnload VS dealloc

viewDidUnload释放了view但没有释放UIViewController，而dealloc是在释放UIViewController的时候被调用。



## 参考资料

[UIView的生命周期总结](https://bestswifter.com/uiviewlifetime/)

[loadView、viewDidLoad及viewDidUnload的关系](http://www.cnblogs.com/mjios/archive/2013/02/26/2933667.html)



# UIView

### initWithCoder

调用时间: 通过xib加载对象实例后，系统会调用initWithCoder

作用： 重新设置nib中已经设置好的各项属性





