---
title: "如何在 Windows 世界上使用用户参与策略 API"
description: "如何在 Windows 世界上使用用户参与策略 API"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: bb501fca-9cfe-4495-81df-b5efd6e0137b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 75fc134a5535e6113331470cf61df9c06eb8e2ab
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-the-engagement-api-on-windows-universal"></a>如何在 Windows 世界上使用用户参与策略 API
本文档是对文档的附加补充[如何在 Windows 通用集成 Engagement](mobile-engagement-windows-store-integrate-engagement.md)： 它提供了有关如何使用用户参与策略 API 来报告应用程序统计信息的详细信息。

请记住，如果你只想参与报告应用程序的会话、 活动、 崩溃和技术信息，然后最简单方法是使所有你`Page`子类都继承自`EngagementPage`类。

如果你想要执行的详细信息，例如，如果你需要报告应用程序特定事件、 错误和作业，或如果你需要以不同的方式报告应用程序的活动，不是一个中实现`EngagementPage`类，则需要使用用户参与策略 API。

参与 API 提供的`EngagementAgent`类。 你可以通过这些方法访问`EngagementAgent.Instance`。

即使尚未初始化的代理模块，每个 API 调用延迟，，并且当代理可用时将再次执行。

## <a name="engagement-concepts"></a>用户参与策略概念
以下部分优化常见[移动用户参与策略概念](mobile-engagement-concepts.md)为通用 Windows 平台。

### <a name="session-and-activity"></a>`Session`和`Activity`
*活动*是通常与应用程序，一页相关联也就是说*活动*时启动页将显示停止关闭页时： 这是这种情况，当 Engagement SDK 集成使用`EngagementPage`类。

但是*活动*通过参与 API 还可手动控制。 这可以拆分若干子部分以获取有关此页 （例如进一步在此页内使用对话框频率和多长时间） 的使用情况的更多详细信息中的给定的页。

## <a name="reporting-activities"></a>报告活动
### <a name="user-starts-a-new-activity"></a>用户启动新的活动
#### <a name="reference"></a>参考
            void StartActivity(string name, Dictionary<object, object> extras = null)

你需要调用`StartActivity()`每次更改用户活动。 对此函数的第一个调用会启动新的用户会话。

> [!IMPORTANT]
> 关闭应用程序时，SDK 将自动调用 EndActivity 方法。 因此，强烈建议调用 StartActivity 方法，每当用户更改，并为从不活动调用 EndActivity 方法中，因为调用此方法强制结束当前会话。
> 
> 

#### <a name="example"></a>示例
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>用户结束其当前活动
#### <a name="reference"></a>参考
            void EndActivity()

这会结束该活动，该会话。 除非您真正了解你正在执行的操作，不应调用此方法。

#### <a name="example"></a>示例
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>报告作业
### <a name="start-a-job"></a>启动作业
#### <a name="reference"></a>参考
            void StartJob(string name, Dictionary<object, object> extras = null)

您可以使用作业来跟踪一段时间内 certains 任务。

#### <a name="example"></a>示例
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>结束将作业
#### <a name="reference"></a>参考
            void EndJob(string name)

只要跟踪作业的任务，已终止，则应通过提供的作业名称为此作业调用 EndJob 方法。

#### <a name="example"></a>示例
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>报告事件
还有三种类型的事件：

* 独立事件
* 会话事件
* 作业事件

### <a name="standalone-events"></a>独立事件
#### <a name="reference"></a>参考
            void SendEvent(string name, Dictionary<object, object> extras = null)

独立事件可能会发生会话的上下文之外。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>会话事件
#### <a name="reference"></a>参考
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

会话事件通常用于报告用户在会话期间执行的操作。

#### <a name="example"></a>示例
**不包含数据：**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**使用数据：**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>作业事件
#### <a name="reference"></a>参考
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

作业事件通常用于报告用户作业期间执行的操作。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>报告错误
有三种类型的错误：

* 独立错误
* 会话错误
* 作业错误

### <a name="standalone-errors"></a>独立错误
#### <a name="reference"></a>参考
            void SendError(string name, Dictionary<object, object> extras = null)

与会话错误相悖独立错误可能发生会话的上下文之外。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>会话错误
#### <a name="reference"></a>参考
            void SendSessionError(string name, Dictionary<object, object> extras = null)

会话错误通常用于报告在会话期间影响用户的错误。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Job Errors
#### <a name="reference"></a>参考
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

可以与正在运行的作业而不是与当前的用户会话正在相关相关错误。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>报告崩溃
该代理提供两种方法来处理故障。

### <a name="send-an-exception"></a>发送异常
#### <a name="reference"></a>参考
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>示例
可以通过调用发送随时异常：

            EngagementAgent.Instance.SendCrash(aCatchedException);

一个可选参数，还可用于终止在比发送在发生崩溃的同时参与会话。 为此，请调用：

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

如果你这样做，会话和作业将关闭发送在发生崩溃之后。

### <a name="send-an-unhandled-exception"></a>发送未处理的异常
#### <a name="reference"></a>参考
            void SendCrash(Exception e)

用户参与策略还提供的方法来发送未经处理的异常，如果你有**禁用**Engagement 自动**崩溃**reporting。 这是在应用程序 UnhandledException 事件处理程序中使用时特别有用。

此方法将**始终**调用后终止参与会话和作业。

#### <a name="example"></a>示例
可用于实现你自己 UnhandledExceptionEventArgs 处理程序。 例如，添加`Current_UnhandledException`方法`App.xaml.cs`文件：

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
            {
               EngagementAgent.Instance.SendCrash(e.Exception,false);
            }

在"公共 App() {}"中的 App.xaml.cs 添加：

            Application.Current.UnhandledException += Current_UnhandledException;

## <a name="device-id"></a>设备 ID
            String EngagementAgent.Instance.GetDeviceId()

可以通过调用此方法获取 engagement 设备 id。

## <a name="extras-parameters"></a>其他功能参数
任意数据可以附加到事件、 错误、 活动或作业。 可使用字典构成这些数据。 键和值可以是任何类型。

其他功能数据进行序列化，因此如果你想要在其他功能插入自己的类型必须添加此类型的数据协定。

### <a name="example"></a>示例
我们创建一个新类"Person"。

            using System.Runtime.Serialization;

            namespace Microsoft.Azure.Engagement
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

然后，我们将添加`Person`到一个额外的实例。

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> 如果将其他类型的对象，请确保实现其 tostring （） 方法以返回人工可读字符串。
> 
> 

### <a name="limits"></a>限制
#### <a name="keys"></a>密钥
对象中的每个键必须匹配以下正则表达式：

`^[a-zA-Z][a-zA-Z_0-9]*$`

这意味着，密钥必须至少一个字母开头后, 跟字母、 数字或下划线 (\_)。

#### <a name="size"></a>大小
其他功能仅限于**1024年**每次调用的字符。

## <a name="reporting-application-information"></a>报告应用程序信息
### <a name="reference"></a>参考
            void SendAppInfo(Dictionary<object, object> appInfos)

你可以手动报告跟踪信息 （或任何其他应用程序特定信息），它使用 SendAppInfo() 函数。

请注意，此数据可以以增量方式发送： 仅给定键的最新值将为给定设备保留。 事件的其他功能，如使用字典\<对象、 对象\>附加数据。

### <a name="example"></a>示例
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
              {
                {"birthdate", "1983-12-07"},
                {"gender", "female"}
              };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>限制
#### <a name="keys"></a>密钥
对象中的每个键必须匹配以下正则表达式：

`^[a-zA-Z][a-zA-Z_0-9]*$`

这意味着，密钥必须至少一个字母开头后, 跟字母、 数字或下划线 (\_)。

#### <a name="size"></a>大小
应用程序信息仅限于**1024年**每次调用的字符。

在前面的示例中，发送到服务器的 JSON 是 44 个字符：

            {"birthdate":"1983-12-07","gender":"female"}

## <a name="logging"></a>Logging
### <a name="enable-logging"></a>启用日志记录
SDK 可以配置为生成在 IDE 控制台测试日志。
默认情况下，不会激活这些日志。 要自定义这种情况，更新属性`EngagementAgent.Instance.TestLogEnabled`中提供的值之一`EngagementTestLogLevel`枚举，例如：

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

