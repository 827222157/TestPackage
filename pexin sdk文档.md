<center><font face="微软雅黑" size="6" color="颜色">PAI sdk接入文档</font> </center>

---
- [一、gradle文件配置](#一gradle文件配置)
        - [1、dependencies增加引用](#1dependencies增加引用)
        - [2、android标签下加入以下内容，用以引用aar内容](#2android标签下加入以下内容用以引用aar内容)
- [二、proguard-rules.pro混淆配置](#二proguard-rulespro混淆配置)
- [三、Manifest清单文件](#三manifest清单文件)
        - [1、注册平台申请注册的appid，name字段不可改](#1注册平台申请注册的appidname字段不可改)
        - [2、权限注册](#2权限注册)
        - [3、Gradle文件](#3gradle文件)
- [四、PAI sdk初始化](#四pai-sdk初始化)
        - [1、入口类：MtSDK](#1入口类mtsdk)
        - [2、初始化配置类 MtConfigBuilder](#2初始化配置类-mtconfigbuilder)
- [五、PAI sdk 开屏广告](#五pai-sdk-开屏广告)
        - [1、入口类MtSplash](#1入口类mtsplash)
        - [2、行为回调接口类MtSplashListener](#2行为回调接口类mtsplashlistener)
- [六、PAI sdk 激励视频广告](#六pai-sdk-激励视频广告)
        - [1、MtReward](#1mtreward)
        - [2、行为回调接口类RewardActionListener](#2行为回调接口类rewardactionlistener)
- [七、PAI sdk 原生广告](#七pai-sdk-原生广告)
        - [1、广告获取入口类MtNativeLoader](#1广告获取入口类mtnativeloader)
        - [2、原生广告加载行为回调MtLoadListener](#2原生广告加载行为回调mtloadlistener)
        - [3、原生广告加载行为回调MtActionListener](#3原生广告加载行为回调mtactionlistener)
        - [4、原生广告视频行为回调MtMediaListener](#4原生广告视频行为回调mtmedialistener)
        - [5、原生广告数据MtNativeInfo](#5原生广告数据mtnativeinfo)
- [八、PAI sdk Banner广告](#八pai-sdk-Banner广告)
        - [1、入口类MtBanner](#1入口类mtbanner)
        - [2、行为回调接口类MtBannerListener](#2行为回调接口类mtbannerlistener)
- [九、PAI sdk 插屏广告](#九pai-sdk-插屏广告)
        - [1、MtInterstitial](#1MtInterstitial)
        - [2、广告加载行为回调MtInterstitialListener](#2广告加载行为回调MtInterstitialListener)
        - [3、广告视频行为回调MtInterstitialMediaListener](#3广告视频行为回调MtInterstitialMediaListener)
- [十、PAI 下载app信息回调](#十PAI下载app信息回调)
        - [1、信息回调类MtDLInfoListener](#1信息回调类MtDLInfoListener)
        - [2、下载行为确认回调类MtDLConfirmCallback](#2下载行为确认回调类MtDLConfirmCallback)
- [十一、PAI sdk常量](#十一pai-sdk常量)
        - [1、常量类MtConstant](#1常量类mtconstant)
- [十二、PAI sdk常见错误码](#十二pai-sdk常见错误码)

- [PAI sdk修改包名](#Gradle 修改包名)




### 一、gradle文件配置

#####1、dependencies增加引用

        compile(name: 'mt_family', ext: 'aar')

        //compile (name: "GDTSDK.unionNormal.${支持的版本}", ext: 'aar')//如果支持广点通sdk，根据对接详情处理
        //compile (name: "Baidu_MobAds_SDK-release_v5.94", ext: 'aar')//如果支持百度sdk，根据对接详情处理
        compile 'com.android.support:appcompat-v7:28.0.0'

#####2、android标签下加入以下内容，用以引用aar内容

        repositories {
            flatDir {
                dirs 'libs'
            }
        }

###二、proguard-rules.pro混淆配置

        # PAISDK
        -keep class com.mitan.sdk.** { *; }
        -dontwarn com.mitan.sdk.**



###三、Manifest清单文件
#####1、注册Provider

        <provider
            android:name="com.mitan.sdk.clear.MtProvider"
            android:authorities="${applicationId}.mtfiles"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/mtpath" />
        </provider>


mtpath文件放在resource的xml目录下



#####2、权限注册

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.GET_TASKS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

如果您打包 App 时的 targetSdkVersion >= 23：请动态获取到 SDK 要求的所有权限，然后再调用 SDK 的广告接口。

#####3、Gradle文件

        ndk {
            abiFilters "arm64-v8a", "armeabi-v7a"//根据自己app jni支持的架构选择填写
        }

        sourceSets {
            main {
                jniLibs.srcDirs = ['libs']
            }
        }

###四、PAI sdk初始化
#####1、入口类：MtSDK

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|init|1、Application：上下文 <br>2、MtConfigBuilder：配置类 |void|static方法，application onCreate时调用|
|checkPermission|Activity：Activity实例 |void|static方法，需要时调用|
|setOaid|1、Context：上下文 <br>2、String：oaid字符串 |void|static方法，android10及以上手机需要设置|

#####2、初始化配置类 MtConfigBuilder

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|enableMultiProcess|1、boolean：是否支持多进程 |MtConfigBuilder| |
|withLog|1、boolean：是否显示log |MtConfigBuilder|log tag "PEXIN_SDK" |
|disallowGeoInfo|1、boolean：关闭地理位置信息 |MtConfigBuilder| 建议不要关闭 |
|disallowPhoneState|1、boolean：关闭手机基本信息 |MtConfigBuilder| 建议不要关闭 |
|disableSDKSafeMode|关闭sdk保护模式 |MtConfigBuilder| 在保护模式下，可以避免大部分常见的错误引起的崩溃，建议正式上线不要关闭保护模式，测试阶段可以关闭 |
|setToken|String |MtConfigBuilder| 在后台(http://ssp.xwuad.com/)  <font color=#FF0000>注册后自动生成身份令牌 token, 必填！</font> 否则无法获取广告，在后台“账户设置”中查看token|

######请在您的 Application 类的 onCreate() 回调中调用如下方法初始化 SDK。
例如：

MtConfigBuilder config = new MtConfigBuilder();
        config.setToken("请填入您的身份令牌 token");    //  <font color=#FF0000>必填! 否则无法获取广告</font>
        config.enableMultiProcess(false);
MtSDK.init(this, config);

###五、PAI sdk 开屏广告
#####1、入口类MtSplash

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|MtSplash|1、Activity：上下文 <br>2、String：开屏类型位置id <br>3. ViewGroup: 开屏容器 <br>4、MtSplashListener：开屏行为回调 |      |初始化方法，默认加载并展示开关为true|
|MtSplash|1、Activity：上下文 <br>2、String：开屏类型位置id <br>3. ViewGroup: 开屏容器 <br>4. boolean: 加载并展示开关 <br>5、MtSplashListener：开屏行为回调 |      |初始化方法，可传入加载并展示开关|
|load|                    	 | void |加载并展示广告，对应加载并展示开关为true，注意区分！！！|
|fetchOnly|                    	 | void |获取广告，对应加载并展示开关为false，注意区分！！！|
|showAd|                    	 | ViewGroup |在容器中展示广告，对应加载并展示开关为false，注意区分！！！|
|setDLInfoListener|                    	 | MtDLInfoListener |设置下载动作回传接口，在load动作之前调用（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|fetchDownloadInfo|                    	 | DLInfoCallback |获取下载信息回传接口，在MtDLInfoListener回调动作触发后调用（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|onDestroy|                  	 | void |Activity生命周期结束时调用销毁|

#####2、行为回调接口类MtSplashListener

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|onAdLoaded|                | void |加载成功行为|
|onPresented|                | void |展示行为|
|onFailed| MtError：广告错误   | void |加载失败|
|onClicked|                  | void |点击行为|
|onDismiss|                  | void |完成行为|
|onTick| long：剩余时间        | void |回调剩余时间|
|onExposed|                  | void |回调曝光行为|

例如：

        private void initSplash(ViewGroup adContainer, final Activity context){
            isClicked = false;
            msplash = new MtSplash(this, MyApplication.getSplashId(), mAdContainer, new MtSplashListener() {
                @Override
                public void onPresented() {
                    Toast.makeText(context, "广告展示", Toast.LENGTH_LONG).show();
                }

                @Override
                public void onFailed(MtError error) {
                    Toast.makeText(context, "广告失败: "+error.getErrorMessage(), Toast.LENGTH_LONG).show();
                }

                @Override
                public void onDismiss() {
                    Toast.makeText(context, "广告关闭", Toast.LENGTH_LONG).show();
                    context.finish();
                }

                @Override
                public void onClicked() {
                    isClicked = true;
                    Toast.makeText(context, "广告点击", Toast.LENGTH_LONG).show();
                }
            });//开屏测试
            msplash.setDLInfoListener(new MtDLInfoListener() {
        
                @Override
                public void onDownloadConfirm(Context activity, final MtDLConfirmCallback mtDLConfirmCallback) {
                    data.fetchDownloadInfo(new DLInfoCallback() {

                        @Override
                        public void infoLoaded(ApkInfo s) {
                            Log.e("testpex", "fetch download info->"+s);
                        }
                    });
                    showApkInfoDialog(info, new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            if(mtDLConfirmCallback != null){
                                mtDLConfirmCallback.confirm();//确认下载
                            }
                        }
                    }, new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            if(mtDLConfirmCallback != null){
                                mtDLConfirmCallback.cancel();//取消下载
                            }
                        }
                    });
                }
            });

            msplash.load();
        }



###六、PAI sdk 激励视频广告
#####1、MtReward

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|MtReward|1、Activity：上下文 <br>2、String：激励视频类型位置id <br>3、RewardActionListener：激励视频行为回调                       |      |初始化方法|
|loadAd|                    	 | void |加载激励视频广告|
|showAd|                    	 | void |显示激励视频广告，onAdLoaded回调之后才能调用|
|setDLInfoListener|                    	 | MtDLInfoListener |设置下载动作回传接口，在load动作之前调用（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|fetchDownloadInfo|                    	 | DLInfoCallback |获取下载信息回传接口，在MtDLInfoListener回调动作触发后调用（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|onDestroy|                  	 | void |Activity生命周期结束时调用销毁|

#####2、行为回调接口类RewardActionListener

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|onAdLoaded|                       | void |加载成功|
|onAdFailed| MtError：广告错误       | void |加载失败|
|onAdExposed|                      | void |展示行为|
|onAdClicked|                      | void |点击行为|
|onAdClosed|                       | void |关闭广告行为|
|onRewards|                        | void |收到激励|
|onAdError|  MtError：广告错误                      | void |广告出错|

如果不需要这么多的接口回调可以继承AbstractRewardActionListener选择自己需要的接口重写。

例如：

	MtReward mAdData;

    protected void loadRewardAd() {
        mAdData = new MtReward(this, MyApplication.getRewardId(), new MtRewardListener() {
            @Override
            public void onAdLoaded() {
                Toast.makeText(TestRewardActivity.this, "reward ad loaded", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onAdFailed(MtError error) {
                Toast.makeText(TestRewardActivity.this, "reward ad failed: "+error.getErrorMessage(), Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onAdExposed() {
                Toast.makeText(TestRewardActivity.this, "reward ad exposed", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onAdClicked() {
                Toast.makeText(TestRewardActivity.this, "reward ad clicked", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onAdClosed() {
                Toast.makeText(TestRewardActivity.this, "reward ad closed", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onRewards() {
                Toast.makeText(TestRewardActivity.this, "reward ad rewarded", Toast.LENGTH_SHORT).show();
            }


            @Override
            public void onAdError(MtError error) {
                Toast.makeText(TestRewardActivity.this, "reward ad error: "+error.getErrorMessage(), Toast.LENGTH_SHORT).show();
            }
        });
        mAdData.setDLInfoListener(new MtDLInfoListener() {
    
            @Override
            public void onDownloadConfirm(Context activity, final MtDLConfirmCallback mtDLConfirmCallback) {
                data.fetchDownloadInfo(new DLInfoCallback() {

                    @Override
                    public void infoLoaded(ApkInfo s) {
                        Log.e("testpex", "fetch download info->"+s);
                    }
                });
                showApkInfoDialog(info, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        if(mtDLConfirmCallback != null){
                            mtDLConfirmCallback.confirm();//确认下载
                        }
                    }
                }, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        if(mtDLConfirmCallback != null){
                            mtDLConfirmCallback.cancel();//取消下载
                        }
                    }
                });
            }
        });
        
    }

    public void showAd(View v){
        if(mAdData != null) mAdData.showAd();
    }

    public void loadAd(View v){
        if(mAdData != null) mAdData.loadAd();
    }



###七、PAI sdk 原生广告
#####1、广告获取入口类MtNativeLoader

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|MtNativeLoader|1、Activity：上下文 |      |初始化方法|
|setDownloadConfirmStatus|int: 下载再确认状态						 | void |配合DOWNLOAD_NO_CONFIRM，DOWNLOAD_CONFIRM_DEFAULT使用，加载前调用|
|setVideoPlayStatus|int: 视频自动播放状态						 | void |配合VIDEO_AUTO_PLAY，VIDEO_MANU_PLAY使用，加载前调用|
|load| 1、String：原生广告位置id <br>2、MtActionListener：原生广告获取行为回调 | void |加载原生广告|
|load| 1、String：原生广告位置id <br>2、int：加载平台广告的个数 <br>2、MtActionListener：原生广告获取行为回调  | void |加载原生广告|
|onResume|						 | void |Activity生命周期onResume的时候调用|
|onDestroy|                  	 | void |Activity生命周期结束时调用销毁|

#####2、原生广告加载行为回调MtLoadListener

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|adLoaded| List< MtNativeInfo >：加载的原生广告列表  | void |加载成功|
|loadFailed| MtError：加载失败                      | void |加载失败|


#####3、原生广告加载行为回调MtActionListener

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|onExposure|   | void |加载成功|
|onClick|   | void |加载失败|
|onError| MtError：广告错误  | void |加载失败|
|onStatusChange|   | void |广告状态变化|



#####4、原生广告视频行为回调MtMediaListener

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|onVideoPause|   | void |播放暂停|
|onVideoResume|   | void |播放恢复|
|onVideoStart|   | void |播放开始|
|onVideoComplete|   | void |播放完成|
|onVideoError|    | void |视频错误|


#####5、原生广告数据MtNativeInfo

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|getInfoType|                   | int            |获取数据类型|
|getTitle|                      | String         |获取标题|
|getDesc|                       | String          |获取描述|
|getIcon|                       | String          |获取图标链接|
|getMark|                       | String          |获取广告平台图标链接|
|getDesc|                       | String          |获取描述|
|getCovers|                     | List< String > |获取主图链接列表|
|getMainCover|                  | String | 获取单个主图链接 |
|getAppStatus|                  | int | 获取app型数据下载状态 |
|getDlProcess|                  | int | 获取app型数据下载进度 |
|getPosterType|                 | int | 获取主图类型 |
|getPosterWidth|                 | int | 获取主图宽度 |
|getPosterHeight|                 | int | 获取主图高度 |
|setDLInfoListener|                    	 | MtDLInfoListener |设置下载动作回传接口（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|fetchDownloadInfo|                    	 | DLInfoCallback |获取下载信息回传接口，在MtDLInfoListener回调动作触发后调用（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|pauseDownload|                    	 | void |暂停下载|
|resumeDownload|                    	 | void |取消暂停下载|
|bindAdView|1、 View：放置所有原生广告渲染的视图的容器(注意不要给这个View setOnClickListener，否则会影响点击响应) <br> 2、List<View>: 广告接收点击的View列表 (注意不要给这个View setOnClickListener，否则会影响点击响应)|  View | 绑定广告视图，把返回的视图动态add到layout布局上面准备好的父容器中 |
|bindAdView|1、 View：放置所有原生广告渲染的视图的容器(注意不要给这个View setOnClickListener，否则会影响点击响应) <br> 2、List<View>: 广告接收点击的View列表 (注意不要给这个View setOnClickListener，否则会影响点击响应)<br>3.FrameLayout.LayoutParam logo位置设置|  View | 绑定广告视图，把返回的视图动态add到layout布局上面准备好的父容器中 |
|getMediaView|Context：上下文| View | 当getPosterType等于MtConstant.COVER_VERTICAL_VID和MtConstant.COVER_HORIZON_IMG时获取视频渲染视图|
|onResume| | void | 当页面onResume的时候调用|
|setNativeActionListener|MtActionListener：广告行为监听| void | |
|setMediaListener|MtMediaListener：视频广告行为| void | |
|destroy| | void | 销毁广告 |
|startVideo| | void | 播放视频广告 |
|resumeVideo| | void | 继续播放被暂停的视频广告 |
|pauseVideo| | void | 暂停视频广告的播放 |
|stopVideo| | void | 停止视频广告播放 |

例如：

	    private void loadNativeAd(final Activity activity, String placeid, ViewGroup adContainer){
        
            MtNativeLoader mFactory = new MtNativeLoader(activity);
            mFactory.load(placeid, new MtLoadListener(){
                @Override
                public void adLoaded(List<MtNativeInfo> list) {
                    if(list != null&& list.size()>0){
                        Toast.makeText(TestNativeActivity.this, "native ad loaded = >"+list.size(), Toast.LENGTH_SHORT).show();
                        for(int i=0; i<list.size(); i++){
                            list.get(i).setDLInfoListener(new MtDLInfoListener() {
                        
                                @Override
                                public void onDownloadConfirm(Context activity, final MtDLConfirmCallback mtDLConfirmCallback) {
                                    data.fetchDownloadInfo(new DLInfoCallback() {
    
                                        @Override
                                        public void infoLoaded(ApkInfo s) {
                                            Log.e("testpex", "fetch download info->"+s);
                                        }
                                    });
                                    showApkInfoDialog(info, new DialogInterface.OnClickListener() {
                                        @Override
                                        public void onClick(DialogInterface dialog, int which) {
                                            if(mtDLConfirmCallback != null){
                                                mtDLConfirmCallback.confirm();//确认下载
                                            }
                                        }
                                    }, new DialogInterface.OnClickListener() {
                                        @Override
                                        public void onClick(DialogInterface dialog, int which) {
                                            if(mtDLConfirmCallback != null){
                                                mtDLConfirmCallback.cancel();//取消下载
                                            }
                                        }
                                    });
                                }
                            });
                            View v = renderView(list.get(i));
                            adContainer.addView(v);
                        }
                    }else{
                        Toast.makeText(TestNativeActivity.this, "native ad failed", Toast.LENGTH_SHORT).show();
                    }
                }

                @Override
                public void loadFailed(MtError loadError) {
                    Toast.makeText(TestNativeActivity.this, "native ad failed: "+loadError.getErrorMessage(), Toast.LENGTH_SHORT).show();
                }
            });
        }

	    private void renderAdView(ViewGroup mContainer, MtNativeInfo data){
            data.setNativeActionListener(new MtActionListener() {
                @Override
                public void onExposure() {
                    Toast.makeText(c, "native ad on expose", Toast.LENGTH_SHORT).show();
                }

                @Override
                public void onClick() {
                    Toast.makeText(c, "native ad on click", Toast.LENGTH_SHORT).show();
                }

                @Override
                public void onError(MtError mtError) {
                    Toast.makeText(c, "native ad on error"+mtError.getErrorMessage(), Toast.LENGTH_SHORT).show();
                }
            });
            data.setMediaListener(new MtMediaListener() {
                @Override
                public void onVideoStart() {
                    Toast.makeText(c, "native ad video start", Toast.LENGTH_SHORT).show();
                }

                @Override
                public void onVideoPause() {
                    Toast.makeText(c, "native ad video pause", Toast.LENGTH_SHORT).show();
                }

                @Override
                public void onVideoResume() {
                    Toast.makeText(c, "native ad video resume", Toast.LENGTH_SHORT).show();
                }

                @Override
                public void onVideoComplete() {
                    Toast.makeText(c, "native ad video complete", Toast.LENGTH_SHORT).show();
                }

                @Override
                public void onVideoError(MtError mtError) {
                    Toast.makeText(c, "native ad video error"+mtError.getErrorMessage(), Toast.LENGTH_SHORT).show();
                }
            });

            //创建广告视图
            ViewGroup mAdRoot = (ViewGroup) LayoutInflater.from(c).inflate(R.layout.finish_dialog_ad, null);
            CompactImageView mAdPoster = mAdRoot.findViewById(R.id.ad_preview);
            ViewGroup mVideoContainer = mAdRoot.findViewById(R.id.video_container);

            //选择需要绑定点击事件的视图
            List<View> mCreaters = new ArrayList<>();
            mCreaters.add(mAdPoster);
            mCreaters.add(mAdRoot);
            mCreaters.add(mVideoContainer);

            //渲染
            mAdPoster.setImageUrl(data.getMainCover());

            //设置广告容器的LayoutParam
            ViewGroup.LayoutParams lp = mContainer.getLayoutParams();
            float density = c.getResources().getDisplayMetrics().density;
            lp.height = (int)(200*density);
            mContainer.setLayoutParams(lp);
            mContainer.requestLayout();

            //设置logo的LayoutParam
            FrameLayout.LayoutParams mLayoutParam = null;
            if(mLayoutParam == null) {
                float dens = c.getResources().getDisplayMetrics().density;
                mLayoutParam = new FrameLayout.LayoutParams(0, 0);
                mLayoutParam.gravity = Gravity.RIGHT | Gravity.BOTTOM;
            }

            //绑定视图
            View content = data.bindAdView(mAdRoot, mCreaters, mLayoutParam);

            //把视图添加到页面
            if(content.getParent() != mContainer){
                mContainer.removeAllViews();
                if(content.getParent() != null) ((ViewGroup) content.getParent()).removeAllViews();
                mContainer.addView(content);
            }

            //设置视频类型的视图
            if(data.getPosterType() == MtConstant.COVER_HORIZON_VID||
                    data.getPosterType() == MtConstant.COVER_VERTICAL_VID){
                View media = data.getMediaView(c);
                if(media.getParent() != mVideoContainer){
                    mVideoContainer.removeAllViews();
                    if(media.getParent() != null) ((ViewGroup)media.getParent()).removeAllViews();
                    mVideoContainer.addView(media);
                }
                if(mVideoContainer.getVisibility() == View.GONE) mVideoContainer.setVisibility(View.VISIBLE);
            }else{
                mVideoContainer.setVisibility(View.GONE);
            }
            mContainer.setVisibility(View.VISIBLE);
            return mContainer;
        }


###八、PAI sdk banner广告
#####1、入口类MtBanner

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|MtBanner|1、Activity：上下文 <br>2、String：开屏类型位置id <br>3.int:自动刷新时间间隔(单位，秒，0代表不自动刷新)<br>4. ViewGroup: banner容器 <br>5、MtBannerListener：Banner行为回调 |      |初始化方法|
|setDownloadConfirmStatus|int: 下载再确认状态						 | void |配合DOWNLOAD_NO_CONFIRM，DOWNLOAD_CONFIRM_DEFAULT使用，加载前调用|
|load|                    	 | void |加载广告|
|setDLInfoListener|                    	 | MtDLInfoListener |设置下载动作回传接口，在load动作之前调用（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|fetchDownloadInfo|                    	 | DLInfoCallback |获取下载信息回传接口，在MtDLInfoListener回调动作触发后调用（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|onDestroy|                  	 | void |Activity生命周期结束时调用销毁|

#####2、行为回调接口类MtBannerListener

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|onReceived|                | void |填充行为|
|onPresented|                | void |展示行为|
|onAdFailed| MtError：广告错误   | void |加载失败|
|onClicked|                  | void |点击行为|

例如：

        private void initBannerAd(ViewGroup bannerContainer, final Activity activity){
            if(mBanner == null) {
                mBanner = new MtBanner(activity, MyApplication.getBannerId(), 0, bannerContainer, new MtBannerListener() {
                    @Override
                    public void onAdReceived() {
                        Toast.makeText(activity, "广告填充", Toast.LENGTH_LONG).show();
                    }
                    @Override
                    public void onAdPresented() {
                        Toast.makeText(activity, "广告展示", Toast.LENGTH_LONG).show();
                    }
                    @Override
                    public void onAdFailed(MtError error) {
                        Toast.makeText(activity, "广告失败: " + error.getErrorMessage(), Toast.LENGTH_LONG).show();
                    }

                    @Override
                    public void onClicked() {
                        Toast.makeText(activity, "广告点击", Toast.LENGTH_LONG).show();
                    }
                });
            }
            mBanner.setDLInfoListener(new MtDLInfoListener() {
                                    
                @Override
                public void onDownloadConfirm(Context activity, final MtDLConfirmCallback mtDLConfirmCallback) {
                    data.fetchDownloadInfo(new DLInfoCallback() {

                        @Override
                        public void infoLoaded(ApkInfo s) {
                            Log.e("testpex", "fetch download info->"+s);
                        }
                    });
                    showApkInfoDialog(info, new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            if(mtDLConfirmCallback != null){
                                mtDLConfirmCallback.confirm();//确认下载
                            }
                        }
                    }, new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            if(mtDLConfirmCallback != null){
                                mtDLConfirmCallback.cancel();//取消下载
                            }
                        }
                    });
                }
            });
            mBanner.load();
            Log.e("testbanner", "refresh banner===================>");
        }

###九、PAI sdk 插屏广告
#####1、MtInterstitial

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|MtInterstitial|1、Activity：上下文 <br>2、String：插屏类型位置id <br>3、MtInterstitialListener：广告加载行为回调                       |      |初始化方法|
|load|                    	 | void |加载插屏广告|
|show|                    	 | void |显示插屏广告|
|setDLInfoListener|                    	 | MtDLInfoListener |设置下载动作回传接口，在load动作之前调用（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|fetchDownloadInfo|                    	 | DLInfoCallback |获取下载信息回传接口，在MtDLInfoListener回调动作触发后调用（只适用于集成广电通4.333版本之后，没有集成则不会生效）|
|onDestroy|                  	 | void |Activity生命周期结束时调用销毁|

#####2、广告加载行为回调MtInterstitialListener

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|onReceive|                       | void |加载成功|
|onExposure|                      | void |展示行为|
|onClicked|                      | void |点击行为|
|onClosed|                       | void |关闭广告行为|
|onError|  MtError：广告错误                      | void |广告出错|

#####3、广告视频行为回调MtInterstitialMediaListener

|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|onVideoReady|    videoDuration：是视频素材的时间长度，单位为 ms                   | void |视频加载成功|
|onVideoStart|                      | void |视频开始播放|
|onVideoPause|                      | void |视频暂停|
|onVideoComplete|                       | void |视频播放结束|
|onVideoError|  MtError：广告错误                      | void |视频播放时出现错误|

<strong><font color=#FF0000>注意：</font>您需要处理好 Activity 的运行时变更，由于视频广告可以跟随手机屏幕的方向旋转和全屏播放，请处理好 Activity 的运行时变更（最简单的方式就是在 AndroidManifest 文件中给您的 Activity 加上 android:configChanges="keyboard|keyboardHidden|orientation|screenSize" 属性），不要让播放视频广告的 Activity 被销毁重建。</strong>

例如：

    public class TestInterstitialActivity extends AppCompatActivity implements MtInterstitialListener, MtInterstitialMediaListener {

    MtInterstitial interstitial;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ......

        interstitial = new MtInterstitial(this, MyApplication.getNativeId(), this);
        interstitial.setDLInfoListener(new MtDLInfoListener() {
                                
            @Override
            public void onDownloadConfirm(Context activity, final MtDLConfirmCallback mtDLConfirmCallback) {
                data.fetchDownloadInfo(new DLInfoCallback() {

                    @Override
                    public void infoLoaded(ApkInfo s) {
                        Log.e("testpex", "fetch download info->"+s);
                    }
                });
                showApkInfoDialog(info, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        if(mtDLConfirmCallback != null){
                            mtDLConfirmCallback.confirm();//确认下载
                        }
                    }
                }, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        if(mtDLConfirmCallback != null){
                            mtDLConfirmCallback.cancel();//取消下载
                        }
                    }
                });
            }
        });
    }

    public void loadInter(View v) {
        if (interstitial == null)
            return;
        interstitial.load();
    }

    public void showInter(View v) {
        if (interstitial == null) {
            return;
        }
        interstitial.show();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (interstitial != null)
            interstitial.onDestroy();
    }

    @Override
    public void onReceive() {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onReceive", Toast.LENGTH_SHORT).show();
        if (interstitial == null) {
            return;
        }
        interstitial.setMediaListener(this); 
        
    }

    @Override
    public void onExposure() {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onExposure", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onClicked() {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onClicked", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onClosed() {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onClosed", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onError(MtError mtError) {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onError", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onVideoReady(long l) {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onVideoReady:"+l, Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onVideoStart() {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onVideoStart", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onVideoPause() {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onVideoPause", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onVideoComplete() {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onVideoComplete", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onVideoError(MtError mtError) {
        Toast.makeText(TestInterstitialActivity.this, "Interstitial onVideoError", Toast.LENGTH_SHORT).show();
    }
}

###十、PAI 下载app信息回调
#####1、信息回调类MtDLInfoListener
|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|onDownloadConfirm|1.Activity:下载行为承载活动页<br>2.MtDLConfirmCallback:下载确认回调接口| void |下载行为确认回调|


#####2、下载行为确认回调类MtDLConfirmCallback
|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|confirm|                       | void |确认|
|cancel|                        | void |取消|

#####2、下载行为确认回调类MtDLInfoCallback
|接口方法名|参数|返回值|备注|
|:------:|:------:|:------:|:------:|
|infoLoaded|ApkInfo:app 信息6要素  | void |信息加载成功|


例如：

    public class DownloadHelper {

        public MtDLInfoListener DL_LISTENER = new MtDLInfoListener() {
    
            @Override
            public void onDownloadConfirm(Activity activity, final MtDLConfirmCallback mtDLConfirmCallback) {
                data.fetchDownloadInfo(new DLInfoCallback() {//在onDownloadConfirm回调以后 获取应用信息 6 要素
                    @Override
                    public void infoLoaded(ApkInfo s) {
                        Log.e("testpex", "fetch download info->"+s);
                    }
                });
                ApkInfo info = getAppInfoFromJson(s);
                showApkInfoDialog(info, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        if(mtDLConfirmCallback != null){
                            mtDLConfirmCallback.confirm();//确认下载
                        }
                    }
                }, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        if(mtDLConfirmCallback != null){
                            mtDLConfirmCallback.cancel();//取消下载
                        }
                    }
                });
            }
        };
   
    }

###十一、PAI sdk常量
#####1、常量类MtConstant

|常量名|类型|备注|
|:------:|:------:|:------:|
|INFO_TYPE_NORMAL| int         |对应getInfoType的类型：一般类型|
|INFO_TYPE_APP|    int         |对应getInfoType的类型：app类型|
|COVER_MULTI|    int         |对应getPosterType的类型：样式多|
|COVER_VERTICAL_IMG|    int         |对应getPosterType的类型：竖版图片|
|COVER_HORIZON_IMG|    int         |对应getPosterType的类型：横版图片|
|COVER_VERTICAL_VID|    int         |对应getPosterType的类型：竖版视频|
|COVER_HORIZON_VID|    int         |对应getPosterType的类型：横版视频|
|INFO_APP_NODL|    int         |对应getAppStatus的状态：尚未下载|
|INFO_APP_INSTALLED|    int         |对应getAppStatus的状态：已经安装|
|INFO_APP_DLING|    int         |对应getAppStatus的状态：正在下载|
|INFO_APP_DLED|    int         |对应getAppStatus的状态：已经下载完成|
|INFO_APP_DLFAIL|    int         |对应getAppStatus的状态：下载失败|
|DOWNLOAD_CONFIRM_DEFAULT|    int         |下载再确认默认状态：wifi不用确认，非wifi二次确认|
|DOWNLOAD_NO_CONFIRM|    int         |下载不用再确认|
|VIDEO_AUTO_PLAY|    int         |原生自渲染视频类广告wifi自动播放，非wifi手动播放|
|VIDEO_MANU_PLAY|    int         |原生自渲染视频类广告手动播放|
|SCENES_NATIVE|    int         |下载回调原生场景|
|SCENES_H5|    int         |下载回调h5场景|


###十二、PAI sdk常见错误码
|错误码|错误信息|备注|
|:------:|:------:|:------:|
|1002| 加载超时         |广告返回时间过长|
|1003| 视频错误         |广告视频加载出错|
|1004| 服务端错误返回    |具体看message返回|
|1005| 加载错误         |具体看message|
|1006| 请求量过大         |广告集中请求导致|
|1007| 当前页面不可见         |banner，开屏广告当前页面不可见导致无返回|

### Gradle 修改包名
执行Gradle 脚本命令修改包名 Gradle -q copyFile


```
  复制代码的目录
  def fromPath() {
      return "src/main/java/com/mitan/sdk"
  }
  复制要放到的新目录
  def intoPath() {
      return "src/main/java/com/qqkj/sdk"
  }
  删除以前的目录
  def deletePath() {
      return "vi/src/main/java/com/mitan"
  }
  修改前的package
  def packageName() {
      return "com.mitan.sdk"
  }
  修改后的package
  def toPackageName() {
      return "com.qqkj.sdk"
  }
  修改前的applicationId
  def applicationIds() {
      return "com.mitan.sdk.vi"
  }
  修改后的applicationId
  def toApplicationIds() {
      return "com.qqkj.sdk.vi"
  }
  def proguard_rules(){
      return "proguard-rules.pro"
  }
```

