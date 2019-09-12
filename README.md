# 简介
Mass_Apm Client端sdk，是一款针对Android应用性能问题的自动化低侵入性的收集工具。
# 特性
#### CPU 内存收集
- 收集并统计应用主进程的cpu占用率，得到各场景下cpu峰值和稳定值

#### 内存监控
- 可对Activity Fragment等组件的内存泄漏问题进行监控和统计
- 收集并统计出应用各场景下的内存峰值、稳定值，评价其内存表现

#### 流畅度统计和卡顿信息收集
- 从掉帧数和平均帧率等多个维度，评价场景的流畅度
- 监控和收集app的卡顿堆栈和信息（包括发生卡顿的调用堆栈，以及当时的cpu占用情况）
- 收集和监控app及页面启动速度

## Sdk接入App方法
### app集成方法：
- 1、首先，对插件进行配置,如下所示

在根目录的build.gradle添加插件的配置
```
buildscript {
    
    repositories {
        maven {
            url "http://maven.zuche.com/nexus/content/groups/public/"
        }
    }
    dependencies {
        classpath 'com.sz.ucar.apm:aegis-canary-plugin:+'
    }
}

```
在app/build.gradle 文件中应用插件
```
  apply plugin: 'com.aegis.plugin'

```
对插桩进行配置。
插件是对代码进行插桩，此处可对需要插桩的包进行配置
对满足配置包名的代码插桩后，可以检测造成卡顿等性能问题的函数
```
aegis {
    packageName = [ ] //希望进行插桩的包名，比如: ['com.sz', 'com.zuche'], 此为必填项
}    
```
- 2、然后在app/build.gradle 文件中添加apm模块的依赖，如：
```
dependencies {
  api "com.sz.ucar.apm:aegis-android-lib:+"
  api "com.sz.ucar.apm:aegis-leak-canary:+"
  api "com.sz.ucar.apm:aegis-trace-canary:+"
}

```

- 3、最后，选择程序Application启动的位置进行初始化，如：
```
if (BuildConfig.IS_DEBUG){ //建议只在debug环境下开启
        new Aegis.Builder(this)
            .appType(1) //app类型，用于后台数据统计，使用前向apm组申请
            .isDebug(true) //debug=true下log输出
            .plugin(new TracePlugin()) //涵盖 cpu统计、mem统计、帧率统计、启动速度统计、卡顿侦测、耗电侦测、ANR侦测
            .plugin(new LeakCanaryPlugin())//使用内存leak相关检测
            .build()
            .startAllPlugins();
 }
```
- 至此，Mass-Apm sdk已集成完毕。

### Apm配置方法：
- 1、联系apm组，为应用申请appType，申请的apptype填入初始化代码中
- 2、正确配置数据平台的apm 应用属性开关配置。配置路径  APM -- APM配置 -- 云端配置
- 3、重启并使用app，在数据平台上查看数据。

# APM后台分析系统
### 一、地址：
http://10.101.44.105:3000 （请使用公司内网访问）
登录账户: 优车账户 
### 二、功能：
#### 1、CPU统计
**路径：APM – CPU – 统计结果**
**选择：项目名称，应用版本，采集日期**
##### 查看统计结果（用于性能报告）：
（各页面的CPU峰值，稳定值的统计，是当日采集数据的分析得到的均值）
![](http://wiki.10101111.com/download/attachments/194949403/image2019-6-14_8-58-57.png?version=1&modificationDate=1560473930000&api=v2)

备注：在统计结果页面查看，各场景页面的cpu统计结果（平均值统计），**可直接计入性能报告**。

##### 查看统计详情：
路径：APM – CPU – CPU详情，查看每次进入该页面的CPU的峰值及稳定值。
如下图：
![](http://wiki.10101111.com/download/attachments/194949403/image2019-6-14_9-2-53.png?version=1&modificationDate=1560474165000&api=v2)
还可以通过路径  APM – CPU – 结果验证，查看每次进入页面的CPU走势图。
如下图：
![](http://wiki.10101111.com/download/attachments/194949403/image2019-6-14_9-5-58.png?version=1&modificationDate=1560474350000&api=v2)


#### 2、FPS统计
**路径：APM – CPU – 帧率统计**
**选择：应用类型 应用版本 采集时间**
##### 查看统计结果（用于性能报告）：
(请查看对应场景页面的平均fps值，计入性能报告中)
![](http://wiki.10101111.com/download/attachments/194949403/image2019-6-14_9-16-0.png?version=1&modificationDate=1560474953000&api=v2)


#### 3、内存统计
#### 4、内存泄漏统计
**路径：APM – CPU – 内存泄漏**
**选择：应用类型 应用版本**
##### 查看统计结果（用于性能报告）
请将内存泄漏的gcroot，写入性能报告，并解决。
![](http://wiki.10101111.com/download/attachments/194949403/image2019-6-14_9-22-17.png?version=1&modificationDate=1560475330000&api=v2)


#### APM_sdk采集开关及参数支持云端配置

[云端配置方法](https://gitlab.10101111.com/zeus/openproject/massapm/wikis/Mass-Apm-%E4%BA%91%E7%AB%AF%E9%85%8D%E7%BD%AE%E6%96%B9%E6%B3%95)

![](https://i.imgur.com/GrFKwGW.png)

#### APM_sdk原理分析
[APM整体系统架构图](https://gitlab.10101111.com/zeus/openproject/massapm/wikis/APM%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84%E5%8E%9F%E7%90%86)

[APMsdk原理图](http://wiki.10101111.com/pages/viewpage.action?pageId=190227932)

