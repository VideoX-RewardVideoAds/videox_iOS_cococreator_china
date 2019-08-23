# videox_iOS_cococreator_china

* [接入方式](#接入方式)
* [SDK初始化](#SDK初始化)
* [广告功能](#广告功能)
    * [1、开屏广告](#开屏广告)
    * [2、插屏广告](#插屏广告)
    * [3、激励视频](#激励视频)
    * [4、横幅广告](#横幅广告)
* [打包上传](#打包上传)

## 接入方式

### 1. CocosCreator中的接入工作

- 请将包内VideoXSdk.js文件放入工程目录，并导入为插件

### 2. Xcode中的接入工作

> 在CocosCreator中构建发布，打包成iOS平台的Xcode工程。

1. 使用XCode 打开工程，将Libs文件拖入工程目录，注意勾选`Copy Items if needed`

![copy_items](https://github.com/VideoX-RewardVideoAds/videoxdemo_iOS/blob/master/images/copy_items.png)

2. 在Xcode工程 ***App target*** -> ***General*** -> ***Embedded Binaries*** 处 添加 `VideoXSDK.framework`

![embedded binarise](https://github.com/VideoX-RewardVideoAds/videoxdemo_iOS/blob/master/images/embedded_binarise.jpg)

3. 在VXJSToNative.mm文件里 填写配置信息

   ![confog_msg](https://github.com/VideoX-RewardVideoAds/videoxdemo_iOS/blob/master/images/confog_msg.png)



**注意：**

> 如果您的项目同时集成了`AppsFlyer`SDK，请务必在AppsFlyer的方法回调 `-(void)onConversionDataReceived:(NSDictionary*) installData;` 中实现如下代码：
>
```objective-c
-(void)onConversionDataReceived:(NSDictionary*) installData {
	[VideoXSDK sendAfDeepLinkData:installData];
}
```


## SDK初始化

> function initSdk()

SDK要尽可能早的初始化，`VXJSToNative.mm`里的配置信息不填会导致SDK初始化失败。


## 广告功能

> VideoXSdk.js 包含所有SDK广告接口

### 开屏广告

---

#### 展示开屏广告

> function showSplashAd(slotId)



#### 开屏广告回调

```objective-c
function splashAdDidLoad(){
    //开屏广告已经被加载
}

function splashAdDidClick(){
   	//开屏广告已经被点击
}

function splashAdWillShow(){
    //开屏广告即将展示
}

function splashAdDidClose(){
    //开屏广告已经关闭
}

function splashAdDidFailWithError(error){
    //开屏广告展示失败
}
```



### 插屏广告

---

#### 加载插屏广告

> function loadInterstitialAd(slotId)

#### 判断插屏广告是否准备好

> function isInterstitialAdReady(slotId)

#### 播放插屏广告

> function showInterstitialAd(slotId)

请在播放插屏广告前先判断广告是否准备好



#### 插屏广告回调

```objective-c
function onInterstitialAdLoadSuccess(slotId){
    //插屏加载成功
}

function onInterstitialAdLoadError(slotId, error){
    //do something
    //插屏加载失败
}

function onInterstitialAdOpen(slotId){
    //do something
    //插屏已经打开
}

function onInterstitialAdClose(slotId){
    //do something
    //插屏播放完成
}

function onInterstitialAdClick(slotId){
    //do something
    //插屏广告被点击
}
```



### 激励视频


#### 加载激励广告

> function loadRewardAd(slotId)

激励视频加载需要较长时间，请在合理的场景预加载激励视频

#### 判断激励广告是否准备好

> function isRewardAdReady(slotId)

#### 播放激励广告

> function showRewardAd(slotId)

请在播放激励广告前先判断广告是否准备好



#### 激励视频广告回调

```objective-c
function onRewardAdLoadSuccess(slotId){
    //do something
    //激励视频加载完成
}

function onRewardAdLoadError(slotId, error){
    //do something
    //激励视频加载失败
}

function onRewardAdLoadOnShow(slotId){
    //do something
    //激励视频开始播放
}

function onRewardAdLoadOnClose(slotId, shouldReward){
    //do something
    //激励视频播放完成，激励状态
}

function onRewardAdLoadOnShowError(slotId, error){
    //do something
    //激励视频播放失败
}

function onRewardAdLoadClick(slotId){
    //do something
    //激励视频被点击
}
```



### 横幅广告

---

#### 展示横幅广告

> function showBannerAd(slotId,position)

`position` ：传1为底部横幅，传非1的数值为顶部横幅，请传整数类型。



#### 横幅广告回调

```objective-c
function onBannerAdShowError(error){
    //do something
		//横幅广告展示失败
}

function onBannerAdDidClosed(){
    //do something
    //横幅广告已经关闭
}

function bannerAdDidClicked(){
		//横幅广告被点击
}
```


## 打包上传

如果在使用XCode发布app的过程中，你收到如下的错误提示：

![upload_error](https://github.com/VideoX-RewardVideoAds/videoxdemo_iOS/blob/master/images/upload_error.png)


是因为我们的SDK支持了 i386、x86_64

解决方法 1:

* 使用lipo命令 去掉SDK对 i386、x86_64 的支持

解决方法 2:

* 在 ***target*** -> ***Build Phases*** 点击左上角的 ***+***，选中 ***New Run Script Phases***

* 在新添加的 ***Run Script***中添加如下脚本：

```shell
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"  

# This script loops through the frameworks embedded in the application and  
# removes unused architectures.  
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK  
do  
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)  
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"  
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"  

EXTRACTED_ARCHS=()  

for ARCH in $ARCHS  
do  
echo "Extracting $ARCH from $FRAMEWORK_EXECUTABLE_NAME"  
lipo -extract "$ARCH" "$FRAMEWORK_EXECUTABLE_PATH" -o "$FRAMEWORK_EXECUTABLE_PATH-$ARCH"  
EXTRACTED_ARCHS+=("$FRAMEWORK_EXECUTABLE_PATH-$ARCH")  
done  

echo "Merging extracted architectures: ${ARCHS}"  
lipo -o "$FRAMEWORK_EXECUTABLE_PATH-merged" -create "${EXTRACTED_ARCHS[@]}"  
rm "${EXTRACTED_ARCHS[@]}"  

echo "Replacing original executable with thinned version"  
rm "$FRAMEWORK_EXECUTABLE_PATH"  
mv "$FRAMEWORK_EXECUTABLE_PATH-merged" "$FRAMEWORK_EXECUTABLE_PATH"  

done
```

如下图所示：

![run_script](https://github.com/VideoX-RewardVideoAds/videoxdemo_iOS/blob/master/images/run_script.png)


最后重新打包即可。



