---
title: 存储在 Azure Application Insights 中的个人数据指南 | Microsoft Docs
description: 本文介绍如何管理存储在 Azure Application Insights 中的个人数据以及用于识别和删除它的方法。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: a59b57c546f18a7d91160f2ae7282af82fc42160
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044707"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>存储在 Application Insights 中的个人数据指南

Application Insights 是数据存储，可能会在其中找到个人数据。 本文讨论通常在 Application Insights 中的哪些位置可找到此数据，以及可用于处理此数据的功能。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>个人数据处理策略

下面介绍了一些可行的方法，不过最终将由你和你的公司来决定用于处理私人数据（如有）的策略。 我们从技术角度出发，按偏好顺序，从最偏好的到最不偏好的列出这些方法：
* 如果可能，请停止收集、混淆、匿名处理或以其他方式调整要收集的数据，以免其被视为“私密”。 此方法为首选方法，无需创建昂贵且影响很大的数据处理策略。
* 如果不可能，请尝试规范化数据，以减少对数据平台和性能的影响。 例如，不记录显式用户 ID，而是创建数据查找，将用户名及其详细信息关联到可随后在其他位置记录的内部 ID。 这样一来，如果某个用户要求删除其个人信息，则只需删除查找表中对应于该用户的行就足够了。 
* 最后，如果必须收集私人数据，请就清除 API 路径和现有查询 API 路径构建相关流程，以满足在导出和删除与用户关联的任何私人数据时可能需要承担的任何义务。

## <a name="where-to-look-for-private-data-in-application-insights"></a>在 Application Insights 中的何处查找私人数据？

Application Insights 是十分灵活的存储，可在规定数据架构的同时允许使用自定义值替代每个字段。 因此，无法确切知道可在特定应用程序的哪些位置找到私人数据。 但是，不妨从库存中的以下位置着手：

* IP 地址：虽然 Application Insights 在默认情况下会将所有 IP 地址字段混淆成“0.0.0.0”，但为了保留会话信息，通常会将此值替代为实际的用户 IP。 可以使用下面的 Analytics 查询来查找特定的表，此类表的 IP 地址列中包含的值在过去 24 小时内不是“0.0.0.0”：
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* 用户 ID：默认情况下，Application Insights 会使用为用户随机生成的 ID，以便进行会话跟踪。 不过，这些字段常常会被替代，改为存储与应用程序更相关的 ID。 例如：用户名、AAD GUID 等。这些 ID 通常会被视为范围内的个人数据，因此应处理得当。 我们的建议始终是尝试对这些 ID 进行混淆或匿名处理。 通常可以在其中发现这些值的字段包括：session_Id、user_Id、user_AuthenticatedId、user_AccountId、customDimensions。
* 自定义数据：Application Insights 允许向任何数据类型追加一组自定义维度。 这些维度可以是任何数据。 使用以下查询来确定在过去 24 小时内收集的任何自定义维度：
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* 内存中和传输中数据：Application Insights 会跟踪异常、请求、依赖项调用和跟踪。 私人数据通常可以在代码和 HTTP 调用级别收集。 查看异常、请求、依赖项和跟踪表中是否存在任何此类数据。 尽可能使用[遥测初始值设定项](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling)来混淆该数据。
* Snapshot Debugger 捕获：使用 Application Insights 中的 [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) 功能时，只要在应用程序的生产实例上捕获某个异常，就可以收集调试快照。 快照会公开导致异常的完整堆栈跟踪，以及堆栈中每一步的本地变量的值。 遗憾的是，此功能不允许选择性地删除吸附点，也不允许以编程方式访问快照中的数据。 因此，如果默认的快照保留率不满足符合性要求，建议关闭此功能。

## <a name="how-to-export-and-delete-private-data"></a>如何导出和删除私人数据

如果可能，__强烈__建议重新构建数据收集策略，禁止收集私人数据、混淆或对其进行匿名处理或以其他方式对其进行修改，以防其被视为“私有”。 在数据收集后对其进行处理需要你和你的团队定义和自动化策略，为客户构建一个界面，以便与数据交互，并且还需持续承担维护成本。 此外，Application Insights 的计算成本很高，大量并发查询或清除 API 调用可能会对与 Application Insights 功能的所有其他交互产生负面影响。 尽管如此，在某些情况下，确实必须收集私人数据。 对于这些情况，数据应按本部分所述进行处理。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>查看和导出

对于查看和导出数据请求，应使用[查询 API](https://dev.applicationinsights.io/quickstart)。 将数据形状转换为适当形状以提供给用户时，将由你实现相关逻辑。 [Azure Functions](https://azure.microsoft.com/services/functions/) 是托管此类逻辑的好地方。

### <a name="delete"></a>删除

> [!WARNING]
> Application Insights 中的删除操作具有破坏性且不可逆！ 执行时请特别小心。

我们已在隐私处理情景中提供“清除”API 路径。 由于存在与这样做相关的风险、潜在的性能影响，并有可能导致 Application Insights 数据的所有聚合、度量和其他方面发生偏差，因此应谨慎使用此路径。 有关处理私人数据的替代方法，请参阅上面的[个人数据处理策略](#strategy-for-personal-data-handling)部分。

清除是一项高特权操作，如果未在 Azure 资源管理器中显式授予某个角色，Azure 中没有任何应用或用户（甚至包括资源所有者）有权执行。 此角色为数据清除程序，由于可能会丢失数据，应仔细委托。

一旦分配 Azure 资源管理器角色，就有两个新的 API 路径可用（已提供完整开发人员文档和调用形状的链接）：

* [POST purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) - 使用一个对象来指定要删除的数据的参数，并返回引用 GUID
* GET purge status：POST purge 调用将返回“x-ms-status-location”标头，其中包含一个 URL，可以调用该 URL 来确定清除 API 的状态。 例如：
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  虽然绝大多数清除操作完成起来会比 SLA 快得多，但由于其对 Application Insights 使用的数据平台造成的严重影响，因此**将完成清除操作所需的正式 SLA 设置为 30 天**。

## <a name="next-steps"></a>后续步骤
如需详细了解如何收集、处理和保护数据，请参阅 [Application Insights 数据安全性](app-insights-data-retention-privacy.md)。