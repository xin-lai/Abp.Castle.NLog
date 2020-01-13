# Magicodes.Abp.Castle.NLog

Abp的NLog日志输出模块。
已支持.NET Core 3.1,Abp 5.1.0

forked from woaisoft/Abp.Castle.NLog

----------------------

## Nuget Packages ##
| 名称     |      Nuget      |
|----------|:-------------:|
| Magicodes.Abp.Castle.NLog  |  [![NuGet](https://buildstats.info/nuget/Magicodes.Abp.Castle.NLog)](https://www.nuget.org/packages/Magicodes.Abp.Castle.NLog) |


----------------------

## 开始使用
1. 使用Nuget安装Magicodes.Abp.Castle.NLog
2. 配置[nlog.config](doc/nlog.config) 文件，可下载直接使用。或者参考以下配置：

````xml
<?xml version="1.0" encoding="utf-8"?>

<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      autoReload="true"
      internalLogLevel="Warn"
      internalLogFile="App_Data\Logs\nlogs.log">

  <!-- 定义日志输出的根目录为web目录的上级目录 -->
  <variable name="logdir" value="${basedir}/App_Data/logs"/>

  <targets async="true">

    <default-target-parameters
      type="File"
      archiveAboveSize="50485760"
      maxArchiveFiles="50"
      archiveNumbering="Rolling"
      keepFileOpen="false"
      layout="${date:format=HH\:mm\:ss\:ffff}:[${level}] ${callsite} ${onexception:${exception:format=tostring} ${newline}${stacktrace}${newline}"/>

    <!--屏幕彩色打印消息-->
    <target name="console" xsi:type="ColoredConsole"
            layout="${date:format=HH\:mm\:ss\:ffff}:[${level}] ${message}"/>

    <!--默认日志-->
    <target xsi:type="File" name="defaultLog" fileName="${logdir}/${level}/${shortdate}.log" layout="${date:format=HH\:mm\:ss\:ffff}: ${message} ${onexception:${exception:format=tostring} ${newline}${stacktrace}${newline}" />

    <target name="warnLog" xsi:type="File"
            fileName="${logdir}/${level}/${shortdate}.log"
            layout="${date:format=HH\:mm\:ss\:ffff}:  ${logger}${newline}${message} ${onexception:${exception:format=tostring} ${newline}${stacktrace}${newline}" />

    <target xsi:type="WebService"
            name="wsLog"
            url="https://monitor.xin-lai.com/Application/08231710-365D-4E7D-8C66-CA4417E47450"
            protocol="JsonPost"
            encoding="UTF-8">
      <parameter name="Project" type="System.String" layout="Magicodes.Admin.Core" />
      <parameter name="Branch" type="System.String" layout="Develop" />
      <parameter name="Level" type="System.String" layout="${level}" />
      <!--${date:format=yyyy-MM-dd HH\:mm\:ss.fff}-->
      <parameter name="Time" type="System.String" layout="${longdate}" />
      <parameter name="Message" type="System.String" layout="${message}" />
      <parameter name="Callsite" type="System.String"
                 layout="${callsite:className=True:fileName=True:includeSourcePath=True:methodName=True}" />
      <parameter name="Detail" type="System.String"
                 layout="${onexception:inner=${newline}${exception:format=tostring}}" />
      <parameter name="Stacktrace" type="System.String" layout="${stacktrace}" />
    </target>
  </targets>
  <rules>
    <logger name="*" levels="Trace,Debug,Info" writeTo="console,defaultLog" />
    <logger name="*" minlevel="Warn" writeTo="console,warnLog" />
  </rules>
</nlog>
````

3. 修改启动类：Startup.cs

        - 将原来的日志组件log4net替换为nlog
        - 注释“using Abp.Castle.Logging.Log4Net; ”，然后添加“using Abp.Castle.Logging.NLog;”
        - 修改ConfigureServices方法:`f => f.UseAbpNLog().WithConfig("nlog.config")`

具体可以参考以下代码：

````C#
                    //配置日志
                    options.IocManager.IocContainer.AddFacility<LoggingFacility>(
                        f =>
                        {
                            var logType = _appConfiguration["Abp:LogType"];
                            _logger.LogInformation($"LogType:{logType}");
                            if (logType != null && logType == "NLog")
                            {
                                f.UseAbpNLog().WithConfig("nlog.config");
                            }
                            else
                            {
                                f.UseAbpLog4Net().WithConfig("log4net.config");
                            }
                        });
````

## 联系我们

> #### 订阅号

关注“麦扣聊技术”订阅号可以获得最新文章、教程、文档：

![](./res/wechat.jpg "麦扣聊技术")

> #### QQ群

- 编程交流群<85318032>

- 产品交流群<897857351>

> #### 文档官网&官方博客

- 文档官网：<https://docs.xin-lai.com/>
- 博客：<http://www.cnblogs.com/codelove/>


> #### 其他开源库

- <https://github.com/xin-lai>
- <https://gitee.com/magicodes>
