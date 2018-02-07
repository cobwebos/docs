---
title: "Azure Application Insights 中的数据保留和存储 | Microsoft Docs"
description: "保留和隐私政策声明"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: mbullwin
ms.openlocfilehash: 1818e564acb0e9b5fa620d6f38db141811ca9777
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Application Insights 中的数据收集、保留和存储


在应用中安装 [Azure Application Insights][start] SDK 后，它会将有关应用的遥测数据发送到云中。 负责的开发人员自然想要确切了解发送了哪些数据、数据的后续情况，以及如何控制数据。 具体而言，是否发送了敏感数据？数据存储在何处？其安全性怎样？ 

首先，简短的答案是：

* “按原样”运行的标准遥测模块不太可能将敏感数据发送到服务。 遥测考虑到负载、性能和使用指标、异常报告和其他诊断数据。 诊断报告中显示的主要用户数据是 URL；但是，应用在任何情况下都不应该将敏感数据以明文形式放在 URL 中。
* 可以编写发送其他自定义遥测数据的代码，帮助进行诊断与监视使用情况。 （这种可扩展性是 Application Insights 的突出特性之一）。在编写此代码时，有可能不小心包含个人数据和其他敏感数据。 如果应用程序可处理此类数据，则应对编写的所有代码进行彻底审查。
* 开发和测试应用时，可以轻松检查 SDK 发送的内容。 数据会显示在 IDE 和浏览器的调试输出窗口中。 
* 数据保存在美国的 [Microsoft Azure](http://azure.com) 服务器中。 （但应用可在任何位置运行）。Azure 有[严格的安全过程，并符合各种法规标准](https://azure.microsoft.com/support/trust-center/)。 只有你和指定的团队可以访问数据。 Microsoft 工作人员只会在知情的情况下和受限的具体情况下，才对数据拥有受限的访问权限。 数据在传输时经过加密，但在服务器中不会加密。

本文的余下部分详细阐述上述答案。 本文的内容简单直白，因此，可以将其转达给不属于直属团队的同事。

## <a name="what-is-application-insights"></a>什么是 Application Insights？
[Azure Application Insights][start] 是 Microsoft 提供的一项服务，可帮助改进实时应用程序的性能和可用性。 它在应用程序运行时全程进行监视，包括测试期间以及发布或部署之后。 Application Insights 可创建图表和表格来显示多种信息，例如，一天中的哪些时间用户最多、应用的响应能力如何，以及应用依赖的任何外部服务是否顺利地为其提供服务。 如果出现崩溃、故障或性能问题，可以搜索详细的遥测数据来诊断原因。 此外，如果应用的可用性和性能有任何变化，服务会向你发送电子邮件。

要获取此功能，需在应用程序中安装 Application Insights SDK，该 SDK 将成为应用程序代码的一部分。 当应用运行时，SDK 将监视其操作，并将遥测发送到 Application Insights 服务。 这是 [Microsoft Azure](http://azure.com) 托管的云服务。 （不过，Application Insights 适用于任何应用程序，而不只是 Azure 中托管的应用程序）。

![应用中的 SDK 将遥测数据发送到 Application Insights 服务。](./media/app-insights-data-retention-privacy/01-scheme.png)

Application Insights 服务存储并分析遥测数据。 若要查看分析或搜索已存储的遥测数据，可以登录到 Azure 帐户并打开应用程序的 Application Insights 资源。 还可以与团队的其他成员或指定的 Azure 订户共享数据访问权限。

可以从 Application Insights 服务导出数据，例如，导出到数据库或外部工具。 需要为每项工具提供从服务获取的特殊密钥。 如果需要，可以吊销该密钥。 

Application Insights SDK 可用于多种应用程序类型：托管在自己的 J2EE 或 ASP.NET 服务器中或者 Azure 中的 Web 服务；Web 客户端 - 即网页中运行的代码；桌面应用和服务；设备应用，例如 Windows Phone、iOS 和 Android。 它们都将遥测数据发送到相同的服务。

## <a name="what-data-does-it-collect"></a>它收集哪些数据？
### <a name="how-is-the-data-is-collected"></a>它如何收集数据？
有三种数据源：

* SDK。可以[在开发阶段](app-insights-asp-net.md)或者[在运行时](app-insights-monitor-performance-live-website-now.md)将它与应用集成。 不同类型的应用程序有不同的 SDK。 此外还有[网页 SDK](app-insights-javascript.md)，连同页面一起加载到用户的浏览器中。
  
  * 每个 SDK 有许多[模块](app-insights-configuration-with-applicationinsights-config.md)，这些模块使用不同的技术收集不同类型的遥测数据。
  * 如果在开发环境中安装 SDK，则除了使用标准模块发送自己的遥测数据以外，还可以使用 SDK 的 API 发送这些数据。 这些自定义遥测数据可以包含所要发送的任何数据。
* 在某些 Web 服务器中，还装有与应用一起运行并发送有关 CPU、内存和网络占用量的遥测数据的代理。 例如，Azure VM、Docker 主机和 [J2EE 服务器](app-insights-java-agent.md)都可能有这种代理。
* [可用性测试](app-insights-monitor-web-app-availability.md)是 Microsoft 运行的过程，可定期将请求发送到 Web 应用。 结果将发送到 Application Insights 服务。

### <a name="what-kinds-of-data-are-collected"></a>收集哪些类型的数据？
主要类别如下：

* [Web 服务器遥测数据](app-insights-asp-net.md) - HTTP 请求。  URI、处理请求花费的时间、响应代码、客户端 IP 地址。 会话 ID。
* [网页](app-insights-javascript.md) - 页面、用户和会话计数。 页面加载时间。 异常。 Ajax 调用。
* 性能计数器 - 内存、CPU、IO、网络占用量。
* 客户端和服务器上下文 - OS、区域性、设备类型、浏览器和屏幕分辨率。
* [异常](app-insights-asp-net-exceptions.md)和崩溃 - **堆栈转储**、版本 ID、CPU 类型。 
* [依赖项](app-insights-asp-net-dependencies.md) - 对外部服务的调用，例如 REST、SQL、AJAX。 URI 或连接字符串、持续时间、成功结果、命令。
* [可用性测试](app-insights-monitor-web-app-availability.md) - 测试持续时间、步骤、响应。
* [跟踪日志](app-insights-asp-net-trace-logs.md)和[自定义遥测](app-insights-api-custom-events-metrics.md)  -  **在日志或遥测中编写的任何内容**。

[更多详细信息](#data-sent-by-application-insights)。

## <a name="how-can-i-verify-whats-being-collected"></a>如何验证收集了哪些信息？
如果使用 Visual Studio 开发应用，请在调试模式下运行应用 (F5)。 遥测数据会显示在“输出”窗口中。 在该窗口中，可以复制遥测数据并将其格式设置为 JSON 以便于检查。 

![](./media/app-insights-data-retention-privacy/06-vs.png)

“诊断”窗口中还提供了一个可方便阅读的视图。

针对网页，请打开浏览器的调试窗口。

![按 F12 打开“网络”选项卡。](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>是否可以编写代码来筛选遥测数据，然后将它发送出去？
可以编写[遥测处理器插件](app-insights-api-filtering-sampling.md)来实现此目的。

## <a name="how-long-is-the-data-kept"></a>数据保留多长时间？
原始数据点（即，可以在 Analytics 中查询，在“搜索”中检查的项）保留 90 天。 如果需要将数据保留 7 天以上，可以使用[连续导出](app-insights-export-telemetry.md)将它复制到存储帐户。

1 分钟粒度的聚合数据（即，在指标资源管理器中显示的计数、平均值和其他统计信息）可保留 90 天。

## <a name="who-can-access-the-data"></a>谁可以访问该数据？
你和团队成员（如果使用组织帐户）可以看到数据。 

你和团队成员可以导出数据，还可以将其复制到其他位置并传递给其他人员。

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Microsoft 如何处理应用发送到 Application Insights 的信息？
Microsoft 只使用这些数据来向你提供服务。

## <a name="where-is-the-data-held"></a>数据保存在哪个位置？
* 在美国、欧洲或东南亚。 创建新的 Application Insights 资源时，可以选择存储位置。 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>这是否意味着必须在美国、欧洲或东南亚托管我的应用？
* 不会。 应用程序可在任何位置运行，不管是在自己的本地主机中还是云中。

## <a name="how-secure-is-my-data"></a>数据的安全性如何？
Application Insights 是一项 Azure 服务。 [Azure Security, Privacy, and Compliance white paper](http://go.microsoft.com/fwlink/?linkid=392408)（Azure 安全性、隐私性和遵从性白皮书）中介绍了安全政策。

数据存储在 Microsoft Azure 服务器中。 对于 Azure 门户中的帐户，将实施 [Azure Security, Privacy, and Compliance document](http://go.microsoft.com/fwlink/?linkid=392408)（Azure 安全性、隐私性和遵从性白皮书）中所述的帐户限制。

Microsoft 工作人员对数据的访问将受到限制。 我们只有在获得许可以及为了帮助你使用 Application Insights 而有必要访问时才访问数据。 

跨所有客户应用程序（例如数据速率和平均跟踪大小）的聚合数据用于改善 Application Insights。

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>其他人的遥测数据是否会干扰我的 Application Insights 数据？
他们可以使用检测密钥将附加的遥测数据发送到帐户，而检测密钥在网页的代码中。 如果附加数据过多，指标会错误地呈现应用的性能和使用情况。

如果与其他项目共享代码，请务必删除检测密钥。

## <a name="is-the-data-encrypted"></a>数据是否已加密？
服务器中的数据目前不会加密。

在数据中心之间移动的所有数据都经过加密。

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>从应用程序传输到 Application Insights 服务器的数据是否经过加密？
是。我们使用 https 将数据从几乎所有 SDK（包括 Web 服务器、设备和 HTTPS 网页）发送到门户。 唯一的例外是从纯 HTTP 网页发送的数据。 

## <a name="personally-identifiable-information"></a>个人身份信息
#### <a name="could-personally-identifiable-information-pii-be-sent-to-application-insights"></a>个人身份信息 (PII) 有可能会发送到 Application Insights 吗？
是的，有可能。 

一般准则是：

* 大多数标准遥测数据（即，在未编写任何代码的情况下发送的遥测数据）不包含明确的 PII。 但是，从事件集合的推断可能会识别个人的身份。
* 异常和跟踪消息可能包含 PII
* 自定义遥测数据 - 即，使用 API 以代码编写的调用（例如 TrackEvent）或日志跟踪 - 可能包含选定的任何数据。

本文档末尾的表格包含收集的数据的更详细说明。

#### <a name="am-i-responsible-for-complying-with-laws-and-regulations-in-regard-to-pii"></a>我是否需要负责遵守有关 PII 的法规？
是的。 有责任确保数据的收集与使用符合法规和 Microsoft Online Services 条款。

应该适当地通知客户有关应用程序所收集的数据和数据用法。

#### <a name="can-my-users-turn-off-application-insights"></a>用户是否可以关闭 Application Insights？
无法直接关闭。 我们未提供用户可操作的开关来关闭 Application Insights。

但是，可以在应用程序中实现此类功能。 所有 SDK 都包括关闭遥测收集的 API 设置。 

#### <a name="my-application-is-unintentionally-collecting-sensitive-information-can-application-insights-scrub-this-data-so-it-isnt-retained"></a>我的应用程序无意中收集了敏感信息。 Application Insights 是否可以擦除这些数据，以免将它保留？
Application Insights 不会筛选或删除数据。 应该适当地管理数据，避免将此类数据发送到 Application Insights。

## <a name="data-sent-by-application-insights"></a>Application Insights 发送的数据
不同的平台有不同的 SDK，并且有多个可安装的组件。 （请参阅 [Application Insights - 概述][start]。）每个组件发送不同的数据。

#### <a name="classes-of-data-sent-in-different-scenarios"></a>不同情况下发送的数据类
| 操作 | 收集的数据类（参阅下一表格） |
| --- | --- |
| [将 Application Insights SDK 添加到 .NET Web 项目][greenbrown] |ServerContext<br/>推断<br/>性能计数器<br/>Requests<br/>**异常**<br/>会话<br/>users |
| [在 IIS 上安装状态监视器][redfield] |依赖项<br/>ServerContext<br/>推断<br/>性能计数器 |
| [将 Application Insights SDK 添加到 Java Web 应用][java] |ServerContext<br/>推断<br/>请求<br/>会话<br/>users |
| [将 JavaScript SDK 添加到网页][client] |ClientContext <br/>推断<br/>Page<br/>ClientPerf<br/>Ajax |
| [定义默认属性][apiproperties] |所有标准事件和自定义事件的**属性** |
| [调用 TrackMetricapi][api] |数字值<br/>**属性** |
| [调用跟踪*][api] |事件名称<br/>**属性** |
| [调用 TrackException][api] |**异常**<br/>堆栈转储<br/>**属性** |
| SDK 无法收集数据。 例如： <br/> - 无法访问性能计数器<br/> - 遥测初始值设定项异常 |SDK 诊断 |

有关[适用于其他平台的 SDK][platforms]，请参阅相关文档。

#### <a name="the-classes-of-collected-data"></a>收集的数据类
| 收集的数据类 | 包含（此列表并不详尽） |
| --- | --- |
| **属性** |**任何数据 - 由代码确定** |
| DeviceContext |ID、IP、区域性、设备型号、网络、网络类型、OEM 名称、屏幕分辨率、角色实例、角色名称、设备类型 |
| ClientContext |OS、区域性、语言、网络、窗口分辨率 |
| 会话 |会话 ID |
| ServerContext |计算机名称、区域性、OS、设备、用户会话、用户上下文、操作 |
| 推断 |IP 地址中的地理位置、时间戳、OS、浏览器 |
| 度量值 |指标名称和值 |
| 活动 |事件名称和值 |
| PageViews |URL 和页面名称或屏幕名称 |
| 客户端性能 |URL/页面名称、浏览器加载时间 |
| Ajax |从网页到服务器的 HTTP 调用 |
| Requests |URL、持续时间、响应代码 |
| 依赖项 |类型（SQL、HTTP...）、连接字符串或 URI、同步/异步、持续时间、成功结果、SQL 语句（包含状态监视器） |
| **异常** |类型、**消息**、调用堆栈、源文件与行号、线程 ID |
| 崩溃 |进程 ID、父进程 ID、崩溃线程 ID；应用程序修补程序、ID、版本；异常类型、地址、原因；模糊符号和寄存器、二进制开始和结束地址、二进制文件名和路径、CPU 类型 |
| 跟踪 |**消息**和严重级别 |
| 性能计数器 |处理器时间、可用内存、请求速率、异常率、进程专用字节、IO 速率、请求持续期间、请求队列长度 |
| 可用性 |Web 测试响应代码、每个测试步骤的持续时间、测试名称、时间戳、成功结果、响应时间、测试位置 |
| SDK 诊断 |跟踪消息或异常 |

可以通过[编辑 ApplicationInsights.config 来关闭某些数据][config]

## <a name="credits"></a>致谢
此产品包含 MaxMind 创建的 GeoLite2 数据，可从 [http://www.maxmind.com](http://www.maxmind.com) 获取。



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

