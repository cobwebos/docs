---
title: "Azure 通知中心 - 常见问题 (FAQ)"
description: "关于设计/实现有关通知中心的解决方案的常见问题"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: "推送通知, 推送通知, iOS 推送通知, android 推送通知, ios 推送, android 推送"
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9f0e7f071f1aa8fdd95c4959eae884afc60644e9
ms.lasthandoff: 03/25/2017


---
# <a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>使用 Azure 通知中心推送通知 — 常见问题
## <a name="general"></a>常规
### <a name="0--what-is-the-resource-structure-of-notification-hubs"></a>0.通知中心的资源结构是怎样的？

通知中心有两个资源级别：中心和命名空间。 中心是单个推送资源，可以保存一个应用的跨平台推送信息。 命名空间是一个区域中所有中心的集合。

通常推荐的映射使用一个命名空间匹配一个应用。 在命名空间内，可具有适用于生产应用的生产中心、适用于测试应用的测试中心等。

### <a name="1--what-is-the-price-model-for-notification-hubs"></a>1.通知中心的价格模型有哪些？
可以在[通知中心定价]页面找到最新的详细信息。 通知中心是按照命名空间级别（请参阅以上资源结构，了解命名空间的级别）计费的，并提供以下三层：

* **免费层** - 这是浏览推送功能的良好起点，不推荐用于生产应用。 每个命名空间每月可获得 500 台设备和 100 万次推送，但不具有 SLA 保证。
* **基本层** - 该层或标准层建议用于较小型的生产应用。 以每个命名空间每月可获得 200K 台设备和 1000 万次推送作为基准，具有配额增长选项。
* **标准层** - 建议用于中型、大型生产应用。 以每个命名空间每月可获得 10M 台设备和 1000万次推送作为基准，具有配额增长选项，再加上丰富的遥测功能。

以下是一些出色的标准层功能：
* *丰富的遥测功能* - 通知中心提供消息遥测来跟踪所有推送请求和用于调试的平台通知系统反馈。
* *多租户* - 可在命名空间级别与平台通知系统 (PNS) 凭据一起使用。 这样可轻松地将租户拆分到相同命名空间内的中心。
* *计划推送* - 可以计划随时发出通知。

### <a name="2--what-is-the-notification-hubs-sla"></a>2.什么是通知中心 SLA？
对于**基本**和**标准**通知中心层，就在受支持的层内部署的通知中心而言，我们保证至少在 99.9% 的情况下，正确配置的应用程序将能够发送推送通知或执行注册管理操作。 若要详细了解我们的 SLA，请访问[通知中心 SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) 页面。

> [!NOTE]
> 由于推送通知取决于第三方平台通知系统（Apple 的 APNS、Google 的 FCM 等），所以这些消息的发送不具有 SLA 保证。 通知中心将这些发送批处理到 PNS（具有 SLA 保证）后，PNS 负责完成推送（不具有 SLA 保证）。

### <a name="3--which-customers-are-using-notification-hubs"></a>3.哪些客户在使用通知中心？
我们有许多客户在使用通知中心，以下列出了几位知名客户：

* Sochi 2014 — 数百个兴趣组、300 多万台设备、2 周内发出的通知超过了 1.5 亿条。 [案例研究 - Sochi]
* Skanska - [案例研究 - Skanska]
* Seattle Times - [案例研究 - Seattle Times]
* Mural.ly - [案例研究 - Mural.ly]
* 7Digital - [案例研究 - 7Digital]
* 必应应用 — 数千万台设备，每天发送 300 万条以上的通知。

### <a name="4-how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>4.如何将中心升级或降级到不同层的命名空间？
转到 [Azure 门户]的通知中心命名空间或通知中心，单击要更新的资源，在导航中转到“定价层”。 可以更新到所需的任何层，但请注意：
* 更新后的定价层将应用于正在使用的命名空间中的所有中心。
* 如果要降级，并且设备数超出所要降级到的层的限制，则需要删除设备以满足限制的要求才能进行降级。


## <a name="design--development"></a>设计和开发
### <a name="1--which-server-side-platforms-do-you-support"></a>1.支持哪些服务器端平台？
我们的服务器 SDK 适用于 .NET、Java、Node.js、PHP 和 Python。 此外，通知中心 API 基于 REST 接口，因此如果要处理不同的平台或不希望有其他依赖项，可选择直接通过 REST API 操作。 在 [NH - REST API] 页面，可以看到更多详细信息。

### <a name="2--which-client-platforms-do-you-support"></a>2.支持哪些客户端平台？
我们支持将推送通知发送到 [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md)、[Android](notification-hubs-android-push-notification-google-gcm-get-started.md)、[Windows 通用](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)、[Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md)、[Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)、[Android China（通过百度）](notification-hubs-baidu-china-android-notifications-get-started.md)、Xamarin（[iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)）、[Chrome 应用](notification-hubs-chrome-push-notifications-get-started.md)和 [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari)。 有关详细信息，请访问[入门教程]页面。

### <a name="3--do-you-support-smsemailweb-notifications"></a>3.是否支持 SMS/电子邮件/Web 通知？
通知中心的设计目的主要是将通知发送到移动应用，不提供电子邮件或短信的功能。 但提供这些功能的第三方平台可以与通知中心进行集成，以通过使用 [Azure 移动应用]发送原生推送通知。

通知中心也不提供现成的浏览器内推送通知传递服务。 客户可以在支持的服务器端平台上，选择使用 SignalR 来实现此功能。 如果想要在 Chrome 沙盒中将通知发送到浏览器应用，请查看 [Chrome 应用教程]。

### <a name="4----what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4.  Azure Mobile Apps 与 Azure 通知中心之间的关系如何？它们各自适用于什么场合？
如果你有现成的移动应用后端并且只想添加发送推送通知的功能，则可以使用 Azure 通知中心。 如果你想要从头开始安装移动应用后端，那么你应当考虑使用 Azure Mobile Apps。 Azure Mobile Apps 会自动预配通知中心，以便你能够轻松地从移动应用后端发送推送通知。 Azure Mobile Apps 的定价包括通知中心的基本费用，你只需在超出所含推送时支付费用。 可以在[应用服务定价]页面找到有关成本的更多详细信息。

### <a name="5----how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5.  如果通过通知中心发送推送通知，可以支持多少个设备？
有关支持的设备数目的详细信息，请参阅[通知中心定价]页面。

在某些情况下，如果需要支持超过 10,000,000 台已注册的设备，请直接[与我们联系](https://azure.microsoft.com/overview/contact-us/)，我们将帮你扩展解决方案。

### <a name="6----how-many-push-notifications-can-i-send-out"></a>6.  我可以发送多少推送通知？
Azure 会根据系统中通过的通知数量自动向上扩展，具体取决于所选的层。

> [!NOTE]
> 整体使用成本可能会根据目前提供的推送通知数量而增加。 请务必留意[通知中心定价]页面上概述的现有层的限制。
> 
> 

我们现有的客户每天使用通知中心发送数百万条推送通知。 只要使用 Azure 通知中心，你就不需要执行任何特别的操作来调整推送通知可及范围。

### <a name="7----how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7.  已发送的推送通知到达我的设备需要多长时间？
在正常使用情况下，Azure 通知中心能够在 1 分钟内处理至少 **100 万次推送通知发送**，其中传入负载相当一致，本质上不会出现猛增现象。 此速率可能因标记数、传入发送的性质以及其他外部因素而有所不同。

在预计的传递时间内，服务能计算每个平台的目标，并根据注册的标记/标记表达式将消息路由到各自的推送通知传递服务。 然后，由推送通知服务 (PNS) 负责将通知发送到设备。

PNS 对于传递通知不保证任何 SLA；但是，一般情况下，绝大多数推送通知会在发送到我们的平台之后的数分钟内（通常在 10 分钟的限制内）传递到目标设备。 可能存在少量可能需要更多时间的异常消息。

> [!NOTE]
> Azure 通知中心有一个策略，可删除任何不能在 30 分钟内传递到 PNS 的推送通知。 这一延迟的出现原因有多种，最常见的是因为 PNS 限制了你的应用程序。
> 
> 

### <a name="8----is-there-any-latency-guarantee"></a>8.  是否有任何延迟保证？
推送通知的特性（由平台特定的外部推送通知服务传递）导致没有延迟保证。 通常情况下，大部分推送通知会在几分钟内完成传递。

### <a name="9----what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9.  我在设计包含命名空间和通知中心的解决方案时需要考虑哪些因素？
#### <a name="mobile-appenvironment"></a>移动应用/环境
* 每个环境中的每个移动应用只应有一个通知中心。
* 在多租户方案中，每个租户都应具有一个单独的中心。
* 你不能在测试与生产环境之间共享同一通知中心，因为这可能导致发送通知时出现问题。 例如，Apple 提供沙盒和每个都具有单独凭据的生产推送终结点。
* 默认情况下，你可以通过 Azure 门户或 Visual Studio 中的 Azure 集成组件将测试通知发送到已注册的设备。 阈值设置为从注册池中随机选取的 10 个设备。

> [!NOTE]
> 如果中心最初的配置使用的是 Apple 沙盒证书，而后又使用 Apple 生产证书进行了重新配置，则旧的设备令牌将和新证书一起失效，导致推送失败。 将生产和测试环境分开，针对不同的环境使用不同的中心是最佳做法。
> 
> 

#### <a name="pns-credentials"></a>PNS 凭据
将移动应用注册到某个平台的开发人员门户（如Apple 或 Google 等）后，你会获得应用标识符和安全令牌，应用后端需要向平台的推送通知服务提供这些信息才能将推送通知发送到设备。 必须在通知中心配置这些安全令牌，其形式可以是证书（例如，对于 Apple iOS 或 Windows Phone）或安全密钥（Google Android、Windows 等等）。 这通常在通知中心级别完成，在多租户方案中，也可在命名空间级别完成。

#### <a name="namespaces"></a>命名空间
命名空间可用于部署分组。  在多租户方案中，还可以用它来表示同一应用的所有租户的所有通知中心。

#### <a name="geo-distribution"></a>地理分布
在推送通知方案中，地理分布并非总是关键所在。 请注意，各种最终将推送通知传递至设备的推送通知服务（如 APNS、GCM 等）并不是均匀分布的。

如果你有一个在全球范围内使用的应用程序，那么你可以在不同的命名空间内创建若干中心，以利用全球不同 Azure 区域中可用的通知中心服务。

> [!NOTE]
> 这将增加管理成本，尤其是注册方面，因此不建议如此，仅在明确需要时采用此方法。
> 
> 

### <a name="10----should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10.  我该从应用后端注册还是直接通过客户端设备注册？
在创建注册之前，当你必须进行客户端身份验证时，或者，当你拥有必须根据某些应用逻辑由应用后端创建或修改的标记时，从应用后端进行注册很有用。 有关详细信息，可以阅读[后端注册指南]与[后端注册指南 - 2] 页面深入了解。

### <a name="11----what-is-the-push-notification-delivery-security-model"></a>11.  什么是推送通知传递安全模型？
Azure 通知中心使用基于[共享访问签名 (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md) 的安全模型。 你可以在根命名空间级别或细粒度通知中心级别使用 SAS 令牌。 这些 SAS 令牌可以使用不同的授权规则进行设置，如发送消息权限、侦听通知权限等。可以在 [NH 安全模型]文档中找到更多详细信息。

### <a name="12----how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12.  我如何处理推送通知中的敏感负载？
所有通知都由平台的推送通知服务 (PNS) 传递到目标设备。 当发件人将通知发送到 Azure 通知中心后，我们会对通知进行处理并传递到各自的 PNS。

从发送方到 Azure 通知中心、而后再到 PNS 的所有连接都使用 HTTPS。

> [!NOTE]
> Azure 通知中心不会以任何方式记录消息的负载。
> 
> 

但是，对于发送敏感负载，建议使用安全推送模式，在该模式下，发送者将带有消息标识符的“ping”通知传递到设备，其中不包含敏感负载，当设备上的应用接收到此负载后，它能够直接调用安全 API 来提取消息详细信息。 可以在 [NH - 安全推送教程]页面找到有关如何实现上述模式的指南。

## <a name="operations"></a>操作
### <a name="1----what-is-the-disaster-recovery-dr-story"></a>1.  什么是灾难恢复 (DR) 情景？
我们这边提供元数据灾难恢复范围（通知中心名称、连接字符串和其他重要信息）。 触发 DR 场景后，注册数据是通知中心基础结构中会丢失的**唯一片段**。 你必须实施解决方案，将此数据重新填充到恢复后的新中心。

* *步骤 1* - 在另一个数据中心创建辅助通知中心。 你可以在发生 DR 事件时动态创建此辅助中心，也可以在一开始就创建一个。 无论选择哪一个选项都没有很大的差别，因为通知中心预配是一个相对较快的过程，大约只需要数秒的时间。 一开始就创建好辅助中心将能避免 DR 事件影响到你的管理能力，因此强烈建议你这样做。
* *步骤 2* - 使用主通知中心的注册信息冻结辅助通知中心。 建议不要尝试同时在两个中心上维护注册信息，并在收到注册信息时在两个中心之间动态同步，由于注册信息会在 PNS 端到期这一固有性质，这个方式通常效果不佳。 在我们收到 PNS 关于过期的或无效注册的反馈后，通知中心会将这些注册信息清除。  

建议使用具有以下功能之一的应用后端：

* 在其一端维护一组给定的注册信息，以便其可以在发生 DR 时将这些信息批量插入辅助通知中心

**或**

* 从主中心获取注册信息的常规转储作为备份，然后批量插入辅助 NH。

> [!NOTE]
> [注册信息导出/导入]文档中介绍了标准层中可用的注册信息导出/导入功能。
> 
> 

如果你没有后端，则当应用在任何目标设备上启动时，它们将在辅助通知中心执行新注册，最后，辅助通知中心将拥有所有已注册的活动设备。

缺点是设备上的应用未启动期间将不会接收通知。

### <a name="2----is-there-any-audit-log-capability"></a>2.  是否有任何审核日志功能？
有关所有通知中心管理操作，请转到 [Azure 经典门户]中公开的操作日志。

## <a name="monitoring--troubleshooting"></a>监视与故障排除
### <a name="1----what-troubleshooting-capabilities-are-available"></a>1.  故障排除功能有哪些？
Azure 通知中心提供多种可执行常见故障排除的功能，特别是在有关已删除通知的最常见情况中。 有关详细信息，请参阅 [NH - 故障排除]白皮书。

### <a name="2----what-telemetry-features-are-available"></a>2.  遥测功能有哪些？
Azure 通知中心支持在 [Azure 经典门户]中查看遥测数据。 可以在 [NH - 指标]页面找到有关可用指标的详细信息。

> [!NOTE]
> 成功的通知仅表示推送通知已传递到外部推送通知服务（例如 Apple 的 APNS、Google 的 GCM 等等）。 由 PNS 负责将通知传递到目标设备。 PNS 通常不会向第三方公开传递指标。  
> 
> 

我们还提供了以编程方式导出遥测数据的功能（在**标准**层）。 有关详细信息，请参阅 [NH - 指标示例]。

[Azure 经典门户]: https://manage.windowsazure.com
[通知中心定价]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[案例研究 - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[案例研究 - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[案例研究 - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[案例研究 - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[案例研究 - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - REST API]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Getting Started Tutorials]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome 应用教程]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[后端注册指南]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[后端注册指南 - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH 安全模型]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - 安全推送教程]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - 故障排除]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - 指标]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - 指标示例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[注册信息导出/导入]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure 门户]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Azure 移动应用]: https://azure.microsoft.com/services/app-service/mobile/
[应用服务定价]: https://azure.microsoft.com/pricing/details/app-service/

