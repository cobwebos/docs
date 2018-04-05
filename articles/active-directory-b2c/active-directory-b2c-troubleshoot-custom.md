---
title: 使用 Application Insights 排查自定义策略问题 - Azure AD B2C | Microsoft Docs
description: 如何设置 Application Insights 来跟踪自定义策略的执行
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 3e27976b02ee75083b07ca2e4f0c06e19502d1f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C：收集日志

本文提供用于从 Azure AD B2C 收集日志的步骤，以便可以诊断自定义策略存在的问题。

>[!NOTE]
>目前，此处所述的详细活动日志仅用来帮助开发自定义策略。 请勿在生产中使用开发模式。  日志收集在开发过程中发送到标识提供者以及从中发出的所有声明。  如果在生产中使用，则开发人员对他们所拥有的 App Insights 日志中收集的 PII（私人身份信息）负责。  仅当策略处于“开发模式”时才会收集这些详细日志。


## <a name="use-application-insights"></a>使用 Application Insights

Azure AD B2C 支持将数据发送到 Application Insights 的功能。  Application Insights 提供了一种方法来诊断异常和直观显示应用程序性能问题。

### <a name="setup-application-insights"></a>设置 Application Insights

1. 转到 [Azure 门户](https://portal.azure.com)。 确保在具有 Azure 订阅的租户（而不是 Azure AD B2C 租户）中。
1. 在左侧导航菜单中，单击“+ 新建”。
1. 搜索并选择“Application Insights”，单击“创建”。
1. 完成表单，并单击“创建”。 选择“常规”作为“应用程序类型”。
1. 创建资源后，打开 Application Insights 资源。
1. 在左侧菜单中找到“属性”，并单击它。
1. 复制**检测密钥**并将其保存，以便在下一部分中使用。

### <a name="set-up-the-custom-policy"></a>设置自定义策略

1. 打开 RP 文件（例如 SignUpOrSignin.xml）。
1. 将以下属性添加到 `<TrustFrameworkPolicy>` 元素：

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. 如果该元素尚未存在，请将子节点 `<UserJourneyBehaviors>` 添加到 `<RelyingParty>` 节点。 它必须紧接在 `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` 的后面
2. 将以下节点添加为 `<UserJourneyBehaviors>` 元素的子级。 确保将 `{Your Application Insights Key}` 替换为上一部分中从 Application Insights 获取的**检测密钥**。

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` 告知 ApplicationInsights 通过处理管道加快遥测，这样有利于开发，但在量大时会受到约束。
  * `ClientEnabled="true"` 发送用于跟踪页面视图和客户端错误的 ApplicationInsights 客户端脚本（不需要）。
  * `ServerEnabled="true"` 将现有 UserJourneyRecorder JSON 作为自定义事件发送到 Application Insights。
示例：

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. 上传该策略。

### <a name="see-the-logs-in-application-insights"></a>在 Application Insights 中查看日志

>[!NOTE]
> 可以在 Application Insights 中看到新日志之前，有一小段延迟（小于 5 分钟）。

1. 打开在 [Azure 门户](https://portal.azure.com)中创建的 Application Insights 资源。
1. 在“概述”菜单中，单击“分析”。
1. 在 Application Insights 中打开新选项卡。
1. 下面是可用于查看日志的查询列表

| 查询 | 说明 |
|---------------------|--------------------|
traces | 查看 Azure AD B2C 生成的所有日志 |
traces \| where timestamp > ago(1d) | 查看 Azure AD B2C 为前一天生成的所有日志

条目可能较长。  导出到 CSV 进行更深入的了解。

可在[此处](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)了解更多有关 Analytics 工具的信息。

>[!NOTE]
>社区已开发一个用户旅程查看器来帮助标识开发人员。  Microsoft 不支持它，严格按原样提供。  它将读取 Application Insights 实例，并提供用户旅程事件的有序视图。  可以获取源代码并将其部署在自己的解决方案中。

从 Application Insights 读取事件的查看器版本位于[此处](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>目前，此处所述的详细活动日志仅用来帮助开发自定义策略。 请勿在生产中使用开发模式。  日志收集在开发过程中发送到标识提供者以及从中发出的所有声明。  如果在生产中使用，则开发人员对他们所拥有的 App Insights 日志中收集的 PII（私人身份信息）负责。  仅当策略处于“开发模式”时才会收集这些详细日志。

[不支持的自定义策略示例和相关工具的 Github 存储库](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>后续步骤

浏览 Application Insights 中的数据，从而帮助你了解标识体验框架基础 B2C 如何运作来提供你自己的标识体验。
