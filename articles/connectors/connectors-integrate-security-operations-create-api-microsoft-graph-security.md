---
title: 集成和管理安全操作-Azure 逻辑应用 & Microsoft Graph 安全性
description: '& Azure 逻辑应用的 Microsoft Graph 安全提高应用的威胁防护、检测和响应'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 845f57d84f49bdd964cc6f61790faff093f59466
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679099"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>通过将安全操作与 Microsoft Graph 安全性和 Azure 逻辑应用集成，提升威胁防护能力

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和 [Microsoft Graph 安全性](https://docs.microsoft.com/graph/security-concept-overview)连接器，可以创建用于集成 Microsoft 安全产品、服务和合作伙伴的自动工作流，从而提升应用的威胁检测、防护和响应能力。 例如，可以创建用于监视和管理 Microsoft Graph 安全性实体（如警报）的 [Azure 安全中心 playbook](../security-center/security-center-playbooks.md)。 下面介绍了 Microsoft Graph 安全性连接器支持的一些方案：

* 按查询或警报 ID 获取警报。 例如，可以获取非常严重的警报列表。
* 更新警报。 例如，可以更新警报分配、向警报添加注释或标记警报。
* 通过创建[警报订阅 (Webhook)](https://docs.microsoft.com/graph/api/resources/webhooks)，监视警报何时创建或更改。
* 管理警报订阅。 例如，可以获取有效订阅、延长订阅到期时间或删除订阅。

逻辑应用的工作流可使用操作，以从 Microsoft Graph 安全性连接器获取响应，并让输出可用于工作流中的其他操作。 另外，还可以让工作流中的其他操作使用 Microsoft Graph 安全性连接器操作的输出。 例如，如果通过 Microsoft Graph 安全性连接器获取非常严重的警报，可使用 Outlook 连接器在电子邮件中发送这些警报。 

若要详细了解 Microsoft Graph 安全性，请参阅 [Microsoft Graph 安全性 API 概述](https://aka.ms/graphsecuritydocs)。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)。 若要了解 Microsoft Flow 或 PowerApps，请参阅[什么是 Flow？](https://flow.microsoft.com/) 或[什么是 PowerApps？](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 根据 [Microsoft Graph 安全性身份验证要求](https://aka.ms/graphsecurityauth)，必须已明确授予 Azure Active Directory (AD) 租户管理员同意，才能使用 Microsoft Graph 安全性连接器。 若要授予此同意，必须提供 Microsoft Graph 安全性连接器的应用程序 ID 和名称（也可以在 [Azure 门户](https://portal.azure.com)中查找）：

   | properties | Value |
   |----------|-------|
   | **应用程序名称** | `MicrosoftGraphSecurityConnector` |
   | **应用程序 ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   若要为连接器授予同意，Azure AD 租户管理员可以执行下面两个步骤之一：

   * [为 Azure AD 应用程序授予租户管理员同意](../active-directory/develop/v2-permissions-and-consent.md)。

   * 在逻辑应用首次运行期间，应用可以通过[应用程序同意体验](../active-directory/develop/application-consent-experience.md)请求获得 Azure AD 租户管理员同意。
   
* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要访问 Microsoft Graph 安全性实体（如警报）的逻辑应用。 目前，此连接器没有触发器。 因此，若要使用 Microsoft Graph 安全性操作，请使用触发器（例如，“定期”触发器）启动逻辑应用。

## <a name="connect-to-microsoft-graph-security"></a>连接到 Microsoft Graph 安全性 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录 [Azure门户](https://portal.azure.com/)，然后在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请先添加触发器和所需的其他任何操作，再添加 Microsoft Graph安全性操作。

   -或-

   对于现有逻辑应用，请在要添加 Microsoft Graph安全性操作的最后一个步骤下，选择“新建步骤”。

   -或-

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
   依次选择出现的加号 (+) 和“添加操作”。

1. 在搜索框中，输入“microsoft graph 安全性”作为筛选器。 从操作列表中选择所需的操作。

1. 使用 Microsoft Graph 安全性凭据登录。

1. 为所选操作提供必要的详细信息，并继续构建逻辑应用的工作流。

## <a name="add-actions"></a>添加操作

下面更详细地介绍了如何使用各种 Microsoft Graph 安全性连接器操作。

### <a name="manage-alerts"></a>管理警报

若要筛选、排序或获取最新结果，只需提供 [Microsoft Graph支持的 ODATA 查询参数](https://docs.microsoft.com/graph/query-parameters)。 请勿指定完整的基 URL 或 HTTP 操作，例如 `https://graph.microsoft.com/v1.0/security/alerts` 或 `GET`/`PATCH` 操作。 下面的具体示例展示了在要获取非常严重的警报列表时“获取警报”操作的参数：

`Filter alerts value as Severity eq 'high'`

若要详细了解可以与此连接器一起使用的查询，请参阅 [Microsoft Graph 安全性警报参考文档](https://docs.microsoft.com/graph/api/alert-list)。 若要使用此连接器增强体验，请详细了解连接器支持的[架构属性警报](https://docs.microsoft.com/graph/api/resources/alert)。

| 行动 | 描述 |
|--------|-------------|
| **获取警报** | 获取按一个或多个[警报属性](https://docs.microsoft.com/graph/api/resources/alert)筛选出的警报，例如： <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **按 ID 获取警报** | 按警报 ID 获取特定警报。 | 
| **更新警报** | 按警报 ID 更新特定警报。 <p>若要确保在请求中传递必需的可编辑属性，请参阅[可编辑警报属性](https://docs.microsoft.com/graph/api/alert-update)。 例如，若要向安全分析师分配警报以供调查，可以更新警报的“分配对象”属性。 |
|||

### <a name="manage-alert-subscriptions"></a>管理警报订阅

Microsoft Graph 支持[订阅](https://docs.microsoft.com/graph/api/resources/subscription)或 [Webhook](https://docs.microsoft.com/graph/api/resources/webhooks)。 若要获取、更新或删除订阅，请向 Microsoft Graph 实体构造提供 [Microsoft Graph 支持的 ODATA 查询参数](https://docs.microsoft.com/graph/query-parameters)，并添加后跟 ODATA 查询的 `security/alerts`。 
请勿添加基 URL（例如，`https://graph.microsoft.com/v1.0`）。 而是改用此示例中的格式：

`security/alerts?$filter=status eq 'New'`

| 行动 | 描述 |
|--------|-------------|
| **创建订阅** | [创建订阅](https://docs.microsoft.com/graph/api/subscription-post-subscriptions)，用于通知所发生的任何更改。 可以对此订阅筛选出所需的特定警报类型。 例如，可以创建订阅，用于通知非常严重的警报。 |
| **获取有效订阅** | [获取未过期的订阅](https://docs.microsoft.com/graph/api/subscription-list)。 | 
| **更新订阅** | 通过提供订阅 ID 来[更新订阅](https://docs.microsoft.com/graph/api/subscription-update)。 例如，若要扩展订阅，可以更新订阅的 `expirationDateTime` 属性。 | 
| **删除订阅** | 通过提供订阅 ID 来[删除订阅](https://docs.microsoft.com/graph/api/subscription-delete)。 | 
||| 

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](https://aka.ms/graphsecurityconnectorreference)。

## <a name="get-support"></a>获取支持

有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

了解其他[逻辑应用连接器](../connectors/apis-list.md)
