# 四大组件

# Activity       
`Activity`是一个应用向外界展示信息所必须的一个组件。
## 生命周期     
经典的生命周期图        

![activity_lifecycle.png](/images/activity.png)       

关于各个周期：      

**onCreate()**      
`Activity`被创建时首先调用。        
**onStart()**       
`Activity`界面出现时。        
**onResume()**      
`Activity`被完全显示，可以与用户交互时。        
**onPause()**       
`Activity`失去焦点但仍对用户可见，如显示了一个弹窗。        
**onStop()**        
`Activity`被其他`Activity`完全遮挡，对用户不可见，如跳转到新的`Activity`或者退出程序，直接锁屏。        
**onDestory()**     
 `Activity`被销毁时调用。       
**onRestart()**     
`Activity`对象再次被启动时调用。        

- **正常启动生命周期**      
```onCreate()->onStart()->onResume()->activity runing->onPause()->onStop()->onDestory()```      
- **被遮挡但可见不可操作又取消遮挡**        
```onPause()->activity stop->onResume()->activity runing```     
- **不可见变为可见**        
```onPause()->onStop()->activity stop->onRestart()->onStart()->onResume()->activity runing```       

[Activity示例代码](https://github.com/Chenlei534/AndroidLearnCode/blob/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_activity/AboutActivity.java)        


## 启动模式     

- **standard**      
标准模式。      
`Activity`默认的启动模式，`Activity`每次启动都会在栈顶创建一个新的`Activity`实例。      
如果`Activity`已经位于栈顶，则`Activity`启动时也将会重新创建新的实例。    

- **singleTop**     
栈顶复用模式。      
`Activity`启动时判断其是否在栈顶，如果在栈顶则直接复用，并且回调onNewIntent()方法，不会再重新创建新的`Activity`实例。       
如果未处于栈顶则重新创建新的`Activity`实例。        

- **singleTask**        
栈内复用模式。      
1.`Activity`被首次创建时首先检测AndroidManifest.xml中相应`<activity />`节点下的`android:taskAffinity="..."`属性是否存在，如果不存在则在启动该`Activity`的`Activity`所在的任务栈上新建该`Activity`，如果属性值存在并与某一任务栈相同则在该任务栈中新建该`Activity`，如果属性名与已存在的任务栈都不相同则新建与属性名相同的任务栈并新建该`Activity`。     
2.`Activity`非首次创建启动时检测活动栈中是否已存在`Activity`实例，如果存在则直接复用并将其之上的所有`Activity`实例全部出栈，并且回调`onNewIntent()`方法，使该`Activity`处于栈顶的位置，如果不存在则在栈顶创建新的Activity实例。     

- **singleInstance**        
单实例模式。        
该模式下`Activity`会启动一个新的任务栈来管理`Activity`实例，当从其他任务栈中启动该`Activity`时，该`Activity`所在的任务栈，将会转移到前台。      
可以在不同的程序中共享一个`Activity`        

### 异常状态        
当系统资源配置发生改变或者内存不足时`Activity`会被杀死。        

- **系统配置发生改变**      
系统配置发生改变`Activity`会被杀死重建，经典场景就是**横竖屏切换**的时候。      
此时的生命周期状态转换：        
```onSaveInstanceState()->onStop()->onDestory()->onCreate()->onStart()->onRestoreInstanceState()->onResume()->activity runing```        
这里出现了两个方法      
1.onSaveInstanceState()     
当Activity被异常状态下终止时会调用该方法来将`Activity`终止前的相关状态保存在Bundle中用来重建时恢复状态      
2.onRestoreInstanceState()      
当Activity被在异常状态下重建时回调该方法，该方法中的Bundle里保存了异常终止前Activity相关状态。      
此时可以通过在AndroidManifestl.xml文件中相应的activity节点下配置```android:configChanges="orientation|keyboardHidden"```属性来禁止相应的配置改变时activity的销毁重建。但是此时会回调Activity中的        ```onConfigurationChanged()```方法。  

- **系统内存不足**      
当系统内存不足时，系统将会根据activity优先级的高低，优先杀死优先级低的```Activity```。      
1.此时的生命周期：      
```activity runing->onPause()->onStop()->onDestory()```     
2.优先级：      
前台```Activity```>可见非前台```Activity```>后台```Activity```      

[上文代码](https://github.com/Chenlei534/AndroidLearnCode/tree/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_activity)       

----------      

# Service        
`Service` 是一个可以在后台执行长时间运行操作而不提供用户界面的应用组件。服务可由其他应用组件启动，而且即使用户切换到其他应用，服务仍将在后台继续运行。 此外，组件可以绑定到服务，以与之进行交互，甚至是执行进程间通信 (IPC)。 例如，服务可以处理网络事务、播放音乐，执行文件 I/O 或与内容提供程序交互，而所有这一切均可在后台进行。     

## 生命周期

![service_lifecycle.png](/images/serve.png)               

由上图可知`Service`有两种生命周期，分别对应着`Service`的两种形式        

## Service形式      

### 启动形式        

如果应用组件通过`startService()`方法来启动服务时，该服务即处于**启动状态**。        

服务处于启动状态后将开始在后台无期限运行，组件被销毁也不会影响服务的运行状态。并且启动形式的服务与组件之间没有很大的关联并不会将服务结果返回给组件。如通过启动一个服务来下载网络资源。      

启动的服务可以在组件中通过`stopService()`或者在服务内部通过`stopSelf()`方法来停止服务。     

**onCreate()**      
服务首次被创建时，系统将会调用该方法来进行相关设置。该方法只有在服务被创建时调用，如果服务已在运行，则该方法不会调用。
**onStartCommand()**        
组件通过调用`startService()`方法启动服务时，该方法将会被调用。该方法被调用后服务将会启动并将在后台无限期运行，并且需要调用`stopService()`或者`stopSelf()`方法来停止服务。每次调用`startService()`方法都会该方法都将会被调用。在绑定状态下不需要实现这个方法     
**onDestory()**     
当服务不再使用并将被销毁时，系统将调用该方法。来清理所有的资源，如线程、监听器、接收器等。
**onBind()**               
无论启动服务还是绑定服务该方法必须实现。如果组件通过`bindSercie()`方法绑定服务，则系统将调用该方法，该方法的最后需要一个`IBinder`接口的实现类，用来供组件与服务进行通信。如果组件通过`startService()`方法来启动服务则该方法返回`null`。     

- **启动形式生命周期**      
`onCreate()->onStartCommand()->Service runing->onDestroy()`     

[启动形式服务示例代码](https://github.com/Chenlei534/AndroidLearnCode/blob/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_service/StartService.java)      

### 绑定形式   

如果应用组件通过`bindService()`方法绑定到服务时，该服务即处于**绑定状态**。组件与服务绑定后，绑定服务提供了一个**客户端-服务器**接口，允许组件与服务进行交互、发送请求、获取结果，甚至是利用进程间通信（IPC）跨进程执行这些操作。通俗来讲就是绑定服务后组件和服务就可以进行方法调用，增强两者之间的操作性。     

组件与服务绑定后，服务开始运行，一个服务可以绑定多个组件，当绑定形式的服务与任何一个组件都失去绑定后该服务会被销毁。        
绑定的服务可以在组件中通过`stopService()`或者在服务内部通过`stopSelf()`方法来停止服务。     

**onCreate()**      
服务首次被创建时，系统将会调用该方法来进行相关设置。该方法只有在服务被创建时调用，如果服务已在运行，则该方法不会调用。      
**onBind()**        
无论启动服务还是绑定服务该方法必须实现。如果组件通过`bindSercie()`方法绑定服务，则系统将调用该方法，该方法的最后需要一个`IBinder`接口的实现类，用来供组件与服务进行通信。如果组件通过`startService()`方法来启动服务则该方法返回`null`。     
**onUnbind()**      
绑定状态的服务被销毁时调用      
**onDestory()**     
当服务不再使用并将被销毁时，系统将调用该方法。来清理所有的资源，如线程、监听器、接收器等。      

- **绑定形式生命周期**      
`onCreate()->onBind()->Service runing->onUnbind()->onDestroy()`          
#### 1、扩展Binder类实现绑定服务        

[扩展Binder类绑定服务示例代码](https://github.com/Chenlei534/AndroidLearnCode/blob/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_service/BindService.java)

#### 2、使用AIDL来实现绑定服务      

[使用AIDL来绑定服务示例代码](https://github.com/Chenlei534/AndroidLearnCode/blob/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_service/BindRemoteService.java)       

### 启动服务与绑定服务之间的转换问题        

一个服务可以同时是启动状态和绑定状态。但是系统只会为一个Service创建一个实例对象，所以启动服务和绑定服务操作的都是同一个Service实例，但是由于两者的执行顺序不同将会出现下列两种情况：        
- **先绑定服务后启动服务**      
`状态变化：绑定服务模式->启动服务模式`      
绑定服务将会转换为启动服务运行，此时如果之前绑定服务的组件被销毁也不影响服务的运行。服务将会按照启动服务的模式运行。        
- **先启动服务后绑定服务**      
 `状态变化：启动服务模式`       
服务不会转换为绑定服务，仍然以启动服务模式运行。        
但是还是会与组件绑定，当与组件解除绑定之后，对服务无影响。      
### 服务使用注意事项     

注意：服务在其托管进程的主线程中运行，它既不创建自己的线程，也不在单独的进程中运行（除非另行指定）。 这意味着，如果服务将执行任何 CPU 密集型工作或阻止性操作（例如 MP3 播放或联网），则应在服务内创建新线程来完成这项工作。通过使用单独的线程，可以降低发生“应用无响应”(ANR) 错误的风险，而应用的主线程仍可继续专注于运行用户与 Activity 之间的交互。       

由此可知服务是托管在进程的主线程中运行，所以如果服务要进行耗时或者阻塞性操作，则应该在服务内创建新的线程来完成工作。        

- **建议使用：IntentService**       
`IntentService`是继承并处理异步请求的一个类，在`IntentService`内有一个工作线程来处理耗时操作        

### 保证服务不被杀死        

- **因内存资源不足被杀死**      

可将`onStartCommand() `方法的返回值设为`START_STICKY`或`START_REDELIVER_INTENT `，该值表示服务在内存资源紧张时被杀死后，在内存资源足够时再恢复。        
也可将`Service`设置为前台服务，这样就有比较高的优先级，在内存资源紧张时也不会被杀掉。       

- **用户通过 settings -> Apps -> Running -> Stop 方式杀死Service**      
该方法会执行`Service`的生命周期将会调用`onDestory`，所以可以在`onDestory()`中发送广播重新启动`Service`。        
也可以开启两个服务，相互监听，相互启动。

### onStartCommand返回值        
- **START_STICKY**      
返回此值时`Service`被系统kill后，系统将会尝试重新创建`Service`，并调用`onStartCommand()`方法，如果在此期间没有新的命令被传递到`Service`则参数`Intent`为`null`       
- **START_NOTE_STICKY**     
返回值为该值时，如果`Service`被系统kill则系统不会自动重启该服务     
- **START_REDELIVEER_INTENT**       
返回此值时，如果`Service`被系统kill杀死，系统将自动重启该服务，并重新传递`Intent`       
- **START_STICKY_COMPATIBILITY**        
`START_STICKY`的兼容版本并不保证kill后一定能重启        
`系统默认返回START_STICKY`      

[上文代码](https://github.com/Chenlei534/AndroidLearnCode/tree/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_service)

---

# BroadcastReceiver

Android应用可以从Android系统和其他Android应用发送或接收广播消息，类似于 [发布 - 订阅](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) 设计模式。当感兴趣的事件发生时，发送这些广播。例如，Android系统在发生各种系统事件时发送广播，例如系统启动或设备开始充电时。例如，应用程序还可以发送自定义广播，以通知其他应用程序他们可能感兴趣的内容（例如，已下载了一些新数据）。      

## 什么是广播       

广播可以用来在：APP内部、不同APP之间、APP与Android系统之间传递消息。        
## 广播应用场景         

通过上述理解我们可以总结一下广播的常用的**应用场景**：      
- **应用内外不同组件的通信**        
- **多线程通信**        
- **与Android系统在特定情况下的通信**       
等      

## 广播使用     

### 广播角色        

使用广播来进行消息传递，如果需要传递一个消息，那么最少需要一个消息的发送者，一个消息的接收者。广播进行消息的传递也是使用了**广播发送**和**广播接收**两个角色        

#### 广播接收       

为了方便理解先说广播接收，即如何接收其他广播发送来的消息。广播的接收使用`BroadcastReceiver`类，要实现一个广播接收者需要两个步骤     
**1、继承`BroadcastReceiver`并重写`onReceive()`方法**       
 ```
public class MyBroadcastReceiver extends BroadcastReceiver {
    /**
     * 重写该方法，当接收到注册的相应广播后会执行该方法
     * @param context
     * @param intent
     */
    @Override
    public void onReceive(Context context, Intent intent) {
        Log.e("接收到一个广播");
    }
}
```

[广播接收者示例代码](https://github.com/Chenlei534/AndroidLearnCode/blob/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_broadcastreceiver/DynamicRegisterReceiver.java)       

**2、注册广播接收者**       
广播接收的注册分为两种方式。        
- **静态注册**      
静态注册的广播为常驻广播，即不会受到任何组件生命周期的影响。如果需要时刻监听某广播则需要静态注册，如监听手机开机、锁屏等操作。在App首次启动时，系统会自动实例化继承自`BroadcastReceiver`的类并注册到系统中。        
优点：应用程序关闭后，程序依旧会被系统调用。        
缺点：耗电占用内存。        
使用：在AndroidManifest.xml中通过<receive>标签声明。        
```
<receiver android:name="com.example.MyBroadcastReceiver">
    <intent-filter>
        <!-- 接收的广播类型 -->
        <action android:name="MY_STATIC_BROADCAST" />
        <action android:name="Intent.ACTION_SCREEN_OFF" />
    </intent-filter>
</receiver>1
```     
`注意：Android8.0禁止了大多数静态注册`      

**动态注册**        
不常驻广播，跟随组件生命周期变化，在特定时刻监听广播可以使用动态注册。      
使用：在代码中调用Context.registerReceiver()方法进行注册        
```
//动态注册广播
myBroadcastReceiver=new MyBroadcastReceiver(); //实例化广播接收者和IntentFilter
IntentFilter intentFilter=new IntentFilter();
intentFilter.addAction(RECEIVER_ACTION);    //设置接收广播类型
intentFilter.addAction(Intent.ACTION_SCREEN_OFF);   //可以接收多个广播类型
registerReceiver(mDynamicRegisterReceiver,intentFilter);    //注册广播
```     
`注意：动态广播最好在onResume()中注册onPause()中注销，因为onPause()方法在app死亡之前一定会被执行。其他则不一定。动态广播注册后必须注销，否则将导致内存泄漏。不允许重复注册或者重复注销。`       

[动态注册代码示例](https://github.com/Chenlei534/AndroidLearnCode/blob/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_broadcastreceiver/BroadcastReceiverActivity.java)

#### 广播发送           

广播发送根据所发送的**消息类型**以及**发送方式**分为了以下几种广播类型      
- **普通广播**      

开发者自定义的intent广播，广播发送后如果广播接收中注册的intentFilter的action与发送的intent相匹配，则会接收此广播并回调onReceive()。     
使用：      
```
//发送动态广播
Intent intent=new Intent();
intent.setAction(RECEIVER_ACTION);
sendBroadcast(intent);
```     
`注意：如果发送广播需要某一权限接收广播则也需要相应权限。`      

- **系统广播**      
Android内值得多个系统广播，手机上的基本操作都会发出相应的广播。     
如：开关机，充电，电池状态，解锁关闭屏幕等。        
`注意：使用系统广播只需要注册广播接收即可，广播由系统在相应的时刻自动发送。`        
- **有序广播**          
发送出去的广播被广播接受者按照先后顺序接收。        
1、广播接收顺序规则：       
按照`Priority`属性值从大到小排序。      
`Priority`属性相同者，动态注册广播优先。        
2、有序广播特点：       
接收广播按顺序接收。        
先接受广播的广播接收者可以对广播进行截断，后续的广播接收者将不会再收到此广播。      
先接受广播的广播接收者可以对广播进行修改，后续的广播接收者收到的是被修改过的广播。      
3、如何发送有序广播：       
将`sendBroadcast(intent)`换成`sendOrderdBroadcast(intent)；`        
- **APP应用内广播**     
1. 全局广播导致的问题       
因为`Android`中的广播可以跨App直接通信，所以可能会导致其他App不断的发出针对性的广播导致当前App不断接收广播并处理，或者有其他App注册与当前App一致的`intent-filter`接收广播，获取广播具体信息，导致出现**安全性**与**效率**问题。使用App应用内广播可以解决上述问题。      
2. 应用内广播的含义     
应用内广播的广播接收发送都属于同一个app，与全局广播相比应用内广播**安全性**和**效率**更高。     
3. 应用内广播的使用     
3.1 将全局广播设置成局部广播       
注册广播时将`exported`属性设置为`false`（非本APP内部发出的此广播不被接收）。             
广播发送和接收时增加`permission`权限，进行权限验证。        
广播发送时通过`intent.setPackage(packageName)`指定广播接收器所在的包名，则该广播只会发送到App内与之相匹配的广播接收器中。       
3.2 使用封装好的`LocalBrodcastManager`类       
注册/取消广播接收器和广播发送器时将参数`context`变为`LocalBroadcastManager`的单一实例。
使用`LocalBroadcastManager`方式发送的应用内广播只能通过`LocalBroadcastManager`动态注册。
```     
//注册本地广播  
mDynamicRegisterReceiver=new DynamicRegisterReceiver();              
mLocalBroadcastManager=LocalBroadcastManager.getInstance(BroadcastReceiverActivity.this);
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction(RECEIVER_ACTION);              
mLocalBroadcastManager.registerReceiver(mDynamicRegisterReceiver,intentFilter);
//发送本地广播
Intent intent=new Intent();
intent.setAction(RECEIVER_ACTION);
mLocalBroadcastManager.sendBroadcast(intent);
```     

- **粘性广播**	        

在Android5.0，API21中已失效。       

## 广播原理     
广播接收者通过`Binder`机制在`AMS`注册，     
广播发送者通过`Binder`机制向`AMS`发送广播，     
`AMS`根据广播发送者要求，在已注册列表中，依据`IntentFilter/Permission`寻找合适的广播接收者      
`AMS`将广播发送到合适的广播接收者相应的消息循环队列中       
广播接受者通过消息队列拿到广播，并回调`onReceive()`方法。       
广播接收和注册异步执行。        

## 注意事项     
- **不同注册方式的广播接收器回调`onReceive`中的`context`返回值不同**        
静态注册：      
返回`ReceiverRestrictedContext`。       
全局广播的动态注册：        
返回`Activity Context`。        
应用内广播`LocalBroadcastManager`的动态注册：       
返回`Application Context`。     
应用内广播非`LocalBroadcastManager`的动态注册：     
返回`Activity Context`。        

- **Andorid 8.0禁止使用大部分广播的静态注册**       

[上文代码](https://github.com/Chenlei534/AndroidLearnCode/tree/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_broadcastreceiver)

----------

# ContentProvider     

内容提供程序管理对结构化数据集的访问。它们封装数据，并提供用于定义数据安全性的机制。        
内容提供程序是连接一个进程中的数据与另一个进程中运行的代码的标准界面。     

## 什么是ContentProvider        

`ContentProvider`内容提供者，在`Android`四大组件的日常使用中出现次数相对较少，但是重要性一点也没有减少，由官方解释我们可以知道它是用来**统一管理数据**进行**数据共享**并且可以进行**跨进程通信**。      

- **使用场景**      
如：我们1使用`ContentResolver`在`app`中得到手机通讯录的信息或者通过相应的`uri`访问其他应用的`ContentProvider`提供的信息等。     
## ContentProvider的使用    

上面说道`ContentProvider`提供统一的数据管理和数据共享、进行跨进程通信并举了相关例子，接下来详细说明一下。       

- **统一数据管理、数据共享**        
我们平常进行对数据操作的时候由于数据的不同类型有了许多不同的组织和使用方式，如对文件的存储和数据库的使用，便截然不同，而`ContentProvider`相当于在这些不同的操作上又进行了一层包装并以`Uri`的方式提供数据的访问接口，使得我们在对数据操作时，可以忽略底层的差异。统一使用`ContentResolver`调用上层接口进行数据操作       
- **跨进程通信**        
我们知道一个`APP`就是一个进程，而不同进程之间通信是比较困难的，但是`ContentProvider`就提供了这种在不同`APP`间进行通信的功能，即跨进程通信，也是`ContentProvider`被使用最多的方面。`ContentProvider`进行跨进程通信的底层原理使用了`Binder`。     

接下来说一下具体使用        
###URI、UriMatcher、MIME        
- **URI(Uniform Resource Identifier)：统一资源标识符**      
可以用来唯一标识ContentProvider和其中的数据     
- **格式**      
一个`uri`由以下部分组成：       

|主题名|授权信息|表名|记录|     
| ---------- | ------------------------------ | ------ | -- |       
| content | com.example.provider | User | 1 |       

如：`content://com.example.provider/User/1`     
在`uri`中可以使用通配符`*`和`#`     
`*`：任意长度的有效字符串       
`#`：任意长度的数字字符串   

- **UriMatcher**        
`UriMatcher`简单说来是一个对`uri`进行管理的类。它主要的方法有：     
**1、`UriMatcher.addURI`**      
 在`ContentProvider`中注册`uri`     

```
//初始化
mUriMatcher=new UriMatcher(UriMatcher.NO_MATCH);
//在ContentProvider中注册uri
mUriMatcher.addURI(AUTHORITY,"user",user_code);
```

**2、`UriMatcher.match(uri)`**      
根据uri返回匹配该uri的自定义代码        

- **MIME**      

用于指定某个扩展名的文件用某种应用程序打开。        
由**类型+子类型**组成    

1、必须以`vnd`开头      
2、内容`uri`以路径结尾即**表名结尾**后接`android.cursor.dir/`，如果以`id`结尾即**数据库表`id`结尾**则后接`android.cursor.item/`     
3、最后接上`vnd.<authority>.<path>`，例：       
`content://com.chenlei.content_provider.user`       
对应`MIME`类型为：`vnd.android.cursor.dir/vnd.com.chenlei.content_provider.user`        
`content://com.chenlei.content_provider.user/1`     
对应`MIME`类型为：`vnd.android.cursor.item/vnd.com.chenlei.content_provider.user`       

### ContentProvider     

`ContentProvider`即是内容提供器，它可以用来统一管理和组织应用数据，向其他应用程序提供接口 便于其他应用程序对**本应用允许的数据**进行操作，以进行跨进程通讯。        
简单来说就是**`ContentProvider`向其他`APP`提供，自己`APP`内数据，的访问接口**       
主要方法由以下几个      

- **onCreate**      
初始化`ContentProvider`的使用使用，如果对数据库进行操作则通常完成数据库的创建和升级操作     
- **insert、delete、update、query**     
对数据进行操作的核心方法**增删改查**，方法中对应方法名来对数据进行相应的增删改查操作。      
- **getType**       
用来得到数据类型，即返回当前` uri` 所代表数据的`MIME`类型       

```
    /**
     * 初始化ContentProvider的使用使用
     * 通常完成数据库的创建和升级操作
     * @return true初始化成功，false初始化失败
     */
    @Override
    public boolean onCreate() {
        mContext=getContext();
        mDBHelper=new DBHelper(getContext());
        sqLiteDatabase=mDBHelper.getWritableDatabase();
        //初始化数据库表
        sqLiteDatabase.execSQL("delete from user");
        sqLiteDatabase.execSQL("insert into user values(1,'Carson');");
        sqLiteDatabase.execSQL("insert into user values(2,'Kobe');");
        sqLiteDatabase.execSQL("delete from job");
        sqLiteDatabase.execSQL("insert into job values(1,'Android');");
        sqLiteDatabase.execSQL("insert into job values(2,'iOS');");

        return true;
    }

    @Nullable
    @Override
    public Cursor query(@NonNull Uri uri, @Nullable String[] strings, @Nullable String s, @Nullable String[] strings1, @Nullable String s1) {
        String tableName = getTableName(uri);
        sqLiteDatabase=mDBHelper.getReadableDatabase();
        Cursor cursor=null;

        cursor=sqLiteDatabase.query(tableName,strings,s,strings1,null,null,s1,null);

        return cursor;
    }

    /**
     * 根据传入的内容来返回相应的MIME类型
     * @param uri
     * @return
     */
    @Nullable
    @Override
    public String getType(@NonNull Uri uri) {
        String mime = null;
        switch (mUriMatcher.match(uri)){
            case user_code:
                mime="vnd.android.cursor.dir/vnd.com.chenlei.content_provider.user";
                break;
            case job_code:
                mime="vnd.android.cursor.dir/vnd.com.chenlei.content_provider.job";
                break;
        }
        return mime;
    }

    /**
     * 插入数据
     * @param uri 数据的资源路径
     * @param contentValues 要插入的数据内容
     * @return 返回一个用于记录新纪录的uri
     */
    @Nullable
    @Override
    public Uri insert(@NonNull Uri uri, @Nullable ContentValues contentValues) {
        String tableName=getTableName(uri);
        //插入数据
        sqLiteDatabase.insert(tableName,null,contentValues);

        mContext.getContentResolver().notifyChange(uri,null);

        return uri;
    }
```

[示例代码](https://github.com/Chenlei534/AndroidLearnCode/blob/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_content_provider/CreateLocalContentProvider.java)

#### 在`AndoridManifest.xml`中注册      

当代码完成后还需要在`AndoridManifest.xml`中注册才能被访问使用。     
```
<!--声明外界进程可访问该Provider的权限（读 & 写）-->
<!--android:permission="com.chenlei.PROVIDER"-->
<!--权限可细分为读 & 写的权限-->
<!--外界需要声明同样的读 & 写的权限才可进行相应操作，否则会报错-->
<!--android:readPermisson = "com.chenlei.Read"-->
<!--android:writePermisson = "com.chenlei.Write"-->
<!--设置此provider是否可以被其他进程使用-->
<!--android:exported="true"-->
<provider
    android:authorities="com.chenlei.content_provider" 
    android:name="com.example.androidprimarycodedemo.four_components.about_content_provider.CreateLocalContentProvider"
    android:permission="com.chenlei.PROVIDER"
    android:exported="true"
/>
```

### ContentResolver     

由于如果应用需要对多个`ContentProvider`进行操作，需要了解各个不同`ContentProvider`的实现等再进行操作。所以`API`中提供了`ContentResolver`类统一管理对`ContentProvider`的操作。       
简单来说就是**通过`ContentResolver`，来对其他`APP`内的数据，进行操作**      
`ContentResolver`使用示例。     
```
    //user表的资源路径
    private Uri uriUser=Uri.parse("content://com.chenlei.content_provider/user");
    /**
     * ContentResolver统一管理ContentProvider间的操作
     * 由于如果需要使用多个ContentProvider进行操作，需要了解各个不同ContentProvider的实现等再进行操作
     * 使用ContentResolver同一管理方便操作和使用。
     */
    private ContentResolver contentResolver=null;
    /**
     * 向ContentProvider中插入数据
     */
    private void insertData(){
        contentResolver=getContentResolver();
        //插入表中的数据
        ContentValues contentValues=new ContentValues();
        contentValues.put("_id", 3);
        contentValues.put("name", "Iverson");

        contentResolver.insert(uriUser,contentValues);
        Logger.e("插入完成");
    }

    /**
     * 从ContentProvider中查询数据
     */
    private void queryData(){
        contentResolver=getContentResolver();
        Cursor cursor=contentResolver.query(uriUser,new String[]{"_id","name"}, null, null, null);
        while (cursor.moveToNext()){
            Logger.e(cursor.getInt(0) +","+ cursor.getString(1));
        }
        cursor.close();
    }
```     

[示例代码](https://github.com/Chenlei534/AndroidLearnCode/blob/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_content_provider/ContentProviderActivity.java)

- **在`AndoridManifest.xml`中注册权限**     
```
    <!--应用跨进程通讯的权限-->
    <uses-permission android:name="com.chenlei.PROVIDER"/>
```     

[上文代码](https://github.com/Chenlei534/AndroidLearnCode/tree/master/androidprimarycodedemo/src/main/java/com/example/androidprimarycodedemo/four_components/about_content_provider)