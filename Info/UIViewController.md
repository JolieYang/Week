# UIViewController

## 加载页面方式

1) StoryBoard加载:

```objective-c
[[UIStoryboard storyboardWithName:@"storyboard的名称，比如Main" bundle:nil] instantiateViewControllerWithIdentifier:@"在storyboard中设置视图控制器的StoryboardID"];
```

2) Nib加载:

```objective-c
DemoViewController *vc = [[DemoViewController alloc] initWithNibName: @"" bundle: nil];
```

3) 代码写UI:

通过在UIViewController的实现文件中实现loadView方法，创建视图层次，可以将根视图赋值给view属性。

```objective-c
- (void)loadView {
  self.view = [[CustomeView alloc] init];
}
```

## 生命周期

### 1.Xib/SB 加载

生命周期: initWithCoder或者intWithNibName:Bundle或init从归档文件(xib/SB)加载UIViewController对象 —> 连接outlet和action —> 调用loadView方法—> 调用视图的initWithCoder与awakFromNib—>viewDidLoad—>视图加载完毕—> viewWillAppear—>更新布局—> 视图显示在屏幕上—> viewDidAppear

### 2.代码加载

生命周期: init -> loadView —> viewDidLoad —> viewWillAppear —> viewDidAppear —> viewWillDisappear —> viewDidDisappear —> viewWillUnload —>销毁视图 —>  viewDidUnload —> dealloc



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

作用：初始化工作，界面元素的初始化比如添加或者移除一些子视图，从数据库或网络加载数据，设置视图的初始属性，修改一些约束

### viewWillAppear

 调用时间：view被添加到superview之前，切换动画之前调用

作用:做一些显示前的处理，如键盘弹出，特殊的过程动画(比如状态调，导航栏的颜色)，做一些比较耗时的操作



### viewWillLayoutSubviews

作用： 通知即将开始对视图进行布局

### viewDidLayoutSubviews

作用： 通知视图的布局已经完成

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

为了兼顾从文件和从代码初始化对象，需要实现initWithCoder和initWithFrame。

### xib/SB加载视图

生命周期: 在loadView之后调用，顺序为initWithCoder —> awakFromNib。

通过initWithCoder从归档文件中获取对象，但这时还没建立用户事件，通过调用awakeFromNib保证 action与outlet的建立.

awakeFromNib:Prepares the receiver for service after it has been loaded from an Interface Builder archive, or nib file.当对象从nib文件中解档之后，对每个对象建立outlet和action连接,然后发送awakeFromNib消息。必须调用父类方法，执行父类所需的初始化操作。虽然默认该方法的实现中并没有做什么事情，但是很多UIKit中的类并不是空实现。

### initWithCoder

调用时间: 控件通过xib/SB加载对象实例后，系统会调用initWithCoder

作用： 重新设置nib中已经设置好的各项属性.

Tip: 任意对象只要遵循NSCoding协议都可以通过该方法实例对象。包括UIView和UIViewController

```objective-c
- (instancetype)initWithCoder:(NSCoder *)coder
{
    self = [super initWithCoder:coder];
    if (self) {
        NSLog(@"initWithCoder");
        [self initViews];
    }
    return self;
}
```

### awakeFromNib

调用时间：initWithCoder之后调用，对象建立outlet和action连接之后.

作用: 对一些无法在设计时执行的额外配置，比如定制任意控件的默认配置满足用户的个人设置或者还原应用单个控件之前的状态

```objective-c
-(void)awakeFromNib{
    NSLog(@"awakeFromNib");
    [super awakeFromNib];
    [self initViews];    
}
```



### 代码加载视图

生命周期: initWithFrame

### initWithFrame

调用时间: 不是从xib/SB创建视图时

```objective-c
- (instancetype)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        NSLog(@"initWithFrame");
        [self initViews];
    }
    return self;
}
```



### layoutSubviews

调用时间: 由系统调用，不可手动调用，可以调用setNeedsLayout标记使得在下个刷屏循环中调用layoutSubviews或者调用layoutIfNeeded请求系统调用layoutSubviews。addSubview，视图frame发生改变，UIScrollView滚动，循转，改变transform属性这些情况下，系统会调用layoutSubviews。

作用:对视图进行布局

Tip: 





### Log

22th,March,2017— 23th,March,2017 [done] 对于initWithCoder与awakeFromNib的理解还不够。