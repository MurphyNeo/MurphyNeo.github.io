---
layout: post
title: AndroidManifest.xml简单介绍
category: android
keywords: android,AndroidManifest.xml,manifest
---

## AndroidManifest.xml

- 本质：AndroidManifest.xml是整个应用的主配置清单文件
- 包含：包括该应用的包名、版本号、组件、权限等信息
- 作用：记录应用的相关的配置信息

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"> 
    <uses-permission />  //请求你的package正常运作所需要赋予的安全许可。可以没有，也可以包含多个
    <permission />  //声明了安全许可来限制哪些程序能使用你的package中的组件和功能
    <permission-tree />  
    <permission-group />  
    <instrumentation />  //声明了用来测试此package或其他package指令组件的代码
    <uses-sdk />  //指定当前应用程序兼容的最低sdk版本号
    <uses-configuration />  
    <uses-feature />  
    <supports-screens />  
    <compatible-screens />  
    <supports-gl-texture />  
      
    <application>     //只能有一个
        <activity>  //用来与用户交互的主要工具，其实就相当于指定手机APP页面
            <intent-filter>  
                <action />  
                <category />  
                <data />  
            </intent-filter>  
            <meta-data />  
        <activity> 
        <activity-alias>  
            <intent-filter> . . . intent-filter>  
            <meta-data />  
        </activity-alias> 
        <service>  //service是能在后台运行任意时间的组件
            <intent-filter> . . . intent-filter>  
            <meta-data/>  
        </service>
        <receiver>  //能使你的application获得数据的改变或者发生的操作
            <intent-filter> . . . intent-filter>  
            <meta-data />  
        </receiver>  
        <provider> //用来管理持久化数据并发布给其他应用程序使用的组件
            <grant-uri-permission />  
            <meta-data />  
        </provider> 
        <uses-library />  
    </application>  
</manifest>
```
常用标签
- 全局（包名、版本信息）

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
	package="com.example.neo.example"
	android:versionCode="1"
	android:versionName="1.0"
	android:minSdkVersion="8"
	android:targetSdkVersion="16"
> 
```

### 组件（四大组件）
- Activity组件(即界面控制器组件)
Android应用中的每一个Activity都必须在这里声明，否则系统将不识别也不执行。常用的属性有：类名android:name,android:label,主题android:theme,加载模式android:launchMode,键盘交互模式android:windowSoftInputMode等

```
android:allowTaskReparenting=["true" | "false"]
android:alwaysRetainTaskState=["true" | "false"]
android:clearTaskOnLaunch=["true"" | "false"]
android:configChanges=[one or more of: "mcc" "mnc" "locale" "touchscreen" "keyboard" "keyboardHidden" "navigation" "orientation" "fontScale"]
android:enabled=["true" | "false"]
android:excludeFromRecents=["true" | "false"]
android:exported=["true" | "false"]
android:finishOnTaskLaunch=["true" | "false"]
android:icon="drawable resource"
android:label="string resource"
android:launchMode=["multiple" | "singleTop" | "singleTask" | "singleInstance"]
android:multiprocess=["true" | "false"]
android:name="string"
android:noHistory=["true" | "false"]
android:permission="string"
android:process="string"
android:screenOrientation=["unspecified" | "user" | "behind" | "landscape" | "portrait" | "sensor" | "nonsensor"]
android:stateNotNeeded=["true" | "false"]
android:taskAffinity="string"
android:theme="resource or theme"
android:windowSoftInputMode=[one or more of: "stateUnspecified" "stateUnchanged" "stateHidden" "stateAlwaysHidden" "stateVisible" "stateAlwaysVisible" "adjustUnspecified" "adjustResize" "adjustPan"]

```

```bash
<activity
	android:name=".MainActivity"
    android:label="@string/app_name"
	>  
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            <meta-data />  
<activity> 
```

- Provider(包含权限控制等)

```
<provider android:authorities="list" 
           android:enabled=["true" | "false"] 
           android:exported=["true" | "false"] 
           android:grantUriPermissions=["true" | "false"] 
           android:icon="drawable resource" 
           android:initOrder="integer" 
           android:label="string resource" 
           android:multiprocess=["true" | "false"] 
           android:name="string" 
           android:permission="string" 
           android:process="string" 
           android:readPermission="string" 
           android:syncable=["true" | "false"] 
           android:writePermission="string" > 
</provider>
```

- Service

```
<service android:enabled=["true" | "false"] 
          android:exported[="true" | "false"] 
          android:icon="drawable resource" 
          android:label="string resource" 
          android:name="string" 
          android:permission="string" 
          android:process="string" > 
</service>

```

- Receiver

```
<receiver android:enabled=["true" | "false"] 
           android:exported=["true" | "false"] 
           android:icon="drawable resource" 
           android:label="string resource" 
           android:name="string" 
           android:permission="string" 
           android:process="string" > 
</receiver>
```

经常用到的标签并不多，不需要死记，只需要了解就可以了，写程序多了自然就会记住了。















