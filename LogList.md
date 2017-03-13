## Log List

👍  🌹 

### 基础


- [ ] 本地存储;🌹  🌹 

- [x] ## 多线程；🌹  🌹 308(2h)—>309(5h)—>313(2h)

关键字： 调度， 分派，资源共享，资源竞争，优先级反转

苹果主推GCD和NSOperationQueue。

**锁机制**：同一个资源可能被多个线程同时访问，造成资源抢夺。就会通过锁机制解决资源抢夺问题。OC中解决方法有:属性原子性，NSLock加锁解锁设置加锁代码区，@synchronized代码块；GCD中还提供了信号机制，默认信号量为1，发送一个信号通知，信号量+1，发送等待通知，-1.信号量为0处于等待状态，大于0开始执行。

**死锁**：多个线程相互等待对方结束

**NSThread**: 实例方法initWithTarget,类方法detachNewThreadSelector。NSObject也有一些线程的扩展performSelector，如果要指定线程执行performSelector的代码，则需通过runloop开启。

**GCD**： iOS4.0推出，具备Block特性，简单方便。底层是C实现的，效率高。系统按一定的策略对线程就行管理调度，大大减轻维护工作。

**操作队列NSOperationQueue**: iOS2.0推出，4.0推出GCD的时候也对OperationQueue的底层基于GCD面向对象做了重写。

相比于GCD的优势有，更易于添加任务的依赖关系，支持取消任务，可以设置并发的最大数目，可以设置操作的优先级，提供任务状态属性(具备KVO特性，随着任务状态变化，对应观察者可以收到通知)；支持面向对象，任务以对象的形式封装，可复用，同时具备封装，多态特性，有利用架构设计。

重写start与main方法。

**Runloop**

帮助调度和处理程序运行中发生的各种事件。循环监听事件源，将消息分发给线程来执行。一种异步执行代码的机制，不是线程也不是并发机制。

事件源：分为时间源与输入源，输入源分为基于端口的源，自定义源，performSelector:OnThread:delay: 

端口源：通过创建端口对象，使用NSPort的方法将端口对象加入到runloop中。

自定义输入源： 使用CFRunLoopSourceRef相关函数创建输入源。常见的触摸，滚动事件就是该类源，由系统内部实现。

时间源就是NSTimer。不准确的，RunLoop负责分发，如果当前进程正在处理繁重的任务，则可能导致Timer本次延时或者少执行一次。

优先级反转：低优先级的线程一直阻塞高优先级的线程。

**实践**： 考虑到死锁(相互等待对方结束)这种情况,dispatch_sync这个API是危险的，所以尽量不要用;考虑到优先级反转问题，最好写代码是只用Default优先级的队列，不要添加其他类型队列使问题复杂化;UI相关的一定要在主线程中处理；

**案例**：1) 通过GCD从网络中拉去数据，有一个致命的问题，遇到超时问题是，无法取消。因而推荐使用NSOperationQueue实现。


- [x] 单元测试；🌹 214D

使代码质量变的可靠。 便于之后重构或修改代码而不用担心修改破坏其他部分。从而减少软件开发的时间。

Xcode集成了对测试的支持,其中单元测试使用的是XCTest框架 


- [x] ## 内存管理；🌹 🌹  🌹 307（2.3h)—>308(2h)

**引用计数**： OC中的堆对象都是通过引用计数来保留和释放的。创建对象时，引用计数为1，每增加一个强引用，引用计数加1，弱引用则不改变引用计数。每释放一个强引用的指针(release)，引用计数减1.但引用计数为0时，对象内存释放。

**内存分布**： 指令/代码， 初始化数据，未初始化数据，堆，栈。

**iOS内存机制特点**：1) 物理内存有限， 5到6+都是1G， 6s到7为2G，7plus为3G；崩溃内存占内存的60多。2)内存紧张时系统会发送广播通知，如果内存低于崩溃内存，就会杀死优先级较低的进程。3）没有内存交换机制，也就是不会像桌面系统一样在内存紧张时将物理内存置换到闪存中。4）使用虚拟内存机制，做虚拟存储到物理内存的映射管理，采用分页存取机制，4k为一页，page状态分为Nonresident,Resident and clear, Resident and dirty。clean memory会在内存紧张时被系统unload(也就是可以直接清理)，需要时再重新从闪存加载到内存中。dirty memory则无法卸载，需要通过应用自己去管理维护，如果进程创建了大量dirty memory且内存紧张，会发起memory warning,应用没有释放足够内存就会被kill。

**对策**： 1) 尽可能减少dirty内存的创建;2) 尽量保证dirty内存使用后及时释放；3) 及时处理系统内存警告通知，释放占用大量内存并可以重建的对象；4) 发生内存警告时，不再持续申请内存特别时较大块的内存。

**MRC**:iOS5之前使用手动引用计数机制，alloc将引用计数设为1，retain或者copy引用计数加1，release减1， autorelease则表明对象作用域超出声明范围，并不会立即释放，而是在自动释放池(autoreleasepool)清空后再释放这个对象。dealloc则可以清理引用计数大于0的对象。但无法清理自动释放池中引用计数大为0的对象。

**ARC机制**：WWDC2011提出的用于管理内存的技术。编译器会在编译时在适当的地方自动插入包括retain,release,copy,autorelease,autoreleasepool。除了要避免循环引用，其他内存处理编译器已经帮我们做了。

考虑下循环引用问题，比如代理属性为什么要设置为weak，是如何导致循环引用的?


- [ ] 动画；🌹 
- [ ] 转场；🌹 
- [ ] 安全之沙盒机制；
- [ ] 网络请求313(2h)

**HTTP**:无状态高并发。缺陷就是存在以下三种风险: 1) 截取窃听;2)篡改风险;3)冒充风险(第三方冒充他人身份参与通信).

**HTTPS**:基于HTTP协议实现了SSL协议。

**TLS/SSL协议**: 基本流程：1)客户端发送随机数a,SSL协议版本号以及客户端支持的加密方法。2) 服务器端确认双方使用的加密方法，通过数字证书发送公钥，并发送随机数b给客户端。3)客户端检验证书有效性，并生成新的随机数c，通过公钥加密c发送给服务器端.4)服务器端通过私钥获取随机数c。5) 双方通过三个随机数生成对话密钥，用于加密接下来对话过程传输的信息。(ps: 设计对话密钥的原因是公钥加密计算量太大，而对话密钥是对称加密运算速度快，且服务器公钥只用于加密“对话密钥”本身，减少了加密运算的消耗时间)

WebSocket:

网络安全：保护用户隐私进行风险评估和设计安全对策是应用开发者的责任。

**ATS**: iOS9.0开始，引入了新特性ATS，默认情况下所有的网络链接使用HTTPS进行传输。WWDC16时，苹果表示2017年1月1日会要求所有应用不可通过NSAllowsArbitrayLoad绕过ATS限制，但在2016年12月底苹果表示推迟这一时间，并且没有给出明确限制的时间,不过感觉WWDC17上应该会有所回应。

### 对象模型与消息传递

- [x] 对象模型;🌹 🌹  🌹 307(1h)


数据结构。 

OC的对象是一个包含isa指针的数据结构，isa指针类型为Class,Class是一个名为objc_class的数据结构定义。

```objective-c
typedef struct objec_class *Class;
typedef struce objc_object {
  Class isa;
} *id;
```

objc_class也是一个包含isa指针的结构体，

```objective-c
typedef struct objc_class {
  Class isa;	
}
```

对象是类的实例，类也是一个对象，是元类的实例。元类的isa指针全都指向根元类。根元类的isa指针指向自己，从而在设计上实现了一个闭环，实现所有事物都是对象，都有isa指针。

类中保存的方法是实例方法，元类中存放的是类方法。

对象在内存中的分布是一个结构体，结构体的大小并不能动态的变化，其中成员变量是一个名为ivars的指针，方法则是一个名为methodLists的指针的指针。因而，可以通过修改指针的指针的值动态地为类增加成员方法，但无法给对象增加成员变量。

- [x] runtime运行时;🌹🌹 🌹 222(3h)—>227(1.5h)—>228(1.3h)


objc_object对象, objc_class类, objc_method方法。

OC面向对象的特性主要时通过由C和汇编语言实现的一个运行时系统实现，这个运行时系统就是一个动态链接库—libobjc.A.dylib(源代码是开源的)。这个动态库提供了OC所需的各种动态特性，包括支持面向对象的一些特性。 

OC运行时系统有两个版本: 早期(legacy)版本和现行(Modern)版本。两者最大的区别是修改类实例变量的布局时，早期版本需要重新编译子类，现行版本不需要。

Runtime的核心：消息传递。

- [x] 消息传递; 🌹 🌹 🌹221(1h)—>222(1.3h)

消息的执行会用到一些编译器为实现动态语言而创建的数据结构和函数。objc_msgSend(id receiver, SEL selector,…)，id指向实例的指针，SEL方法选择器，可区分方法的ID。

Messaging是运行时通过SEL查找IMP的过程，通过函数指针就可以执行对应的实现。 （SEL：发送消息时标识消息， IMP：消息实现。 通过消息的分派机制将SEL和IMP关联起来)

Message Forwarding消息转发是查找IMP失败后通过转发执行对应的实现。

OC是一个动态语言，提供运行时系统支持动态的创建类和对象以及进行消息传递和转发工作。不像面向过程语言，调用方法就是简单的执行对应代码片段，而是推迟到运行时发送消息，如[object foo]，消息发送的分派流程是:

1) 通过对象object的isa指针获取objc_class；

2）在类的method_list中查找foo方法;

3)  如果类中没有查到foo方法，则去父类中查找；

4） 找到foo这个函数，就去执行对应的实现IMP。

ps:  objc_class中有cache属性，在类中查找foo方法时，实际上会先从cache中查找，没有再进入流程2,找到后会将方法名foo作为key,对应实现作为value存入cache中，下次发送foo消息，则可在cache中找到，避免遍历objc_method_list。

如果无法找到foo方法，并没有结束，而是会进入消息转发阶段:

1) 动态方法解析： 消息接收者object动态添加方法响应这个消息(+resolveInstanceMethod: 或+resolveClassMethod)或者转发给其他对象处理(-forwardingTargetForSelector:)。

2) 完整的消息转发机制： 通过调用Invocation对象处理，创建NSInvocation对象存放消息有关的所有细节，调用NSInvocation对象让消息分发系统将消息派发给指定的对象。（-forwardInvocation:）

Method swizzling: 方法交换。也就是更改SEL和IMP的对应关系。

- [x] Block;🌹 🌹 🌹214D(1h) —> 215D(5h) —> 216(4h)

使用场景： 代替Delegate实现跨页面传值。任务完成，消息监听，错误等回调。

Block在定义的地方，并不会执行Block内部的代码.

`block当初是为了解决什么样的问题而设计的？你如何区分何时使用block，何时不使用？`

iOS4.0+引进的对C语言的扩展，用来实现匿名函数的特性。

- [ ] NSNotification；🌹 🌹221(0.5h)
- [ ] 代理模式;🌹🌹🌹220(2.5h)—>221(0.5h)
- [ ] KVO;221(0.5h)
- [ ] Category;228(1h)—> 310(2.5h)


无需继承动态的给已有类添加方法;

**原理**: 对象模型的存储在内存中是一个结构体，结构体的大小不能动态变化，因而无法在运行时动态地给对象添加成员变量。而方法的定义保存在类的可变区域中，通过修改执行方法的指针的指针的值，可以动态的添加成员方法。

**使用场景**：

1. 根据功能相关性将类的实现拆分到多个文件中。好处：1)减少单个文件的体积；2) 便于代码组织； 3) 便于团队多成员协同开发; 4)可按需加载Category。
2. 私有方法前向引用。Cocoa中没有真正的私有方法，只要知道对象支持的方法名称，即使对象所在的类接口没有声明该方法，也可以调用这个方法。不过当然不能直接调用(编译器会报错)，因而需要新建该对象的类别，在类别的头文件中添加该方法的声明。即可调用私有方法。

**添加成员变量**: 

其实这里的类别特指OC中的Category，Category中无法声明属性，

```objective-c
// .h
@interface UIImage(UIImageExt)
@property (nonatomic, strong) NSString *imageUrl;
@end
// .m
#import <objc/runtime.h>
@implementation NSString (NSStringCategory)
static const void *imageUrlKey = &imageUrlKey;
// static NSString *imageUrlKey = @"imageUrlKey"; 
@dynamic imageUrl;
- (NSString *)imageUrl {
    return objc_getAssociatedObject(self, imageUrlKey);
}
- (void)setImageUrl:(NSString *)imageUrl {
    objc_setAssociatedObject(self, imageUrlKey, imageUrl, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}
@end
```

Swift中的实现

```swift
//  错误版本1:遮脸，没写出来
extension UIImage {
    var imageUrl: String {
        get {
            return String(describing: self.scale)
        }
        set {
            self.imageUrl = newValue
        }
    }
}
// 错误版本
extension UIImage {
    var imageUrl: String {
        get {
            return objc_getAssociatedObject(self, &imageUrlKey) as! String
        }
        set {
            objc_setAssociatedObject(self, &imageUrlKey , newValue, objc_AssociationPolicy(rawValue: 0)!)
        }
    }
}
```

我在思考是不是playgorund中并没有运行循环，所以显示的值是nil，但不代表我的写法是错的。

**Category VS Extension**: 

刚接触的时候总会接触到Extension是匿名的Category的观念，因而就默默的将Extension当成特殊的Category。但其实两者完全是两个东西。Extension是在编译阶段与头文件和实现文件共同构成一个类，声明周期跟类一样。通常是用来隐藏类的私有信息。且无法为系统类添加Extension。

而Category则是在运行时动态的已有类添加方法，

- [x] Target-Action;🌹221(0.5h)


主要用于发送消息响应用户界面的事件，可以说是UI中消息传递的最佳选择。 发送者与接收者之间耦合度比较低，双方互不知道。一个控件可以和多个Target-action关联，如果target为nil，则action会在响应链中查找知道找到一个响应它的对象。局限在于发送消息时无法携带自定义的数据，Mac上只能传递发送者，iOS上可以选择传递发送者或者事件类型。

### 基础控件

- [ ] UITableView;🌹 🌹 
- [ ] UICollectionView;🌹 
- [ ] UIScrollView；🌹 
- [x] UINavigationController;🌹 🌹 302(1.7h)—>306(1.5h)


导航控制器由viewControllers数组，导航栏(navigationBar), 可选的工具栏(toolbar), 代理(设置代理可以协调处理相应事件，比如转场)组成。

导航栏： 左边按钮(leftBarButtonItem/backBarButtonItem)，中间标题(title/titleView),右边按钮(rightBarButtonItem)，标题上面标签Promt(prompt)30像素的高度。

视图控制器栈

导航栈：  UINavigationBar与UINavigationItem。

重叠； tintColor传递性。

- [ ] 远程推送;🌹 214N
- [ ] 混编UIWebView;🌹 🌹 


### 框架

- [ ] Wallet;
- [ ] AVFoundation二维码扫描;🌹 🌹 313(1h)

**sessionPreset识别小图二维码**:通过设置AVCaptureSession的sessionPreset属性设置图像每一帧的大小，设的高代表能快速扫描小图。一般AVCaptureSessionPreset640 * 480就够用，要识别小图就设高一些，如AVCatureSessionPreset1920 * 1080。

**rectOfInterest设置识别区域**: 识别区域默认是整张图片，但可以通过rectOfInterest设置识别区域，提高扫描性能和速度。该属性是CGRect，所以不看文档的话会以为是通过设置(x,y,width,height)，但文档中指出默认是(top, left, height, width),其中所有的值范围为 0.0 - 1.0，因而是按比例设值。（ps：  猜想是AVCapture输出的图片是横着的，iPhone的屏幕是竖着的，因而需要将比例旋转90度）。

**一维码扫描问题**: 

- [ ] Photos图片框架;
- [x] 蓝牙;🌹 213N —> 214D(1.8h)

BLE(Bluetooth Low Energy)蓝牙低能耗技术

CoreBluetooth.framework(支持iOS6及以上版本) ：基于蓝牙4.0的蓝牙开发框架。 不像以前的GameKit和MultipeerConnectivity框架局限于iOS设备间的传输，支持跨设备(Android, Windows Phone)进行传输。

蓝牙通信过程中两个核心角色,有点像客户端(中心设备)-服务器模式(外围设备):

 1.中心设备(CBCentralManager),使用数据，扫描外围设备后建立连接，连接成功后可以使用外围设备的服务和特征。

1. 外围设备(CBPeripheralManager)，发布服务，生成与保存数据。

两者之间交互的桥梁是服务和特征，通过唯一标识UUID唯一确定一个服务。特殊的服务使用16位的UUID，如果硬件所做的事情不在这个列表，可使用自定义的UUID(要求必须是128位的)

一个设备包含一个或多个服务，一个服务包含若干个特征。

流程：

【蓝牙中心模式】比如金融刷卡器，通过蓝牙与手机通讯。手机端的应用通过蓝牙发送不同指令控制刷卡器执行一些动作。比如读磁条卡等。

1.创建中心设备对象(CBCentralManager)并指定代理;

2.扫描外围设备，发现外围设备连接并保存；

3.查找外围设备服务和特征，查找到可用特征就读取数据。

支持与外设做数据交互与订阅特征(Characteristic)的通知

【蓝牙外设模式】

1.创建外围设备对象(CBPeripheralManager)并指定代理；

2.创建特征与服务,描述与权限并添加到外围设备中；

3.外围设备开启广播服务;

4.和中央设备(CBCentral)进行交互(设置处理订阅，取消订阅，读characteristic，写characteristic的委托方法);

ps:不论创建外设对象还是中心对象，指定代理时都会检测蓝牙是否已经打开。

电量考虑： 苹果建议外围设备在前30s每20ms发送一次广播报，30s后，使用645ms,768ms,1065ms,1394ms可增加被iOS设备发现的机会。


### 项目

- [ ] 配置文件解析；🌹 
- [ ] 工厂模式；🌹 
- [ ] 字典与JSON,XML互相转换;🌹 
- [ ] CocoaPods;🌹 
- [x] TestFlight;🌹 220(0.5h)

内测分发工具，同类型比较常用的还有： 蒲公英。

分为内部成员(25)跟外部成员(1000)。 主要是在iTunes Connect上操作,创建App Record，通过后测试者会收到对应的邮件，通过邮件打开TestFlight应用进行安装测试。而蒲公英只需要链接即可，易于在微信等平台传播。

TestFlight只支持iOS平台，而像蒲公英支持安卓，iOS平台。所以后面公司该用蒲公英作为分发工具。且使用苹果原生的分发工具，还需跟TestFlight绑定，测试者需安装TestFlight，流程实际上没有蒲公英来的简单明了，对于测试者不是很友好。而TestFlight唯一的优势可能是99美元账号就可以进行分发，支持1000的设备，而蒲公英分发工具则指支持100的安装设备。

还有TestFlight服务器在国外，如果外部测试成员需要苹果对应用进行审核后才可以进行测试。

- [ ] 自定义键盘;🌹 🌹 
- [ ] 支付宝API与签名;🌹 
- [ ] 仿支付宝添加常用功能动画；🌹 🌹 

### 第三方库

- [ ] 布局Masonry;🌹 
- [ ] AFNetworking网络请求;🌹 🌹 
- [ ] WebSocket,SocketRocket;🌹 🌹 
- [ ] RAC；🌹 
- [x] JSONModel；🌹 213N —> 214D (1h)


目的： 更为优雅的将JSON数据转换为对象(Model)，无需手动从字典中获取Model的每个属性。

原理： 基于OC Runtime的反射机制。 反射机制实现类属性获取过程。 

反射机制是什么？ 程序在运行期间动态获取类信息以及能调用任意一个对象的属性和方法的功能称为反射机制。 这种动态获取类信息与动态调用对象的方法的功能称为反射机制。

YYModel: YYModel的作者郭耀源有对常用的几个JSON转换框架做性能对比，YYModel的性能是最好的。 以及YYModel是使用类别(Category)的方式实现，Model无需继承。YYModel做了哪些性能优化呢？ 看了下马在路上的博客分析是使用了C与CoreFoundation。


### 扩展

- [x] 如何提高iOS开发技能;🌹 🌹 220(0.5h)

1. 看视频： 慕课网，极客学院，网易公开课。但这些主要是在入门的时候，现在基本没怎么看视频。 但打算之后英语水平提高了以后，可以直接看WWDC视频。
2. 阅读博客： 一开始看的比较杂，慢慢的就累积了一些写的比较好的iOS开发者(如王巍，唐巧，崔江涛，李忠，田伟宇，bang，孙源，臧成威)与网站(Raywenderlich,ObjC中国)。资讯以前有段时间会看，但满满的也就没有看，基本上都是按自己兴趣节奏会选定一个专题，然后专门找相关资料看一个点比较多。
3. 书: 看的比较少，看完的就两本，唐巧的《iOS开发进阶》 与一本讲如何写简洁代码的书《代码整洁之道》 。
4. 苹果官方文档： 从Mac项目开始，养成查看文档的习惯
5. 开源项目代码： 有尝试看过AFNetworking的源代码，但还无法真正体会其中的很多东西。目前道行不行的原因吧
6. 多写多思考，实践总结： 自己在学习的时候都会配着写Demo，也有自己做小应用。

- [ ] 简洁优雅代码;🌹 🌹 🌹 


- [ ] Git;🌹 🌹 
- [ ] SVN;🌹 🌹 

