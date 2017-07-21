---
title: "Azure Application Insights 遥测数据模型 - 遥测上下文 | Microsoft Docs"
description: "Application Insights 遥测上下文数据模型"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/15/2017
ms.author: sergkanz
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: d6a0cad8bda6ca68aa691867e84f540c5ac9f6f3
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="telemetry-context-application-insights-data-model"></a>遥测上下文：Application Insights 数据模型

每个遥测项可能都有一个强类型化上下文字段。 每个字段均会启用特定监视方案。 使用自定义属性集合存储自定义或特定于应用程序的上下文信息。


##<a name="application-version"></a>应用程序版本

应用程序上下文字段中的信息总是与发送遥测的应用程序有关。 应用程序版本用于分析应用程序行为的趋势变化及其与部署的关联。

最大长度：1024


##<a name="client-ip-address"></a>客户端 IP 地址

客户端设备的 IP 地址。 支持 IPV4 和 IPV6。 从服务发送遥测时，位置上下文与在服务中启用操作的用户有关。 Application Insights 从客户端 IP 中提取地理位置信息，然后将其截断。 因此，无法将客户端 IP 本身用作最终用户的可识别信息。 

最大长度：46


##<a name="device-type"></a>设备类型

最初，该字段用于指示应用程序的最终用户使用的设备类型。 现在该字段主要用于区分设备类型为“浏览器”的 JavaScript 遥测与设备类型为“电脑”的服务器端遥测。

最大长度：64


##<a name="operation-id"></a>操作 ID

根操作的唯一标识符。 此标识符允许跨多个组件对遥测分组。 请参阅[遥测关联](application-insights-correlation.md)，了解详细信息。 操作 ID 由请求或页面视图创建。 其他所有遥测将此字段设置为包含请求或页面视图的值。 

最大长度：128


##<a name="parent-operation-id"></a>父操作 ID

遥测项的直接父操作的唯一标识符。 请参阅[遥测关联](application-insights-correlation.md)，了解详细信息。

最大长度：128


##<a name="operation-name"></a>操作名称

操作的名称（组）。 操作名称由请求或页面视图创建。 其他所有遥测项将此字段设置为包含请求或页面视图的值。 操作名称用于查找一组操作的所有遥测项（例如“GET Home/Index”）。 此上下文属性用于回答“此页面的典型异常有哪些？”等问题。

最大长度：1024


##<a name="synthetic-source-of-the-operation"></a>操作的综合源

综合源名称。 来自应用程序的一些遥测可能代表综合流量。 可能是索引网站的 Web 爬网程序、站点可用性测试或诊断库（如 Application Insights SDK 本身）的跟踪。

最大长度：1024


##<a name="session-id"></a>会话 ID

会话 ID - 用户与应用交互的实例。 会话上下文字段中的信息总是与最终用户有关。 从服务发送遥测时，会话上下文与在服务中启用操作的用户有关。

最大长度：64


##<a name="anonymous-user-id"></a>匿名用户 ID

匿名用户 ID。 表示应用程序的最终用户。 从服务发送遥测时，用户上下文与在服务中启用操作的用户有关。

[采样](app-insights-sampling.md)是最小化遥测收集量的一种技术。 采样算法尝试在所有相关遥测内部或外部采样。 匿名用户 ID 用于生成采样得分。 所以匿名用户 ID 应为随机值。 

使用匿名用户 ID 存储用户名是字段误用。 使用已经过身份验证的用户 ID。

最大长度：128


##<a name="authenticated-user-id"></a>已经过身份验证的用户 ID

已经过身份验证的用户 ID。 与匿名用户 ID 相反，此字段表示具有友好名称的用户。 由于其 PII 信息，默认情况下，大多数 SDK 不收集它。

最大长度：1024


##<a name="account-id"></a>帐户 ID

在多租户应用程序中，这是用户使用的帐户 ID 或名称。 例如，Azure 门户的订阅 ID 或博客平台的博客名称。

最大长度：1024


##<a name="cloud-role"></a>云角色

应用程序的角色名称。 直接映射到 Azure 中的角色名称。 也可用于区分单个应用程序包含的微服务。

最大长度：256


##<a name="cloud-role-instance"></a>云角色实例

正在运行应用程序的实例的名称。 本地计算机名称，Azure 实例名称。

最大长度：256


##<a name="internal-sdk-version"></a>内部：SDK 版本

SDK 版本。 请参阅 https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification，了解详细信息。

最大长度：64


##<a name="internal-node-name"></a>内部：节点名称

此字段表示用于计费的节点名称。 使用该字段替代节点的标准检测。

最大长度：256


## <a name="next-steps"></a>后续步骤

- 了解如何[扩展和筛选遥测](app-insights-api-filtering-sampling.md)。
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](application-insights-data-model.md)。
- 查看标准上下文属性集合[配置](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)。

