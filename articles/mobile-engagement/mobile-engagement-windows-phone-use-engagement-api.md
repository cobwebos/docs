---
title: "如何在 Windows Phone Silverlight 上使用 Engagement API"
description: "如何在 Windows Phone Silverlight 上使用 Engagement API"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: ae2ba2e8-f75b-4dee-a164-a7dd65d35a23
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8ed91048ecfb1e7d19bb000216618b4e0c4b381


---
# <a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>如何在 Windows Phone Silverlight 上使用 Engagement API
本文档是文档 [如何在 Windows Phone Silverlight 应用中集成 Mobile Engagement](mobile-engagement-windows-phone-integrate-engagement.md) 的补充内容。 它提供了有关如何使用 Engagement API 来报告应用程序统计信息的全部详细信息。

如果只希望 Engagement 报告应用程序的会话、活动、崩溃和技术信息，则最简单的方法是从 `EngagementPage` 类继承所有 `PhoneApplicationPage` 子类。

如需更多报告，例如，如需报告应用程序特定事件、错误和作业，或必须使用不同于在 `EngagementPage` 类中实施的方式报告应用程序的活动，则需使用 Engagement API。

Engagement API 由 `EngagementAgent` 类提供。 可以通过 `EngagementAgent.Instance` 访问这些方法。

即使还未初始化代理模块，对 API 的各个调用会被延迟，并在代理可用后重新执行调用。

## <a name="engagement-concepts"></a>Engagement 概念
以下部分针对 Windows Phone 平台优化 Mobile Engagement 概念。

### <a name="session-and-activity"></a>`Session` 和 `Activity`
*活动*通常与应用程序的一个页面相关联，即，*活动*在页面显示时开始，并在页面关闭时停止：当使用 `EngagementPage` 类对 Engagement SDK 进行集成时，会出现此情况。

但也可通过使用 Engagement API 手动控制*活动*。 这样就可以在若干子部分中拆分给定的页面，以获取有关该页面使用情况的详细信息（例如，了解对话框在该页面内使用的频率和时长）。

## <a name="reporting-activities"></a>报告活动
### <a name="user-starts-a-new-activity"></a>用户启动新活动
#### <a name="reference"></a>引用
            void StartActivity(string name, Dictionary<object, object> extras = null)

需在每次用户活动更改时调用 `StartActivity()`。 对此函数的第一次调用会启动一个新的用户会话。

> [!IMPORTANT]
> 应用程序关闭时，SDK 会自动调用 EndActivity 方法。 因此，每当用户的活动更改时，强烈建议调用 StartActivity 方法且从不调用 EndActivity 方法，因为调用此方法会强制结束当前的会话。
> 
> 

#### <a name="example"></a>示例
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>用户结束其当前活动
#### <a name="reference"></a>引用
            void EndActivity()

需在用户结束其最后活动时至少调用一次 `EndActivity()`。 该调用将通知 Engagement SDK：用户当前处于空闲状态，且用户会话需在会话超时到期时关闭（如果在会话超时到期前调用 `StartActivity()`，则会继续该会话）。

#### <a name="example"></a>示例
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>报告作业
### <a name="start-a-job"></a>启动作业
#### <a name="reference"></a>引用
            void StartJob(string name, Dictionary<object, object> extras = null)

可以使用此作业在一段时间内跟踪特定任务。

#### <a name="example"></a>示例
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>结束作业
#### <a name="reference"></a>引用
            void EndJob(string name)

作业跟踪的任务终止后，应通过提供作业名称对此作业调用 EndJob 方法。

#### <a name="example"></a>示例
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>报告事件
存在三种类型的事件：

* 独立事件
* 会话事件
* 作业事件

### <a name="standalone-events"></a>独立事件
#### <a name="reference"></a>引用
            void SendEvent(string name, Dictionary<object, object> extras = null)

独立事件可在会话的上下文的外部出现。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>会话事件
#### <a name="reference"></a>引用
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

会话事件通常用于报告用户在会话期间执行的操作。

#### <a name="example"></a>示例
**不包含数据：**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**包含数据：**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>作业事件
#### <a name="reference"></a>引用
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

作业事件通常用于报告用户在作业期间执行的操作。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>报告错误
存在三种类型的错误：

* 独立错误
* 会话错误
* 作业错误

### <a name="standalone-errors"></a>独立错误
#### <a name="reference"></a>引用
            void SendError(string name, Dictionary<object, object> extras = null)

与会话错误相反，独立错误可在会话的上下文的外部出现。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>会话错误
#### <a name="reference"></a>引用
            void SendSessionError(string name, Dictionary<object, object> extras = null)

会话错误通常用于报告在用户会话期间影响用户的错误。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>作业错误
#### <a name="reference"></a>引用
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

错误可与运行作业相关，而不是与当前用户会话相关。

#### <a name="example"></a>示例
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>报告崩溃
代理提供两种方法来解决崩溃。

### <a name="send-an-exception"></a>发送异常
#### <a name="reference"></a>引用
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>示例
可以通过调用以下内容在任意时间发送异常：

            EngagementAgent.Instance.SendCrash(aCatchedException);

还可以使用可选参数在发送崩溃的同时终止 Engagement 会话。 为此，请调用：

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

如果执行了以上操作，会话和作业将在发送崩溃后立即关闭。

### <a name="send-an-unhandled-exception"></a>发送未经处理的异常
#### <a name="reference"></a>引用
            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement 还提供了一种发送未经处理的异常的方法。 在 silverlight UnhandledException 事件处理程序内使用该方法时尤其有用。

此方法被调用后，会**始终**终止 Engagement 会话和作业。

#### <a name="example"></a>示例
你可以使用它来实施自己的 UnhandledException 处理程序（尤其是当你禁用了 Engagement 的自动报告崩溃功能时）。 例如，在 `App.xaml.cs` 文件的 `Application_UnhandledException` 方法中：

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code

              EngagementAgent.Instance.SendCrash(e);
            }

## <a name="onactivated"></a>OnActivated
### <a name="reference"></a>引用
            void OnActivated(ActivatedEventArgs e)

当用户向前导航以离开应用程序时，在引发 Deactivated 事件之后，操作系统将尝试使应用程序处于休眠状态。 然后，应用程序在进行逻辑删除。 在该过程中将终止应用程序，但是保留关于应用程序状态和应用程序内的各个页面的部分数据。

当应用程序被逻辑删除时，你必须在 App.xaml.cs 文件的 `Application_Activated` 方法中插入 `EngagementAgent.Instance.OnActivated(e)`，以重置 Engagement 代理。

### <a name="example"></a>示例
            // Inside your App.xaml.cs file

            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

## <a name="device-id"></a>设备 ID
            String GetDeviceId()

可以通过调用此方法获取 Engagement 设备 ID。

## <a name="extras-parameters"></a>其他参数
可将任意数据附加到事件、错误、活动或作业。 可以使用字典将这些数据结构化。 密钥和值可以是任意类型。

其他信息数据已被序列化，因此如果想要在其他数据中插入自己的类型，则必须添加此类型的数据协定。

### <a name="example"></a>示例
我们创建一个新类“Person”。

            using System.Runtime.Serialization;

            namespace Engagement.Agent
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

然后将 `Person` 实例添加到其他信息中。

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> 如果加入其他类型的对象，请确保实现其 ToString() 方法以返回用户可读的字符串。
> 
> 

### <a name="limits"></a>限制
#### <a name="keys"></a>密钥
对象中的每个密钥必须与以下正则表达式匹配：

`^[a-zA-Z][a-zA-Z_0-9]*$`

这意味着，密钥必须以至少一个字母开头，后跟字母、数字或下划线（\_）。

#### <a name="size"></a>大小
其他参数的限制为每次调用 **1024** 个字符。

## <a name="reporting-application-information"></a>报告应用程序信息
### <a name="reference"></a>引用
            void SendAppInfo(Dictionary<object, object> appInfos)

可使用 SendAppInfo() 函数手动报告跟踪信息（或任何其他应用程序特定信息）。

请注意，这些信息可以增量方式发送：仅为给定设备保留给定密钥的最新值。 如同事件其他信息一样，使用字典\<对象、对象\>附加信息。

### <a name="example"></a>示例
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>限制
#### <a name="keys"></a>密钥
对象中的每个密钥必须与以下正则表达式匹配：

`^[a-zA-Z][a-zA-Z_0-9]*$`

这意味着，密钥必须以至少一个字母开头，后跟字母、数字或下划线（\_）。

#### <a name="size"></a>大小
应用程序信息仅限于每次调用 **1024** 个字符。

在上一示例中，发送到服务器的 JSON 长度为 44 个字符：

            {"subscription":"2013-12-07","premium":"true"}

## <a name="logging"></a>日志记录
### <a name="enable-logging"></a>启用日志记录
可以将 SDK 配置为在 IDE 控制台中生成测试日志。
默认情况下不启用这些日志。 若要对其自定义，请将属性 `EngagementAgent.Instance.TestLogEnabled` 更新为 `EngagementTestLogLevel` 枚举中提供的值之一，例如：

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();



<!--HONumber=Nov16_HO3-->


