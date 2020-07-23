---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 37a61e270f95ffdc420536046e0ce1eda1e489b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391464"
---
| 资源 |[消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan)|[高级计划](../articles/azure-functions/functions-scale.md#premium-plan)|[专用计划](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|默认[超时持续时间](../articles/azure-functions/functions-scale.md#timeout)（分钟） |5 | 30 |30<sup>1</sup> | 30 | 30 |
|最大[超时持续时间](../articles/azure-functions/functions-scale.md#timeout)（分钟） |10 | 无限<sup>7</sup> | 无限<sup>2</sup> | unbounded | unbounded |
| 最大出站连接数（每个实例） | 600 个处于活动状态（总共 1200 个） | unbounded | unbounded | unbounded | unbounded |
| 最大请求大小（MB）<sup>3</sup> | 100 | 100 | 100 | 100 | 依赖于群集 |
| 最大查询字符串长度<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | 依赖于群集 |
| 最大请求 URL 长度<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | 依赖于群集 |
|每个实例的 [ACU](../articles/virtual-machines/windows/acu.md) 数 | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [AKS 定价](https://azure.microsoft.com/pricing/details/container-service/) |
| 最大内存（每个实例的 GB 数） | 1.5 | 3.5-14 | 1.75-14 | 3.5-14 | 支持任何节点 |
| 每个计划的函数应用数 |100 |100 |无限<sup>4</sup> | unbounded | unbounded |
| [应用服务计划](../articles/app-service/overview-hosting-plans.md) | 每个[区域](https://azure.microsoft.com/global-infrastructure/regions/)100 |每个资源组 100 个 |每个资源组 100 个 | - | - |
| 存储<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | 不适用 |
| 每个应用的自定义域数</a> |500<sup>6</sup> |500 |500 | 500 | 不适用 |
| 自定义域 [SSL 支持](../articles/app-service/configure-ssl-bindings.md) |包含无限制的 SNI SSL 连接 | 包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 | 包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 | 不适用 |

<sup>1</sup>默认情况下，应用服务计划中的函数1.x 运行时的超时值是无限的。  
<sup>2</sup>要求将应用服务计划设置为[Always On](../articles/azure-functions/functions-scale.md#always-on)。 按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)付费。  
<sup>3</sup>这些限制是[在主机中设置](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)的。  
<sup>4</sup>您可以承载的函数应用的实际数目取决于应用的活动、计算机实例的大小和相应的资源利用率。  
<sup>5</sup>存储限制是同一应用服务计划中所有应用的临时存储中的总内容大小。 消耗计划使用 Azure 文件存储作为临时存储。  
<sup>6</sup>当函数应用托管在[消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan)中时，仅支持 CNAME 选项。 对于[高级计划](../articles/azure-functions/functions-scale.md#premium-plan)或[应用服务计划](../articles/azure-functions/functions-scale.md#app-service-plan)中的函数应用，可以使用 CNAME 或 A 记录映射自定义域。  
<sup>7</sup>保证最长60分钟。  
<sup>8 个</sup>辅助角色是托管客户应用的角色。 辅助角色提供三种固定大小：一个 vCPU/3.5 GB RAM;两个 vCPU/7 GB RAM;四个 vCPU/14 GB RAM。
