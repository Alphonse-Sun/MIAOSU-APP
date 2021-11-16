# miaosu
这个推送太复杂了,就直接打包传上来,不推送了!
# 安卓开发环境
Android Studio 
App离线SDK下载：最新android平台SDK下载
3.1.10版本起需要申请Appkey https://nativesupport.dcloud.net.cn/AppDocs/usesdk/appkey
准备工程
方式一：新建工程
打开Android studio新建一个空白项目

![image](https://user-images.githubusercontent.com/94306098/141957598-b4e1a08a-fbb1-409b-b85c-eab7c0283d7e.png)

选择 Empty Activity 点击 Next。

![image](https://user-images.githubusercontent.com/94306098/141957660-3e571d30-4b81-4e37-a8b9-79d0ea2dea6d.png)

Minimum API Level 建议选21及以上

点击 Finish 完成创建。

注意：App离线SDK不支持Kotlin

方式二：导入工程
2.7.0之后提供HBuilder-Integrate-AS工程，可以直接导入HBuilder-Integrate-AS工程，直接运行simpleDemo项目即可。

配置工程
基础库配置

将lib.5plus.base-release.aar、android-gif-drawable-release@1.2.23.aar、uniapp-v8-release.aar和oaid_sdk_1.0.25.aar拷贝到libs目录下

![image](https://user-images.githubusercontent.com/94306098/141957769-b9079d9f-506b-4faf-a748-bb495761243f.png)

在build.gradle中添加资源引用

注意：HBuilderX3.2.5版本之后适配了AndroidX。

AndroidX版本需添加如下资源

 implementation fileTree(include: ['*.jar'], dir: 'libs')
 implementation fileTree(include: ['*.aar'], dir: 'libs')
 implementation 'androidx.appcompat:appcompat:1.0.0'
 implementation 'androidx.legacy:legacy-support-v4:1.0.0'
 implementation 'androidx.recyclerview:recyclerview:1.0.0'
 implementation 'com.facebook.fresco:fresco:2.5.0'
 implementation "com.facebook.fresco:animated-gif:2.5.0"
 implementation 'com.github.bumptech.glide:glide:4.9.0'
 implementation 'com.alibaba:fastjson:1.1.46.android'
Copy to clipboardErrorCopied
support版本需添加如下资源

 implementation fileTree(dir: 'libs', include: ['*.aar', '*.jar'], exclude: [])
 implementation "com.android.support:support-v4:28.0.0"
 implementation "com.android.support:appcompat-v7:28.0.0"
 implementation 'com.android.support:recyclerview-v7:28.0.0'
 implementation 'com.facebook.fresco:fresco:1.13.0'
 implementation "com.facebook.fresco:animated-gif:1.13.0"
 implementation 'com.github.bumptech.glide:glide:4.9.0'
 implementation 'com.alibaba:fastjson:1.1.46.android'
Copy to clipboardErrorCopied
uni-app配置时需要在build.gradle中添加aaptOptions配置

 aaptOptions {  
     additionalParameters '--auto-add-overlay'  
     ignoreAssetsPattern "!.svn:!.git:.*:!CVS:!thumbs.db:!picasa.ini:!*.scc:*~"  
 }
Copy to clipboardErrorCopied
注意：更新SDK时需要同时更新aar/jar文件和build.gradle配置
应用配置

配置Appkey

3.1.10版本起需要申请 Appkey，申请请参考https://nativesupport.dcloud.net.cn/AppDocs/usesdk/appkey

打开Androidmanifest.xml， 导航到Application节点，创建meta-data节点，name为dcloud_appkey，value为申请的AppKey如下：

<application
  ...>
  <meta-data
      android:name="dcloud_appkey"
      android:value="替换为自己申请的Appkey" />
Copy to clipboardErrorCopied
配置应用版本号

![image](https://user-images.githubusercontent.com/94306098/141958082-423f6ba1-3ce3-4f65-abd0-93cbeae570f5.png)
 
versionCode为应用的版本号（整数值），用于各应用市场的升级判断，建议与manifest.json中version -> code值一致

versionName为应用的版本名称（字符串），在系统应用管理程序中显示的版本号，建议与manifest.json中version -> name值一致。

applicationId为应用的包名，一般设置为反向域名，不建议修改。

建议将targetSdkVersion设置为25或以上。

App离线SDK minSdkVersion最低支持21，小于21在部分5.0以下机型上将无法正常使用。

完整版build.gradle可参考如下内容。

apply plugin: 'com.android.application'

android {
  compileSdkVersion 29
  defaultConfig {
      applicationId "com.example.myapplication"
      minSdkVersion 21
      targetSdkVersion 28
      versionCode 1
      versionName "1.0"
      multiDexEnabled true
      ndk {
          abiFilters 'x86', 'armeabi-v7a', 'arm64-v8a'
      }
  }
  compileOptions {
      sourceCompatibility JavaVersion.VERSION_1_8
      targetCompatibility JavaVersion.VERSION_1_8
  }
  buildTypes {
      release {
          minifyEnabled false
          proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
      }
  }
}

dependencies {
  implementation fileTree(dir: 'libs', include: ['*.aar', '*.jar'], exclude: [])
  implementation 'androidx.appcompat:appcompat:1.0.0'
  implementation 'androidx.legacy:legacy-support-v4:1.0.0'
  implementation 'com.alibaba:fastjson:1.1.46.android'
}
Copy to clipboardErrorCopied
注意：如果需要集成的第三方sdk存在so库文件，只需添加armeabi-v7a、arm64-v8a、x86三个文件夹即可，否则会导致在部分手机上无法运行。

配置应用名称

打开app-> src -> main -> res -> values -> strings.xml文件，修改“app_name”字段值，该值为安装到手机上桌面显示的应用名称，建议与manifest.json中name（基础配置中的应用名称）对应。

配置应用启动页及provider节点

将下述内容添加到Androidmanifest.xml的application节点中

注意：新建的项目默认会有一个MainActivity的节点，必须删掉！

注意：为适配折叠屏，需要在PandoraEntryActivity的android:configChanges属性中追加“smallestScreenSize|screenLayout|screenSize”，参考如下配置。

  <activity
      android:name="io.dcloud.PandoraEntry"
      android:configChanges="orientation|keyboardHidden|keyboard|navigation"
      android:label="@string/app_name"
      android:launchMode="singleTask"
      android:hardwareAccelerated="true"
      android:theme="@style/TranslucentTheme"
      android:screenOrientation="user"
      android:windowSoftInputMode="adjustResize" >
      <intent-filter>
          <action android:name="android.intent.action.MAIN" />
          <category android:name="android.intent.category.LAUNCHER" />
      </intent-filter>
  </activity>
  <activity
      android:name="io.dcloud.PandoraEntryActivity"
      android:launchMode="singleTask"
      android:configChanges="orientation|keyboardHidden|screenSize|mcc|mnc|fontScale|keyboard|smallestScreenSize|screenLayout|screenSize"
      android:hardwareAccelerated="true"
      android:permission="com.miui.securitycenter.permission.AppPermissionsEditor"
      android:screenOrientation="user"
      android:theme="@style/DCloudTheme"
      android:windowSoftInputMode="adjustResize">
      <intent-filter>
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <action android:name="android.intent.action.VIEW" />
          <data android:scheme="h56131bcf" />
      </intent-filter>
  </activity>
  <!--provider节点必须添加-->
  <provider
      android:name="io.dcloud.common.util.DCloud_FileProvider"
      android:authorities="${apk.applicationId}.dc.fileprovider"
      android:exported="false"
      android:grantUriPermissions="true">
      <meta-data
          android:name="android.support.FILE_PROVIDER_PATHS"
          android:resource="@xml/dcloud_file_provider" />
  </provider>
Copy to clipboardErrorCopied
注意：${apk.applicationId} 必须替换成当前应用的包名

如果集成离线SDK时需要重写application，必须继承自DCloudApplication，否则会导致SDK中业务逻辑无法正常运行。

将对应的application配置到Androidmanifest.xml中（如下），并添加tools:replace="android:name"以防出现冲突。

  <application  
      android:name="io.dcloud.test.TestApplication"  
      android:icon="@drawable/icon"  
      android:label="@string/app_name"  
      tools:replace="android:name">
  </application>
Copy to clipboardErrorCopied
配置应用图标和启动界面

图标名称

icon.png为应用的图标。

push.png为推送消息的图标。

splash.png为应用启动页的图标。

将icon.png、push.png、splash.png放置到drawable，drawalbe-ldpi，drawable-mdpi，drawable-hdpi，drawable-xhdpi，drawable-xxhdpi文件夹下，不同文件夹下对应不同图片尺寸，
 
  ![image](https://user-images.githubusercontent.com/94306098/141958465-2f69d84c-ae24-4750-adba-515816796e1e.png)
 
修改Androidmanifest.xml中application节点的icon属性为@drawable/icon。如下图：
 
![image](https://user-images.githubusercontent.com/94306098/141958509-df9e1e3a-6b06-47ae-9d90-30341f977c82.png)
 
注意：splash.png在不同设备上显示效果不一样，为防止出现拉伸的状况，建议使用Android studio生成.9.png类型图片
  资源配置

Android studio默认项目没有assets文件夹，需手动创建，创建位置与java目录同级。

创建data文件夹并拷贝资源

将SDK->assets->data文件夹拷贝到刚刚创建的assets文件夹下，如下图：
 
![image](https://user-images.githubusercontent.com/94306098/141958662-b1a2088e-14e0-433e-8650-649e10f11dce.png)
 
注意：SDK升级时，data下资源需要同时更新。

创建apps文件夹并拷贝资源

将HBuilderX中的项目导出，将导出的资源复制到assets->apps下
 
  ![image](https://user-images.githubusercontent.com/94306098/141958785-e5bdab8d-f505-4275-a99c-00e362d12403.png)

修改dcloud_control.xml文件

修改dcloud_control.xml中的appid为拷贝过来的uni-app的id，确保dcloud_control.xml中的appid与manifest.json中的id与文件夹名一致，如下图所示：
  ![image](https://user-images.githubusercontent.com/94306098/141958863-55da4ae2-706e-4028-9e59-7c434137193b.png)
生成自定义基座，需要在根节点下添加debug="true"和syncDebug="true"，如下图：

![image](https://user-images.githubusercontent.com/94306098/141958912-8023d2e1-892c-42fc-aa1b-5bf3f4b29abe.png)
自定义基座支持热更新需要将debug-server-release.aar打入apk中，生成正式包时可以删除。

其他配置

AndroidX 配置

需要在项目根目录下的gradle.properties文件中添加如下内容

 android.useAndroidX=true
 android.enableJetifier=true
  
 # IOS开发环境
 安装 Xcode 12.1 及以上版本
 SDK下载链接: https://pan.baidu.com/s/1efPLjJGCbFa1iMzSOZqyUg 提取码: 49hc
 注意：3.1.10版本起需要申请Appkey，具体请点击链接https://nativesupport.dcloud.net.cn/AppDocs/usesdk/appkey
 配置工程
  找到App离线SDK压缩包，并解压，再进入目录；找到HBuilder-Hello文件夹，并打开原生工程，然后准备配置工程。
Copy to clipboardErrorCopied
uni-app项目
  App离线SDK中的HBuilder-Hello文件夹下的打包工程 是按照uni-app项目来配置的，所以基础配置不需要动。
Copy to clipboardErrorCopied
配置Appkey
3.1.10版本起需要申请 Appkey
 
![image](https://user-images.githubusercontent.com/94306098/141960522-a2d456e4-fc7f-4c65-adc3-9933d1e12973.png)

打开info.plist，创建key为dcloud_appkey，Value选择String类型，内容为申请的AppKey如下： 

注意：需要按照下面的教程将 「应用标识」以及 导入资源教程 中「control.xml 中对应的 appid」 修改为正确的值，否则运行时会还是会提示 appkey 错误；

配置应用标识(Bundle Identifier)
选择左侧应用工程根目录，选中TARGETS下的HBuilder打开工程属性界面，在General下修改Identity的值： 
 
 ![image](https://user-images.githubusercontent.com/94306098/141960572-3a497b57-fb88-4b38-9331-9cb3d43ca990.png)

 其中,Bundle Identifier为苹果的AppID，必须与应用发布时配置的Profile关联的AppID一致；

Version为应用版本号，在App Store中显示的版本号，推荐与manifest.json中version下的name值一致；

Build为编译版本号，App Store判断升级使用，推荐与manifest.json中version下的code值一致。

配置应用名称
1、在打开的原生工程中，点击工程的targets和点开manifest文件，然后将manifest文件里的“name”字段的内容 和原生工程里的Display Name 的写成一样。
 
 ![image](https://user-images.githubusercontent.com/94306098/141960674-f49825f8-0f9f-4c96-b118-e5fda2976c0c.png)

 配置应用版本名称
在打开的原生工程中，点击工程的targets和点开manifest文件，然后将manifest文件里的“version”字段里的“name”的内容 和原生工程里的Version 的写成一样。注意，manifest文件里的“version”字段里的“name” 
 
![image](https://user-images.githubusercontent.com/94306098/141960796-0906f3b8-67d6-4304-b142-996f11884ead.png)




 
