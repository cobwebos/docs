---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77198337"
---
| 资源 | [消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan) | [高级计划](../articles/azure-functions/functions-scale.md#premium-plan) | [应用服务计划](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| 横向扩展 | 事件驱动 | 事件驱动 | [手动/自动缩放](../articles/app-service/manage-scale-up.md) | 
| 最大实例数 | 200 | 100 | 10-20 |
|默认[超时持续时间](../articles/azure-functions/functions-scale.md#timeout)（分钟） |5 | 30 |30<sup>2</sup> |
|最大[超时持续时间](../articles/azure-functions/functions-scale.md#timeout)（分钟） |10 | 无限<sup>8</sup> | 不受限制<sup>3</sup> |
| 最大出站连接数（每个实例） | 600 个处于活动状态（总共 1200 个） | unbounded | unbounded |
| 最大请求大小 (MB)<sup>4</sup> | 100 | 100 | 100 |
| 最大查询字符串长度<sup>4</sup> | 4096 | 4096 | 4096 |
| 最大请求 URL 长度<sup>4</sup> | 8192 | 8192 | 8192 |
| 每个实例的 [ACU](../articles/virtual-machines/windows/acu.md) 数 | 100 | 210-840 | 100-840 |
| 最大内存（每个实例的 GB 数） | 1.5 | 3.5-14 | 1.75-14 |
| 每个计划的函数应用数 |100 |100 |不受限制<sup>5</sup> |
| [应用服务计划](../articles/app-service/overview-hosting-plans.md) | 每个[区域](https://azure.microsoft.com/global-infrastructure/regions/) 100 个 |每个资源组 100 个 |每个资源组 100 个 |
| 存储<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| 每个应用的自定义域数</a> |500<sup>7</sup> |500 |500 |
| 自定义域 [SSL 支持](../articles/app-service/configure-ssl-bindings.md) |包含无限制的 SNI SSL 连接 | 包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 | 

<sup>1</sup> 有关各种应用服务计划选项的特定限制，请参阅[应用服务计划限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。  
<sup>2</sup> 默认情况下，应用服务计划中的 Functions 1.x 运行时的超时是无限制的。  
<sup>3</sup> 需要将应用服务计划设置为 [Always On](../articles/azure-functions/functions-scale.md#always-on)。 按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)付费。  
<sup>4</sup> 这些限制[在主机中设置](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)。  
<sup>5</sup> 可以托管的函数应用的实际数目取决于应用的活动、计算机实例的大小和相应的资源利用率。  
<sup>6</sup> 存储限制是同一应用服务计划中所有应用的临时存储中的总内容大小。 消耗计划使用 Azure 文件存储作为临时存储。  
<sup>7</sup> 当函数应用托管在[消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan)中时，仅支持 CNAME 选项。 对于[高级计划](../articles/azure-functions/functions-scale.md#premium-plan)或[应用服务计划](../articles/azure-functions/functions-scale.md#app-service-plan)中的函数应用，可以使用 CNAME 或 A 记录映射自定义域。  
<sup>8</sup> 保证最长 60 分钟。