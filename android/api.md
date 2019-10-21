# 云知声口语评测服务Android SDK API文档

* [OralEvalSDKFactory](#oralevalsdkfactory)
    * [OralEvalSDKFactory.StartConfig](#oralevalsdkfactorystartconfig)
* [IOralEvalSDK](#ioralevalsdk)
	* [IOralEvalSDK.OfflineSDKError](#ioralevalsdkofflinesdkpreparationerror)
 	* [IOralEvalSDK.ICallback](#ioralevalsdkicallback)
 * [SDKError](#ioralevalsdkerror)
 * [JSON字段说明](#Json)
 * [FAQ](#FAQ)
 
 
 **说明：Android SDK V3.6.31版本及之后的版本，SDK内部做了混淆设置，业务层在做混淆的时候，不需要对oraleval-android-sdk.jar 进行混淆，如果需要混淆，<a href=https://github.com/oraleval/FAQ-Docs/blob/master/mix.md>请查看混淆配置文件</a>**
 
### SDK调用流程图

> ![image](https://github.com/oraleval/OralEvalSDKDocPrj/blob/master/评测sdk流程图.png)
 

### <a name="oralevalsdkfactory"></a>OralEvalSDKFactory


评测静态工厂类，用于设置评测服务地址、初始化离线引擎、发起评测等。

<br/>
* public final static String SDK_VERSION; 

|  |  |
| ----- | ----- |
| 说明 | 获得当前SDK版本号 |
| 版本支持 | 最低2.0.0 | 

<br/>
* public static void initServerAndPort(String server, int port)


| | |
| -----| ------ |
| 说明| 设置服务地址。一般不用设置，使用默认值即可 |
|版本支持| 最低2.0.0 |
|参数 server|服务的域名或者ip地址|
|参数 port|服务的tcp端口|

<br/>
* public static <a href="#ioralevalsdkofflinesdkpreparationerror">IOralEvalSDK.OfflineSDKError</a> initOfflineSDK(Context context, String workingDir)

| | |
| ----- | ----- |
| 说明| 初始化离线评测引擎，该方法会解压离线引擎模型并加载模型。调用任何离线功能之前需要确保该初始化成功。如果不需要使用离线评测功能，则无需调用此初始化方法|
|版本支持| 最低2.0.0 |
|参数 context| android.content.Context实例，比如一个Activity|
|参数 workingDir|离线引擎模型的解压的位置|
|返回值|初始化正确返回NOERROR；其他错误参考[IOralEvalSDK.OfflineSDKError](#ioralevalsdkofflinesdkpreparationerror)的说明|

<br/>
* public static void cleanupOfflineSDK(Context context)

| | |
| ----- | ----- |
| 说明| 销毁离线引擎。销毁后不能再调用任何离线评测功能|
|版本支持| 最低2.0.0 |
|参数 context|android.content.Context实例，比如一个Activity|

<br/>
* public static <a href="#ioralevalsdk"> IOralEvalSDK </a> start(Context context, <a href="#oralevalsdkfactorystartconfig"> StartConfig </a> cfg, <a href="#ioralevalsdkicallback">  ICallback </a> cb)

| | |
| ----- | ----- |
| 说明| 发起一个评测。评测必须是单实例的，即上一次评测结束之前，不能发起新的评测|
|版本支持| 最低2.0.0 |
|参数 context|android.content.Context实例，比如一个Activity|
|参数 cfg|评测参数，详见[StartConfig](#oralevalsdkfactorystartconfig)|
|参数 cb|监听接口，用于接收评测结果，实时音量，实时语音数据，和错误信息，详见[ICallback](#ioralevalsdkicallback)|


#### <a name="oralevalsdkfactorystartconfig"></a>OralEvalSDKFactory.StartConfig


评测参数类。标识了所有评测的参数设置。

<br/>
* public StartConfig(String oralText)

| | |
| ----- | ----- |
| 说明|指定评测文本，构建一个默认参数对象。默认ServiceType为A，VadEnable为false，ConnectTimeout为100，useOfflineWhenFailedToConnectToServer为false，AudioStream为null|
|版本支持| 最低2.0.0 |
|参数 oralText|要评测的文本。<!--文本的具体要求请参考[评测输入输出说明](评测输入输出说明.md)-->|
|注意|请不要传递空字符串，否则有可能会抛出IllegalArgumentException异常|

<br/>

* public void setUid(String uid)

| | |
| ----- | ----- |
| 说明|指定用户标识。我们的后台提供针对每个用户的统计和学习报告功能，需要根据用户标识获取学习报告|
|版本支持| 最低2.0.0 |
|参数 uid|用户标识，如用户名等可以唯一识别用户的字符串|

<br/>

* public void setOnline_ip(String online_ip)

| | |
| ----- | ----- |
| 说明|设置在线评测ip和端口号，默认不设置，在线评测就走我们内部默认的ip和端口号，如果设置了ip和端口号，在线评测就走设置的ip和端口号|
|版本支持| 最低3.6.22 |
|参数 online_ip|ip和端口号组成的字符串|

<br/>
* public void setVadEnable(boolean vadEnable)

| | |
| ----- | ----- |
| 说明|设置vad是否启用。VAD用于检测用户是否说话，以及是否说话完成。在检测到用户没有说话或者说话已经完成时会自动结束评测|
|版本支持| 最低2.0.0 |
|参数 vadEnable|true启用VAD，false不启用VAD，默认不启动|

<br/>
* public void setVadBeforeMs(int vadBeforeMs)

| | |
| ----- | ----- |
| 说明|设置最长前置静音时间。即如果评测开始后的vadBeforeMs毫秒内没有检测到说话，就自动结束。对应[ICallback](#ioralevalsdkicallback)中的onStop()方法中的EndReason为NoVoice|
|版本支持| 最低2.0.0， 3.0.0之后的版本将停止类型从int改为枚举 |
|参数 vadBeforeMs|最长前置静音时间，默认2000(毫秒)，最大值3000(毫秒)，最小值1000(毫秒)|

<br/>
* public void setVadAfterMs(int vadAfterMs)

| | |
| ----- | ----- |
| 说明|设置最长后置静音时间。即如果检测到说话后，在vadAfterMs毫秒内一直没有检测到说话，就自动结束。对应[ICallback](#ioralevalsdkicallback)中的onStop()方法中的EndVoice为VoiceEnd|
|版本支持| 最低2.0.0， 3.0.0之后的版本将停止类型从int改为枚举 |
|参数 vadAfterMs|最长后置静音时间，默认2000(毫秒)。此参数最大值支持3000毫秒，大于3000毫秒内部按照3000毫秒处理，最小值1000(毫秒)|

<br/>
* public void set_useOfflineWhenFailedToConnectToServer(boolean _useOfflineWhenFailedToConnectToServer)

| | |
| ----- | ----- |
| 说明|设置是否启动离在线混合评测，即遇到网络或者服务错误的时候，自动使用本地的评测引擎。离线评测引擎需要SDK支持|
|版本支持| 最低2.0.0 |
|参数 _useOfflineWhenFailedToConnectToServer|true启动混合评测，false仅使用在线评测。默认为false|

<br/>
* public void setOralText(String oralText)

| | |
| ----- | ----- |
| 说明|设置评测文本|
|版本支持| 最低2.0.0 |
|参数 oralText|需要评测的音频对应的文本。文本长度最大支持16000字符<!--文本的具体要求请参考[评测输入输出说明](评测输入输出说明.md)-->|
|注意|请不要传递空字符串，否则有可能会抛出IllegalArgumentException异常|

<br/>
* public void setOralEvalMode(OralEvalEnum.OnlineUS)

| | |
| ----- | ----- |
| 说明|设置评测语言|
|版本支持| 最低3.6.29 |
|参数 enum|需要评测的语言种类默认为英文。<!--文本的具体要求请参考[评测输入输出说明](评测输入输出说明.md)-->|
|注意|目前仅支持OralEvalEnum.OnlineUS和OralEvalEnum.OnlineCH|
<br/>
* public void setScoreAdjuest(float scoreAdjuest)

| | |
| ----- | ----- |
| 说明|调整评分严厉程度，比如对于鼓励为主的应用可以适当提升评测得分|
|版本支持| 最低2.4.3 |
|参数 scoreAdjuest|得分系数。具体值请咨询客户经理（默认值是1.0，范围（0.6~1.9））|

<br/>
* public void setServiceType(String serviceType)

| | |
| ----- | ----- |
| 说明|设置评测模式|
|版本支持| 最低2.0.0 |
|参数 serviceType|需要评测的模式。具体参考<a href="https://github.com/oraleval/FAQ-Docs/blob/master/云知声模式说明文档-20180412.pdf">评测模式说明文档</a>|

<br/>
* public void setConnectTimeout(int connectTimeout)

| | |
| ----- | ----- |
| 说明|设置在线评测时连接服务器的超时时间。对应java.net.socket.connect()中超时参数|
|版本支持| 最低2.0.0 |
|参数 connectTimeout|连接评测服务时的超时时间，单位为毫秒。默认值为1000毫秒|

<br/>
* public void setVolumeReport(boolean on)

| | |
| ----- | ----- |
| 说明|设置SDK是否上报音量，如果应用层不需要实时音量，请关闭上报音量功能，减少不必要的计算消耗|
|版本支持| 最低3.5.9 |
|参数 on|true为打开上报，flase关闭上报。默认为true|

<br/>
* public void setAudioStream(InputStream audioStream)

| | |
| ----- | ----- |
| 说明|设置评测的音频流。音频格式为16k采样，16bit有符号整数，单声道的pcm|
|版本支持| 最低2.0.0 |
|参数 audioStream|不为空，则从该流中读取音频评测， 否则从麦克风录音评测。默认从麦克风录音评测|

<br/>
* public void setMp3Audio(boolean mp3Audio)

| | |
| ----- | ----- |
| 说明|设置本地音频数据回调的音频数据格式|
|版本支持| 最低2.0.0 |
|参数 mp3Audio|true，则以mp3格式输出， 否则以16k，16bit有符号整数，单声道pcm格式输出|

<br/>
* public void setBufferLog(boolean bufferLog)

| | |
| ----- | ----- |
| 说明|设置是否保存评测SDK内部Log，以便需要时取出。参考[IOralEvalSDK](#ioralevalsdk).getLog()|
|版本支持| 最低2.6.5 |
|参数 bufferLog|true，内部保存Log；否则，内部不保存Log|

<br/>

* public void setAsyncRecognize(boolean asyncRecognize)

| | |
| ----- | ----- |
| 说明|设置是否启用延迟评测。|
|版本支持| 最低3.6.14 |
|参数 asyncRecognize|true启用延迟评测，false不启用延迟评测，默认不启用|	
<br/>

* public void setReTry(boolean reTry)

| | |
| ----- | ----- |
| 说明|设置是否使用重试，注意：如果使用重试，在构造OralEvalSDKFactory.StartConfig实例时需要用StartConfig(String oralText, String fileFullName)进行构造，且fileFullName文件为opus格式的文件，可参考demo|
|版本支持| 最低v3.6.17 |
|参数 reTry|true使用重试，false不使用重试，默认不使用|	
<br/>

* public void setSecret(String secret)

| | |
| ----- | ----- |
| 说明|控制appkey访问权限，增强appkey使用的安全性|
|版本支持| 最低3.6.40 |
|参数 secret|secret客户可在云知声appkey管理平台生成、修改；|
<br/>

### <a name="ioralevalsdk"></a> IOralEvalSDK

<br/>
评测实例，从<a href="#oralevalsdkfactory">OralEvalSDKFactory</a>.start()创建。一个时刻只能有一个评测实例。
<br/>

* public void cancel()

| | |
| ----- | ----- |
| 说明| 取消评测，将会在[IOralEvalSDK.ICallback](#ioralevalsdkicallback).onCancel()回调中完全结束上次评测，或者[IOralEvalSDK.ICallback](#ioralevalsdkicallback).onError()中捕获错误|
|版本支持| 最低3.6.16 |

<br/>
* public void stop()

| | |
| ----- | ----- |
| 说明| 停止评测，将会在 <a href="#ioralevalsdkicallback">IOralEvalSDK.ICallback</a>.onStop()回调中得到结果，或者[IOralEvalSDK.ICallback](#ioralevalsdkicallback).onError()中捕获错误|
|版本支持| 最低2.0.0 |

<br/>
* public String getLog()

| | |
| ----- | ----- |
| 说明| 获得该次评测的Log字符串。当遇到问题是，该字符串可以帮助Unisound分析错误原因。|
| 注意事项 | 评测参数中需要指明打开Log功能。[OralEvalSDKFactory.StartConfig](#oralevalsdkfactorystartconfig).setBufferLog(true)。调用该函数取出Log后，内部Log将会清空，即取出的内容每次都是最新且不重叠的，如果开始评测后没有调用过该函数，那么Log会从评测开始之时，一直保存到评测结束|
|返回值|从上次取出Log后开始，到目前为止，该次评测内部的Log|
|版本支持| 最低2.6.5 |


#### <a name="ioralevalsdkofflinesdkpreparationerror"></a> IOralEvalSDK.OfflineSDKError


离线SDK错误类型

<br/>
枚举值：
<br/>

| 值|说明 |版本支持|
| ----- | ----- | ----- |
| NOERROR| 没有错误 | 最低2.0.0
|WRONG_MODEL | 离线模型数据错误 |最低2.0.0
|WRONG_STATE | 离线调用流程错误。遇到此错误，请检查是否启动了多个评测实例 |最低2.0.0
|WRONG_REFERENCE | 离线引用错误。遇到此错误联系云知声 |最低2.0.0
|WRONG_DATA |离线数据错误。遇到此错误联系云知声  |最低2.0.0
|OUT_OF_MEMORY |离线评测计算内存不足 |最低2.0.0
|OUT_OF_VOC |文本单词超出离线词汇表 |最低2.0.0
|TEXT_TOO_LONG |文本长度超出离线引擎处理能力 |最低2.0.0
|TEXT_EMPTY |空文本 |最低2.0.0
|EXPIR | 离线引擎已经过期 |最低2.0.0
|UNKNOWN |未知错误。遇到此错误联系云知声 |最低2.0.0
|TIMEOUT|超时|最低3.0.0


#### <a name="ioralevalsdkerror"></a> SDKError


在线SDK错误类型和错误码(Category和对应的errno)：

| 值|说明 |版本支持|
| ----- | ----- | ----- |
|Network | 网络错误。错误码-7表示连接失败或超时，-8表示读取结果失败，-9标识发送数据失败 |最低3.0.0
|Device | 设备错误。错误码-1001标识打开或读取录音设备失败，-1002是当输入音源是InputStream时读取该输入流失败|最低3.0.0
|Unknown_word | 所有评测的单词都不在词汇表中。错误码为-2001|最低3.0.0
|Server |服务器错误。 此时错误码是服务端返回的，请联系云知声根据错误码进一步分析。常见内错误码包括0xFFF7文本为空内容，0xE007文本过长，0xE001暂时没有可用资源，0x2003内容格式错误 |最低3.0.0

目前阶段后端还有缺陷，不能正确返回所有错误码，故同时需要httpErrorMessage供定位问题。


#### <a name="ioralevalsdkicallback"></a> IOralEvalSDK.ICallback


评测回调接口。由评测发起者实现，用于接收评测结果、评测录音等信息。
此接口的方法，由SDK在工作线程中调用，因此不是在UI主线程。且为了正常执行评测过程不能在此接口的方法中执行阻塞或耗时操作

<br/>
* public void onStart([IOralEvalSDK](#ioralevalsdk) who, int audioRecorderSessionId)

| | |
| ----- | ----- |
| 说明| 评测开始回调 |
|版本支持| 最低2.0.0 |
|参数 who|评测实例。标示谁发起了此回调
|参数 audioRecorderSessionId|从麦克风录音评测时，启动的AudioRecord实例的AudioSessionId，对应AudioRecord.getAudioSessionId()方法的返回值。如果无法正常取得AudioSessionId，则为-1|

<br/>
* public void onError <a href="#ioralevalsdk">IOralEvalSDK</a> who, <a href="#ioralevalsdkerror">SDKError</a>  error,  <a href="#ioralevalsdkofflinesdkpreparationerror">OfflineSDKError</a> offlineError)

| | |
| ----- | ----- |
| 说明| 评测出错回调。返回错误后，评测会自动结束，不需要再手动调用[IOralEvalSDK](#ioralevalsdk).stop() |
|版本支持| 最低2.0.0 |
|参数 who|评测实例。标示谁发起了此回调|
|参数 error| 在线评测错误的原因值|
|参数 offlineError| 离线评测错误的原因值 |

<br/>
* public void onStop(<a href="#ioralevalsdk">IOralEvalSDK</a> who, String result, boolean isOffline, String url, EndReason stopType)

| | |
| ----- | ----- |
| 说明| 评测结果回调。返回结果后，评测会自动结束，不需要再手动调用[IOralEvalSDK](#ioralevalsdk).stop() |
|版本支持| 最低2.0.0 |
|参数 who|评测实例。标示谁发起了此回调|
|参数 result| 在线评测错误的原因值|
|参数 isOffline| 结果是否为离线评测的结果 |
|参数 url| 在线评测的录音url地址。如果本次是离线结果，即isOffline为true，则此url无效 |
|参数 stopType| 结束评测的原因。UserAction是调用[IOralEvalSDK](#ioralevalsdk).stop()结束，VoiceEnd是VAD检测到完成说话结束，NoVoice是VAD检测到长时间没有人说话结束,InputStreamEnd是当使用非mic音频源评测时，音频流结束或读取失败|

<br/>
* public void onCancel()

| | |
| ----- | ----- |
| 说明| 取消评测回调。无返回结果，评测会自动结束，需要手动调用[IOralEvalSDK](#ioralevalsdk).cancel() |
|版本支持| 最低3.6.16 |

<br/>
* public void onVolume(<a href="#ioralevalsdk">IOralEvalSDK</a> who, int value)

| | |
| ----- | ----- |
| 说明| 评测实时音量回调 |
|版本支持| 最低2.0.0 |
|参数 who|评测实例。标示谁发起了此回调|
|参数 value| 实时录音的音频音量值，0-100范围|

<br/>
* public void onAudioData(<a href="#ioralevalsdk">IOralEvalSDK</a> who, byte[] audioData, int offset, int len)

| | |
| ----- | ----- |
| 说明| 评测实时录音数据回调 |
|版本支持| 最低2.0.0 |
|参数 audioData|录音数据。数据格式根据[OralEvalSDKFactory.StartConfig](#oralevalsdkfactorystartconfig).setMp3Audio()设置|
|参数 offset| 实时录音流数据的偏移值 |
|参数 len|实时录音流数据的总长度|

<br/>
* public void onOpusData(<a href="#ioralevalsdk">IOralEvalSDK</a> who, byte[] opusData, int offset, int len)

| | |
| ----- | ----- |
| 说明| 评测实时opus流数据回调 |
|版本支持| 最低v3.6.17 |
|参数 opusData|opus流数据|
|参数 offset| 实时录音的opus流数据的偏移值|
|参数 len |实时录音的opus流数据的总长度|

<br/>
* public void onAsyncResult([<a href="#ioralevalsdk">IOralEvalSDK</a> who, String url);

| | |
| ----- | ----- |
| 说明| 延迟评测回调，默认为不启用，需要设置 public void setAsyncRecognize(boolean asyncRecognize)为true|
|版本支持| 最低3.6.14|
|参数 who|评测实例。标示谁发起了此回调|
|参数 url| 延迟评测结果所在的地址|

<br/>
* public void onStartOralEval()

| | |
| ----- | ----- |
| 说明| 录音结束回调 |
|版本支持| 3.6.24 |


### <a name="Json"></a>评测返回结果中Json字段说明

**Json字段说明请查找：**<a href=https://github.com/oraleval/FAQ-Docs/blob/master/Json%20Description.md>Json字段说明</a>

### <a name="FAQ"></a>FAQ

**DEMO使用方法

> 将SDK中libs下面的jar包拷贝到工程的libs目录下；src/main下面的assets、java、jniLibs、res目录以及AndroidMainifest.xml覆盖到工程对应的目录。

> 工程结构图如下：

> ![image](https://github.com/oraleval/OralEvalSDKDocPrj/blob/master/Project.png)

```
  sourceSets {
    main {
      jniLibs.srcDir 'libs'
    }
  }
该代码加入到build.gradle
```
> 动态获取权限，运行过程需要给予应用所需要的录音及文件写入权限

**VAD功能

> 该功能是自动检测是否在录音，如果没有在录音，则自动停止录音。在V3.6.36版本之前是默认开启的，在V3.6.36版本后默认关闭。


