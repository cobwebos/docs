---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 1080e3488483610af0f0c9afadaf000895021821
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657732"
---
| Resource | 免费 | 已共享 | 基本 | 标准 | 高级 (v2) | 独立 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| 每个[Azure App Service 计划](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> [的 Web 应用、移动应用或 API 应用](https://azure.microsoft.com/services/app-service/) |10 |100 |无限制<sup>2</sup> |无限制<sup>2</sup> |无限制<sup>2</sup> |无限制<sup>2</sup>|
| [应用服务计划](../articles/app-service/overview-hosting-plans.md) |每个区域仅 10 个 |每个资源组 10 个 |每个资源组 100 个 |每个资源组 100 个 |每个资源组 100 个 |每个资源组 100 个|
| 计算实例类型 |已共享 |已共享 |专用<sup>3</sup> |专用<sup>3</sup> |专用<sup>3</sup></p> |专用<sup>3</sup>|
| [横向扩展](../articles/app-service/manage-scale-up.md)(最大实例数) |1 个共享 |1 个共享 |3 个专用<sup>3</sup> |10 个专用<sup>3</sup> |20 个专用<sup>3</sup>|100 个专用<sup>4</sup>|
| 存储<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU 时间 (5 分钟)<sup>6</sup> |3 分钟 |3 分钟 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费|
| CPU 时间（天）<sup>6</sup> |60 分钟 |240 分钟 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |
| 内存（1 小时） |每个应用服务计划 1024 MB |每个应用 1024 MB |不可用 |不可用 |不可用 |不可用 |
| 带宽 |165 MB |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |
| 应用程序体系结构 |32 位 |32 位 |32 位/64 位 |32 位/64 位 |32 位/64 位 |32 位/64 位 |
| 每个实例的 Web 套接字<sup>7</sup> |5 |35 |350 |无限制 |无限制 |不受限制 |
| 每个应用程序的并发[调试器连接数](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| 每个订阅的应用服务证书<sup>10</sup>| 不支持 | 不支持 |10 |10 |10 |10 |
| 每个应用的自定义域数</a> |0（仅 azurewebsites.net 子域）|500 |500 |500 |500 |500 |
| 自定义域 [SSL 支持](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |不受支持, 默认情况下, azurewebsites.net 提供通配符证书|不受支持, 默认情况下, azurewebsites.net 提供通配符证书|无限制的 SNI SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 | 包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接|
| 集成的负载均衡器 | |X |X |X |X |X<sup>9</sup> |
| [始终打开](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [计划的备份](../articles/app-service/manage-backup.md) | | | | 每2小时计划一次备份, 每天最多12个备份 (手动 + 计划) | 每小时计划备份, 每天最多50个备份 (手动 + 计划) | 每小时计划备份, 每天最多50个备份 (手动 + 计划) |
| [自动缩放](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure 计划程序](https://azure.microsoft.com/services/scheduler/)支持 | |X |X |X |X |X |
| [终结点监视](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [过渡槽](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>除非特别说明，否则应用和存储配额依每个应用服务计划为准。  
<sup>2</sup>可以在这些计算机上托管的应用的实际数目取决于应用的活动、计算机实例的大小和相应的资源利用率。  
<sup>3</sup>专用实例可有不同的大小。 有关详细信息，请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。  
<sup>4</sup>请求时允许更多。  
<sup>5</sup>存储限制是跨相同应用服务计划中所有应用的内容总大小。  
<sup>6</sup>这些资源受到专用实例上的物理资源（实例大小和实例数）的限制。  
<sup>7</sup>如果将基本层的某个应用扩展为两个实例，则其中每个实例有 350 个并发连接。 对于标准层和更高版本, web 套接字没有理论限制, 但其他因素可能会限制 web 套接的数量。 例如, 允许的最大并发请求数 ( `maxConcurrentRequestsPerCpu`由定义) 为:每个小型 VM 7500、每个中型 VM 15000 (7500 x 2 核心) 和每个大型 VM 75000 (18750 x 4 核心)。  
<sup>8</sup>按需、按计划或作为应用服务实例内的后台任务连续运行自定义可执行文件和/或脚本。 连续执行 WebJob 需要使用“始终打开”。 计划的 WebJob 需要使用 Azure 计划程序免费或标准版。 对于可以在应用服务实例中运行的 Webjob 的数量没有预定义的限制。 有实际的限制取决于应用程序代码尝试执行的操作。  
<sup>9</sup>独立应用服务 Sku 可以通过 Azure 负载均衡器进行内部负载均衡 (ILB), 因此没有来自 internet 的公共连接。 因此，必须从能够直接访问 ILB 网络终结点的计算机使用 ILB 独立应用服务的某些功能。  
<sup>10</sup>可以通过支持请求将每个订阅的应用服务证书配额限制增加到最大限制为200。  