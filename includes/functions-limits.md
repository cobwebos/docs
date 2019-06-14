---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 9b98e1a1f5243584d0ca4b1490e25302ec26b465
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050536"
---
| Resource | [消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan) | [高级计划](../articles/azure-functions/functions-scale.md#premium-plan) | [应用服务计划](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| 横向扩展 | 事件驱动 | 事件驱动 | [手动/自动缩放](../articles/app-service/web-sites-scale.md) | 
|默认值[超时持续时间](../articles/azure-functions/functions-scale.md#timeout)（分钟） |5 | 30 |30<sup>2</sup> |
|最大[超时持续时间](../articles/azure-functions/functions-scale.md#timeout)（分钟） |10 | unbounded | 不受限制<sup>3</sup> |
| 最大出站连接数 （每个实例） | 600 active （1200年总计） | unbounded | unbounded |
| 最大请求大小 (MB)<sup>4</sup> | 100 | 100 | 100 |
| 最大查询字符串的长度<sup>4</sup> | 4096 | 4096 | 4096 |
| 请求的最大 URL 长度<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md)每个实例 | 100 | 210-840 | 100-840 |
| 最大内存 (GB 每个实例) | 1.5 | 3.5-14 | 1.75-14 |
| 每个计划的 function app |100 |100 |unbounded<sup>5</sup> |
| [应用服务计划](../articles/app-service/overview-hosting-plans.md) | 每 100 个[区域](https://azure.microsoft.com/global-infrastructure/regions/) |每个资源组 100 个 |每个资源组 100 个 |
| 存储<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| 每个应用的自定义域数</a> |500<sup>7</sup> |500 |500 |
| 自定义域 [SSL 支持](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |不受支持，通配符证书为 *。 默认情况下可用的 azurewebsites.net| 不受限制的 SNI SSL 和包含 1 个 IP SSL 连接 |不受限制的 SNI SSL 和包含 1 个 IP SSL 连接 | 

<sup>1</sup>有关各种应用服务计划选项的特定限制，请参阅[应用服务计划限制](../articles/azure-subscription-service-limits.md#app-service-limits)。  
<sup>2</sup>默认情况下，Functions 1.x 运行时在应用服务计划中的超时时间为无限。  
<sup>3</sup>需要应用服务计划设置为[Always On](../articles/azure-functions/functions-scale.md#always-on)。 以标准支付[费率](https://azure.microsoft.com/pricing/details/app-service/)。  
<sup>4</sup>这些限制[主机中设置](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)。  
<sup>5</sup>可以托管的函数应用的实际数目取决于应用的活动、 计算机实例和相应的资源利用率的大小。
<sup>6</sup>存储限制是内容总大小中临时存储的所有应用相同的应用服务计划中。 消耗计划使用 Azure 文件进行临时存储。  
<sup>7</sup>函数应用中的托管时[消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan)，支持仅 CNAME 选项。 为函数应用中[高级版计划](../articles/azure-functions/functions-scale.md#premium-plan)或[应用服务计划](../articles/azure-functions/functions-scale.md#app-service-plan)，可以将映射自定义域，使用 CNAME 或 A 记录。
