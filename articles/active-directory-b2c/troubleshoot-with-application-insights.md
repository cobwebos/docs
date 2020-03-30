---
title: 使用应用程序见解解决自定义策略
titleSuffix: Azure AD B2C
description: 如何设置应用程序见解以跟踪自定义策略的执行。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186261"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>使用应用程序见解收集 Azure 活动目录 B2C 日志

本文提供了从活动目录 B2C（Azure AD B2C） 收集日志的步骤，以便您可以诊断自定义策略的问题。 Application Insights 提供了一种方法来诊断异常和直观显示应用程序性能问题。 Azure AD B2C 包含用于将数据发送到应用程序见解的功能。

此处描述的详细活动日志**应仅在**自定义策略的开发期间启用。

> [!WARNING]
> 请勿在生产中启用开发模式。 日志收集发送到标识提供程序并从标识提供程序发送的所有声明。 作为开发人员，您负责在应用程序见解日志中收集的任何个人数据。 仅当策略置于**开发人员模式**中时，才会收集这些详细的日志。

## <a name="set-up-application-insights"></a>设置应用程序见解

如果还没有，请在订阅中创建应用程序见解实例。

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 选择顶部菜单中的**目录 + 订阅**筛选器，然后选择包含 Azure 订阅的目录（而不是 Azure AD B2C 目录）。
1. 选择 **"在**左侧导航菜单中创建资源"。
1. 搜索并选择 **"应用程序见解**"，然后选择 **"创建**"。
1. 完成表单，选择 **"审阅 + 创建**"，然后选择 **"创建**"。
1. 部署完成后，选择 **"转到资源**"。
1. 在 **"在**应用程序见解"菜单中配置，选择 **"属性**"。
1. 记录用于后续步骤的 **"仪器"密钥**。

## <a name="configure-the-custom-policy"></a>配置自定义策略

1. 打开依赖方 （RP） 文件，例如 *"登录或Signin.xml*"。
1. 将以下属性添加到 `<TrustFrameworkPolicy>` 元素：

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. 如果不存在，请向`<UserJourneyBehaviors>``<RelyingParty>`节点添加子节点。 它必须立即位于 之后`<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`。
1. 将以下节点添加为 `<UserJourneyBehaviors>` 元素的子级。 请确保`{Your Application Insights Key}`替换为前面记录的应用程序见解**检测密钥**。

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`告诉应用程序见解通过处理管道加快遥测速度。 有利于开发，但受量限制。
    * `ClientEnabled="true"`发送用于跟踪页面视图和客户端错误的应用程序洞察客户端脚本。 您可以在"应用程序见解"门户中的**浏览器计时**表中查看这些内容。 通过设置`ClientEnabled= "true"`，您将应用程序见解添加到页面脚本中，并获取页面加载和 AJAX 调用的计时、计数、浏览器异常和 AJAX 失败的详细信息以及用户和会话计数。 此字段是**可选的**，默认情况下设置为`false`。
    * `ServerEnabled="true"` 将现有 UserJourneyRecorder JSON 作为自定义事件发送到 Application Insights。

    例如：

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

1. 上传该策略。

## <a name="see-the-logs-in-application-insights"></a>在 Application Insights 中查看日志

在应用程序见解中看到新日志之前，有一个短暂的延迟（通常不到五分钟）。

1. 打开在 [Azure 门户](https://portal.azure.com)中创建的 Application Insights 资源。
1. 在 **"概述"** 菜单中，选择 **"分析**"。
1. 在 Application Insights 中打开新选项卡。

下面是可用于查看日志的查询列表：

| 查询 | 描述 |
|---------------------|--------------------|
`traces` | 查看 Azure AD B2C 生成的所有日志 |
`traces | where timestamp > ago(1d)` | 查看 Azure AD B2C 为前一天生成的所有日志

条目可能较长。 导出到 CSV 进行更深入的了解。

有关查询的详细信息，请参阅 Azure[监视器 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>后续步骤

社区已开发一个用户旅程查看器来帮助标识开发人员。 它将读取 Application Insights 实例，并提供用户旅程事件的有序视图。 可以获取源代码并将其部署在自己的解决方案中。

用户旅程播放器不受 Microsoft 的支持，并且严格按现有身份提供。

您可以在 GitHub 上查找从应用程序见解读取事件的查看器版本，如下所示：

[Azure-采样/主动目录-b2c 高级策略](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
