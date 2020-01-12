# 跨进程通信webview
### -实现js-native的交互,支持自定义 js-bridge(桥接),并通过自定义注解的方式实现桥接的初始化
****

#### webview 需要跨进程么？

1.Android webview的兼容性问题
    关于使用的浏览器内核 android 4.4以下WebKit，4.4以上chromium
  不同的手机厂商对浏览器或者内核都有改动,导致android的webview存在较多的兼容性问题。
  为了应用程序更加健壮,让webview在单独进程中,若webview出现闪退不会影响到应用程序的使用。
  
2.内存问题
    由于android系统对每个进程都分配了限制大小的内存,若应用程序申请不到连续可用的内存时候会oom
  webview十分占用内存,让webview在单独进程中,可以让自身的应用程序进程所分配可使用的内存更大,减
  少OOM的可能性。关闭webview也更彻底一些。
  

#### 通信设计：
    js - native 通信： 
    通过@JavascriptInterface 实现js - native 的通信
    Native接收的方法:  post(action,params,callback…)
    统一接口
    通过参数差异化去触发不同action对应的原生指令
    params 是一个 json 字符串,可以灵活的进行输入
    callback(回调方法) 前端将全局的方法名称传入, 
    webview通过 loadUrl的方式去调用回调方法，并将返回值包装成json返回给前端
    
    webview-应用程序中的指令 通信
    由于指令 可能会涉及到不同的业务,所以是在应用进程中实现
    这边就涉及到了跨进程通信。
    这边采取通信效率较高的 AIDL 的方式。通过aidl实现的跨进程的方法调用和回调
    整个前端到webview再到应用程序再按原理返回[回调]的流程如下图所示

![流程图](https://github.com/xiangwangrush/-webview/blob/master/image/t.png "若加载不出来,请点击查阅")

    连接管理器-实现稳定的AIDL跨进程通信;
    指令管理器-实现指令的的注册,管理;
    指令分发器-实现js的请求到对应的指令
    
    关于指令的初始化,这边采用自定义注解,编译时生产注册代码,并在webview创建时候将所有指令注册到
    指令管理器中,方便后面指令分发器可以正确的找到对于的指令去执行。
    
    
 #### 如何使用:
     建议是先down下这个demo工程,跑一下看下效果,demo中实现了两个有回调的 js 请求原生的操作。
     后面根据自己的项目进行对于的改造。基本是不需要怎么改的,因为这边是非常精简的核心内容。


    
    

    

