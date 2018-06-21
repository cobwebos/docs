---
title: Azure 使用情况 API 概述 | Microsoft Docs
description: 了解如何通过 Azure 使用情况 API 以编程方式访问 Azure 资源的成本和使用情况数据。
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: 69e148089f48ebe04faceff87b08eb3f7127f8a3
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248361"
---
# <a name="azure-consumption-api-overview"></a>Azure 使用情况 API 概述 

可以通过 Azure 使用情况 API 以编程方式访问 Azure 资源的成本和使用情况数据。 这些 API 目前仅支持企业合约和 Web Direct 订阅（有一些例外）。 这些 API 会持续进行更新，以便为其他类型的 Azure 订阅提供支持。

Azure 使用情况 API 允许访问以下内容：
- 企业和 Web Direct 客户 
    - 使用情况详细信息 
    - 市场费用 
    - 预留建议 
    - 预留详细信息 
    - 预留摘要 
- 仅企业客户 
    - 价目表 
    - 预算 
    - 余额 

## <a name="usage-details-api"></a>使用情况详细信息 API

通过使用情况详细信息 API 获取所有 Azure 第一方资源的费用和使用情况数据。 信息采用使用情况详细信息记录的形式，这些记录目前会每天针对每个资源按计量发出。 可以根据信息来合计所有资源的成本，或者调查特定资源的成本/使用情况。 

该 API 包括：

-   **计量级别使用情况数据** - 查看各种数据，包括使用情况成本、发出费用的计量，以及与费用相关的 Azure 资源。 所有使用情况详细信息记录都映射到每日 Bucket。
-   Azure 基于角色的访问控制 - 在 [Azure 门户](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 或 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 上配置访问策略，指定哪些用户或应用程序有权访问订阅的使用情况数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。 
-   **筛选** - 使用以下筛选器将 API 结果集裁剪成较小的一组使用情况详细信息记录：
    - 使用结束/使用开始
    - 资源组
    - 资源名称
-   **数据聚合** - 通过 OData 应用表达式，按标记或筛选器属性聚合使用情况详细信息
-   **不同产品/服务类型的使用情况** - 使用情况详细信息目前可供企业和 Web Direct 客户使用。

有关详细信息，请参阅[使用情况详细信息 API](https://docs.microsoft.com/rest/api/consumption/usagedetails) 的技术规范。

## <a name="marketplace-charges-api"></a>市场费用 API

使用市场费用 API 获取所有市场资源（Azure 第三方产品/服务）的费用和使用情况数据。 可以根据此数据来合计所有市场资源的成本，或者调查特定资源的成本/使用情况。 

该 API 包括：

-   **计量级别使用情况数据** - 查看各种数据，包括市场使用情况成本、发出费用的计量，以及与费用相关的资源。 所有使用情况详细信息记录都映射到每日 Bucket。
-   Azure 基于角色的访问控制 - 在 [Azure 门户](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 或 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 上配置访问策略，指定哪些用户或应用程序有权访问订阅的使用情况数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。 
-   **筛选** - 使用以下筛选器将 API 结果集裁剪成较小的一组市场记录：
    - 使用开始/使用结束
    - 资源组
    - 资源名称
-   **不同产品/服务类型的使用情况** - 市场信息目前可供企业和 Web Direct 客户使用。

有关详细信息，请参阅[市场费用 API](https://docs.microsoft.com/rest/api/consumption/marketplaces) 的技术规范。

## <a name="balances-api"></a>余额 API

企业客户可以使用余额 API 获取关于余额、新购买、Azure 市场服务费用、调整和超额费用信息的每月摘要。 可以获取当前计费周期或过去的任何计费周期的此信息。 企业可以使用此数据，与手动计算的汇总费用进行比较。 此 API 不提供特定于资源的信息以及成本的聚合视图。 

该 API 包括：

-   Azure 基于角色的访问控制 - 在 [Azure 门户](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 或 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 上配置访问策略，指定哪些用户或应用程序有权访问订阅的使用情况数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。 
-   **仅企业客户** - 此 API 只供 EA 客户使用。 
    - 客户必须具有企业管理员权限才能调用此 API 

有关详细信息，请参阅[余额 API](https://docs.microsoft.com/rest/api/consumption/getbalancesbybillingaccount) 的技术规范。

## <a name="budgets-api"></a>预算 API

企业客户可以使用此 API 创建资源、资源组或计费计量的成本或使用情况预算。 确定此信息以后，即可配置警报，使之在用户定义的预算阈值超出以后进行通知。 

该 API 包括：

-   Azure 基于角色的访问控制 - 在 [Azure 门户](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 或 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 上配置访问策略，指定哪些用户或应用程序有权访问订阅的使用情况数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。 
-   **仅企业客户** - 此 API 只供 EA 客户使用。
-   **可配置的通知** - 指定预算超额时要通知的用户。
-   **基于使用情况或成本的预算** - 根据方案所需的使用情况或成本创建预算。
-   **筛选** - 使用以下可配置的筛选器将预算筛选成较小的一部分资源
    - 资源组
    - 资源名称
    - 计量
-   **可配置的预算时间段** - 指定预算重置的频率和预算的有效时间。

有关详细信息，请参阅[预算 API](https://docs.microsoft.com/rest/api/consumption/budgets) 的技术规范。

## <a name="reservation-recommendations-api"></a>预留建议 API

使用此 API 获取购买虚拟机预留实例的建议。 建议旨在让客户能够分析预期的成本节省情况和购买金额。 

该 API 包括：

-   Azure 基于角色的访问控制 - 在 [Azure 门户](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 或 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 上配置访问策略，指定哪些用户或应用程序有权访问订阅的使用情况数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。 
-   **筛选** - 使用以下筛选器调整建议结果：
    - 范围
    - 回溯期间
-   **不同产品/服务类型的预留信息** - 预留信息目前可供企业和 Web Direct 客户使用。

有关详细信息，请参阅[预留建议 API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) 的技术规范。

## <a name="reservation-details-api"></a>预留详细信息 API

使用预留详细信息 API 查看以前购买的虚拟机预留的信息，例如预留使用量与实际使用量的对比。 可以按 VM 级别详细程度查看数据。 

该 API 包括：

-   Azure 基于角色的访问控制 - 在 [Azure 门户](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 或 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 上配置访问策略，指定哪些用户或应用程序有权访问订阅的使用情况数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。 
-   **筛选** - 使用以下筛选器将 API 结果集裁剪成较小的一组预留：
    - 日期范围
-   **不同产品/服务类型的预留信息** - 预留信息目前可供企业和 Web Direct 客户使用。

有关详细信息，请参阅[预留详细信息 API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) 的技术规范。

## <a name="reservation-summaries-api"></a>预留摘要 API

使用此 API 可查看以前购买的虚拟机预留的聚合信息，例如聚合形式的预留使用量与实际使用量的对比。 

该 API 包括：

-   Azure 基于角色的访问控制 - 在 [Azure 门户](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 或 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 上配置访问策略，指定哪些用户或应用程序有权访问订阅的使用情况数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。 
-   **筛选** - 将每日粒度与以下筛选器配合使用，以便调整结果：
    - 使用日期
-   **不同产品/服务类型的预留信息** - 预留信息目前可供企业和 Web Direct 客户使用。
-   **每日或每月聚合** - 调用方可以指定其需要的预留摘要数据是采用每日粒度还是每月粒度。

有关详细信息，请参阅[预留摘要 API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) 的技术规范。

## <a name="price-sheet-api"></a>价目表 API
企业客户可以使用此 API 检索所有计量的自定义定价。 企业可以将此信息与使用情况详细信息和市场使用情况信息结合起来，根据使用情况和市场数据来计算成本。 

该 API 包括：

-   Azure 基于角色的访问控制 - 在 [Azure 门户](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 或 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 上配置访问策略，指定哪些用户或应用程序有权访问订阅的使用情况数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。 
-   **仅企业客户** - 此 API 只供 EA 客户使用。 Web Direct 客户应使用 RateCard API 来获取定价。 

有关详细信息，请参阅[价目表 API](https://docs.microsoft.com/rest/api/consumption/pricesheet) 的技术规范。

## <a name="scenarios"></a>方案

下面是一些可以通过使用情况 API 实现的方案：

-   **发票对帐** - Microsoft 对我的收费是否正确？  我的帐单情况如何？我可以自行计算吗？
-   **交叉收费** - 知道我的费用以后，需由组织中的谁来付费？
-   **成本优化** - 我已知道自己的费用。 如何充分利用我在 Azure 的投资？
-   **成本跟踪** - 我想要了解自己某段时间在 Azure 的开销和使用情况。 其趋势如何？ 如何进行改善？
-   **当月的 Azure 开销** - 到目前为止，我当月的开销如何？ 我是否需要对自己的 Azure 开销和/或使用情况进行调整？ 这个月的哪个时间我使用 Azure 最多？
-   **设置警报** - 我想要根据预算设置基于资源的使用量或基于资金的警报。

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用 Azure 计费 API 以编程方式洞察 Azure 使用情况，请参阅 [Azure 计费 API 概述](billing-usage-rate-card-overview.md)。



