.. _android_SDK 使用指南:

Android SDK使用指南
===============================================================================

1. 建立App，下载SDK 
*******************************************************************************


1. 登录PlayData，点击“添加新应用”，进入新应用添加界面。 

2. App建立成功后，可以获得该App的AppKey，以及最新的开发指南和SDK文件。

2. 实现基本的使用 
*********************************************************************************


1. 导入 ``PlayData_Android_SDK.jar`` （简称SDK）
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

首先请在您的工程文件根目录下创建一个名为libs的子目录，并将SDK的JAR包 ``PlayData_Android_SDK.jar`` 拷贝到libs目录下。

对于Eclipse工程，请参照下面的步骤添加JAR包：

1. 在“Package Explorer”标签中右击你的工程并选择“Properties”；

2. 在左侧面板中选择“Java Build Path”；

3. 在主窗口中选择 “Libraries”标签;

4. 点击“Add JARs…”按钮；

5. 选择您拷贝到libs目录下的 ``PlayData_Android_SDK.jar`` ;

6. 点击“OK”完成添加，

2. 配置AndroidManifest.xml
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" 

请按照如下内容配置::

   <manifest....>
   
   <application ……>
   
   ......
   
   <activity ....../>
   
   <meta-data android:value="YOUR_APP_KEY" android:name="ALLYES_APPKEY"></meta-data>
   
   <meta-data android:value="Channel ID" android:name="ALLYES_CHANNEL"/>
   
   </application>
   
   <uses-sdk android:minSdkVersion="7"></uses-sdk>
   
   <uses-permission android:name="android.permission.INTERNET" />
   
   <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
   
   <uses-permission android:name="android.permission.READ_PHONE_STATE" />
   
   <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
   
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
   
   <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
   
   </manifest>

说明：

META-DATA::

    ALLYES_APPKEY 用来定位该应用程序的唯一性。
    
    ALLYES_CHANNEL 用来标注应用推广渠道，渠道名应为20位以内字符；
    

3. 添加代码
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

1. 添加引用: import com.allyes.playdata.AllyesAgent  

2. 注册 Activity: 在每个Activity的onResume方法中调用 AllyesAgent.onResume(Context), onPause方法中调用AllyesAgent.onPause(Context)::

    public void onResume() {  
    
        super.onResume();  
        
        AllyesAgent.onResume(this);  
        
    }  
    
    public void onPause() {  
    
        super.onPause();  
        
        AllyesAgent.onPause(this);  
        
    }

API:

public void onResume(Context context)
    
    context 当前Activity的引用。
    

public void onPause(Context context)
    
    context 当前Activity的引用。

说明:

    方法将会自动地从AndroidManifest.xml文件里读取Appkey。  
    
    确保在所有的activity中都调用 AllyesAgent.onResume() 和AllyesAgent.onPause()方法，这两个调用将不会阻塞应用程序的主线程，也不会影响应用程序的性能。 
    
    注意如果您的Activity之间有继承或者控制关系请不要同时在父和子Activity中重复添加onPause和onResume方法，否则会造成重复统计(eg.使用TabHost、TabActivity、ActivityGroup时)。
    
    一个应用程序在多个activity之间连续切换时，将会被视为同一个session(启动)。  
    
    当用户两次使用之间间隔超过30秒时，将被认为是两个的独立的session(启动)，例如用户回到home，或进入其他程序，经过一段时间后再返回之前的应用。

4. 可选功能
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

1. 设置Debug模式

    AllyesAgent.setDebugMode(boolean isDebug)

    在Debug 模式下，会在logcat中打印debug信息（默认false）。

2. 设置Session启动

AllyesAgent.setSessionContinueMillis(long interval)

定义Session重启间隔，单位为毫秒（默认30秒）。  

3. 设置收集位置信息

AllyesAgent.setAutoLocation(boolean isAutoLocation)

设置是否允许收集地理位置信息 （默认true）

4. 设置路径访问功能

AllyesAgent.openActivityDurationTrack(boolean isOpenActivityTrack)

设置是否打开页面路径访问功能（默认true）

5. 设置openGL 信息
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

AllyesAgent.setOpenGLContext(GL10 gl)

设置openGL 信息，辅助统计GPU 信息


3. 使用自定义事件 
*********************************************************************************

除了基本统计分析功能外，我们还支持您自定义的事件分析，例如您可以统计游戏中通过不同关卡的人数，广告的点击次数或者视频被播放的次数等等。 使用自定义事件功能请先在网站应用管理后台中添加相应的自定义事件后，服务器才会对相应的自定义事件请求进行处理。 


1. 事件数量统计
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

1. 基本的事件数量统计，调用如下方法::

    AllyesAgent.onEvent(Context context, String event_id);
    
API:  
    
public void onEvent(Context context, String event_id)
    
    context 当前Activity的引用  

    event_id 为当前统计的事件ID,注意要先在PlayData网站上注册此事件ID。
        
2. 记录事件的不同属性及取值，调用如下方法::

    AllyesAgent.onEvent(Context context, String event_id, Map<String, String>; map);
    
API:  
    
public void onEvent(Context context, String event_id, Map map)
    
    context 当前Activity的引用  

    event_id 为当前统计的事件ID,注意要先在PlayData网站上注册此事件ID。  

    map 为当前事件的属性和取值集合（key-value）

3. 考虑事件在一个属性上的取值，可以调用如下方法::

    AllyesAgent.onEvent(Context context, String event_id, String label);
    
API:  
    
public void onEvent(Context context, String event_id, String label)
    
    context 当前Activity的引用  

    event_id 为当前统计的事件ID,注意要先在PlayData网站上注册此事件ID。  

    label 事件的一个属性描述
    
2. 事件时长统计 
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

有的事件是持续发生的，需要记录其持续的时间，这里提供两种解决方法。

1. 在事件开始和结束时分别调用 ``onEventBegin`` 和 ``onEventEnd`` 两个函数::

    AllyesAgent.onEventBegin(Context context, String event_id);
    
    ...
    
    AllyesAgent.onEventEnd(Context context, String event_id);
    
API:  
    
public void onEventBegin(Context context, String event_id)
    
public void onEventEnd(Context context, String event_id)
    
    context 当前Activity引用  

    event_id 为当前统计的事件ID,注意要先在PlayData网站上注册此事件ID.

public void onEventBegin(Context context, String event_id, String label)

public void onEventEnd(Context context, String event_id, String label)
    
    context 当前Activity引用  

    event_id 为当前统计的事件ID,注意要先在PlayData网站上注册此事件ID.  

    label 事件的一个属性描述
        
2. 跟踪时长的事件包含多个属性,在事件开始和结束时分别调用onKVEventBegin和 onKVEventEnd两个函数::

    AllyesAgent.onKVEventBegin(Context context, String event_id, Map<String, String> map, String ekvFlag);
    
    ...
    
    AllyesAgent.onKVEventEnd(Context context, String event_id, String ekvFlag);
    
API:  
    
public void onKVEventBegin(Context context, String event_id, Map map, String ekvFlag)
    
    context 当前Activity引用  

    event_id 为当前统计的事件ID,注意要先在PlayData网站上注册此事件ID.  

    map 为当前事件的属性和取值集合（key-value）  

    ekvFlag 事件标示符

public void onKVEventEnd(Context context, String event_id, String ekvFlag)

    context 当前Activity引用  

    event_id 为当前统计的事件ID,注意要先在PlayData网站上注册此事件ID  

    ekvFlag 事件标示符，ekvFlag和event_id一起标示一个唯一事件，并不会被统计；对于同一个事件，在onKVEventBegin和onKVEventEnd 中要传递相同的event_id 和 flag  

3. 自己计算并上传event时长,在您想跟踪时长的代码部分，调用如下方法::

    AllyesAgent.onEventDuration(Context context, String event_id, long duration);
    
    or
    
    AllyesAgent.onEventDuration(Context context, String event_id,String label, long duration)
    
    or
    
    AllyesAgent.onEventDuration(Context context, String event_id, Map<String, String> map, long duration)
    
API：  
    
public void onEventDuration(Context context, String event_id, long duration)

public void onEventDuration(Context context, String event_id,String label, long duration)
    
    context 当前Activity引用  

    event_id 为当前统计的事件ID,注意要先在PlayData网站上注册此事件ID  

    label 事件的一个属性描述  

    duration 事件持续时长，单位毫秒，您需要手动计算并传入时长，作为事件的时长参数

public void onEventDuration(Context context, String event_id, Map map, long duration)
    
    context 当前Activity引用

    event_id 为当前统计的事件ID,注意要先在PlayData网站上注册此事件ID

    map 为当前事件的属性和取值集合（key-value）

    duration 事件持续时长，单位毫秒，您需要手动计算并传入时长，作为事件的时长参数

4. 使用分发渠道分析 
*********************************************************************************

有时需要统计应用程序的分发渠道，例如有多少用户来从应用汇 下载了您的应用,又有多少用户通过Google android market下载到您的应用程序。您只需要在AndroidManifest.xml里添加meta-data，并将 value属性修改为对应的发布渠道名。

配置AndroidManifest.XML添加下面代码::

    <application ……>
    
        <activity ……/>
        
        <meta-data android:value="Channel ID" android:name="ALLYES_CHANNEL"/>
        
    </application>

当然，这需要您在不同渠道发布应用程序时，重新编译打包。

说明

    不要改变'ALLYES_CHANNEL'，修改'Channel ID'为您的渠道名称(eg.value="AndroidMarket")。

5. 设置数据发送策略 
*********************************************************************************

默认启动时发生

6. 在webview中调用PlayData的统计分析代码
*********************************************************************************

如果你的页面中使用了WebView嵌入HTML,js的代码，并且希望统计HTML中的点击事件，比如点击了某个链接的次数，这时你需要阅读下面的文档，通过PlayData的 js 接口来统计相关的事件。注意如果你的HTML是运行在浏览器的，那么还
是无法统计的，下文仅针对使用WebView加载 网页的情况。

1. Java代码集成 
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

首先要找到你的WebView对象，并做下面的初始化工作::

    WebView webview = (WebView) findViewById(R.id.webview);
    
    new AllyesAgentJSInterface(Context, webview);
    
API:  

public AllyesAgentJSInterface(Context context, WebView webview, WebViewClient client)

    context 当前Activity引用

    webview 当前的WebView对象

    client WebViewClient 实例，如果开发者实现了自己的WebViewClient，那么请传入这个实例

public AllyesAgentJSInterface(Context context, WebView webview)

    context 当前Activity引用

    webview 当前的WebView对象

集成了上面的代码后，我们将会监听PlayData的 js 代码调用.

说明：

    如果您的代码中同样实现了自己的 WebViewClient 请务必在初始化的时候，调用::
 
       public AllyesAgentJSInterface(Context context, WebView webview, WebViewClient client) 
 
    并传入相应的WebViewClient。

2. js 代码集成 
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

在网页端，请添加如下的 js 代码::

    <script type="text/javascript">

    
        function onEvent(tag, label, duration) {
        
        window.location='playdata://event/'+JSON.stringify({e:tag,l:label, d:duration});
        
        }
     
        function onKVEvent(tag, map, duration) {
        
           var jsonObj = {
           
              e : tag,
              
              d : duration,
              
              k : map
              
            };
            
            window.location='playdata://ekv/'+JSON.stringify(jsonObj);
            
        }

    </script>

这里面提供了两个js接口，分别用来统计自定义事件和K-V对形式的KV事件，并且和Java接口有如下的映射::

    onEvent(tag) -> AllyesAgent.onEvent(Context context, String tag); 
    
    //tag 传入字符串
    
    onEvent(tag, label)-> AllyesAgent.onEvent(Context context, String tag, String label);
    
    // tag ,label 传入字符串
    
    onEvent(tag, '',  du) -> AllyesAgent.onEvent(Context context, long duration);
    
    // tag 传入字符串， 第二个变量 传入空字符串, du 传入 数值类型
    
    onEvent(tag, label, du) -> AllyesAgent.onEvent(Context context, String tag, String label, long du);
    
    // tag, label 传入字符串, du 传入数值类型
    
    onKVEvent(id, {} ) -> AllyesAgent.onEvent(Context context, HashMap map);
    
    // id 传入字符串， 第二个参数传入 js 对象eg：{'name':'htc G20','type':'android'}
    
    onKVEvent(id, {} , du) -> AllyesAgent.onEvent(Context context, HashMap map, long du);
    
    // id 传入字符串， 第二个参数传入 js 对象eg：{'name':'htc G20','type':'android'}, du 传入 数值类型




