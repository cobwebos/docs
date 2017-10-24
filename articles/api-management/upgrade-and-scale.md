---
title: "升级和缩放 Azure API 管理实例 | Microsoft Docs"
description: "本主题介绍如何升级和缩放 Azure API 管理实例。"
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: cf5cd03bd9147ac81b07dc453a919a3c74de44fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>升级和缩放 Azure API 管理实例 

客户可通过添加和删除单位来缩放 API 管理 (APIM) 实例。 **单位**由专用的 Azure 资源构成，具有以每月 API 调用次数表示的承载容量。 实际吞吐量和延迟因多种因素而异，例如并发连接的数量和速率、配置策略的类型和数量、请求和响应大小以及后端延迟。 

每个单位的容量和价格取决于该单位所在的**层**。 可在三个层之间选择：“开发人员”、“标准”和“高级”。 如果需要提高层中服务的容量，应添加单位。 如果当前在 APIM 实例中选择的层不允许添加更多单位，则需要升级到更高级别的层。 

每个单位的价格、能否添加/删除单位以及能否获得特定的功能（例如多区域部署）取决于为 APIM 实例选择的层。 [定价详细信息](https://azure.microsoft.com/pricing/details/api-management/)一文介绍了每个单位的价格，以及每个层提供的功能。 

>[!NOTE]
>[定价详细信息](https://azure.microsoft.com/pricing/details/api-management/)一文说明了每个层中单位容量的大致数字。 若要获取更准确的数字，需要分析所用 API 的实际方案。 请参阅后面的“如何规划容量”部分。

## <a name="prerequisites"></a>先决条件

若要执行本文中所述的步骤，必须具有：

+ 一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ 一个 APIM 实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

## <a name="how-to-plan-for-capacity"></a>如何规划容量？

若要确定是否有足够的单位用于处理流量，请测试预期的工作负荷。 

如前所述，APIM 每秒可处理的请求数取决于许多可变因素， 例如，连接模式、请求和响应的大小、在每个 API 上配置的策略，以及要发送请求的客户端数。

可使用**指标**（使用 Azure Monitor 功能）在任意给定时间使用的容量。

### <a name="use-the-azure-portal-to-examine-metrics"></a>使用 Azure 门户检查指标 

1. 在 [Azure 门户](https://portal.azure.com/)中导航到自己的 APIM 实例。
2. 选择“指标”。
3. 在“可用指标”中选择“容量”指标。 

    容量指标大致反映了目前在租户中使用的容量。 可以通过不断增加负载进行测试，确定峰值负载有多大。 可以设置指标警报，以便在发生意外的情况时收到通知。 例如，APIM 实例超出容量有 5 分钟以上。 

    >[!TIP]
    > 可以配置警报，以便在用于运行服务的容量偏低时收到通知，或者让服务调入逻辑应用来自动通过添加单位进行扩展。

## <a name="upgrade-and-scale"></a>升级和缩放 

如前所述，可在三个层之间选择：“开发人员”、“标准”和“高级”。 应该将“开发人员”层用于评估服务；不应将其用于生产。 “开发人员”层不附带 SLA，无法缩放此层（添加/删除单位）。 

“标准”和“高级”是附带 SLA 的生产层，可以缩放。 最高可将“标准”层扩展到四个单位。 可将任意数目的单位添加到“高级”层。 

使用“高级”层可在任意数目的所需 Azure 区域中分配单个 API 管理实例。 最初创建 API 管理服务时，实例仅包含一个单位，并驻留在单个 Azure 区域中。 初始区域被指定为**主要**区域。 可以轻松添加更多的区域。 添加区域时，需指定想要分配的单位数。 例如，可在**主要**区域中包含一个单位，在另外某个区域中包含五个单位。 可以定制要在每个区域中处理的任何流量。 有关详细信息，请参阅[如何将 Azure API 管理服务实例部署到多个 Azure 区域](api-management-howto-deploy-multi-region.md)。

可在任意两个层之间来回升级和降级。 请注意，降级可能会删除某些功能，例如，从“高级”层降级到“标准”层时，会删除 VNET 或多区域部署。

>[!NOTE]
>升级或缩放过程可能需要 15 到 30 分钟才能完成。 完成时会收到通知。

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>使用 Azure 门户进行升级和缩放

1. 在 [Azure 门户](https://portal.azure.com/)中导航到自己的 APIM 实例。
2. 选择“规模和定价”。
3. 选择所需的层。
4. 指定想要添加的**单位**数。 可以使用滑块，或键入单位数。<br/>
    如果选择“高级”层，则首先需要选择一个区域。
5. 按“保存”

## <a name="next-steps"></a>后续步骤

[如何将 Azure API 管理服务实例部署到多个 Azure 区域](api-management-howto-deploy-multi-region.md)

