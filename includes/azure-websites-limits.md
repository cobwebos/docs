---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 707f18ade87e755243a9122ecb7f898a753b2a8e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224546"
---
| 资源 | 免费 | 共享 | 基本 | 标准版 | 高级 (v2) | 独立 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web, mobile, or API apps](https://azure.microsoft.com/services/app-service/) per [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |无限制<sup>2</sup> |无限制<sup>2</sup> |无限制<sup>2</sup> |无限制<sup>2</sup>|
| [应用服务计划](../articles/app-service/overview-hosting-plans.md) |每个区域仅 10 个 |每个资源组 10 个 |每个资源组 100 个 |每个资源组 100 个 |每个资源组 100 个 |每个资源组 100 个|
| 计算实例类型 |共享 |共享 |专用<sup>3</sup> |专用<sup>3</sup> |专用<sup>3</sup></p> |专用<sup>3</sup>|
| [Scale out](../articles/app-service/manage-scale-up.md) (maximum instances) |1 个共享 |1 个共享 |3 个专用<sup>3</sup> |10 个专用<sup>3</sup> |20 个专用<sup>3</sup>|100 个专用<sup>4</sup>|
| 存储<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU time (5 minutes)<sup>6</sup> |3 分钟 |3 分钟 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费|
| CPU 时间（天）<sup>6</sup> |60 分钟 |240 分钟 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |
| 内存（1 小时） |1,024 MB per App Service plan |1,024 MB per app |N/A |N/A |N/A |N/A |
| 带宽 |165 MB |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |
| 应用程序体系结构 |32 位 |32 位 |32 位/64 位 |32 位/64 位 |32 位/64 位 |32 位/64 位 |
| Web sockets per instance<sup>7</sup> |5 |35 |350 |无限制 |无限制 |无限制 |
| IP connections | 600 | 600 | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | 64,000 |
| 每个应用程序的并发[调试器连接数](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |第 |第 |第 |5 |5 |5 |
| App Service Certificates per subscription<sup>9</sup>| 不支持 | 不支持 |10 |10 |10 |10 |
| 每个应用的自定义域数</a> |0（仅 azurewebsites.net 子域）|500 |500 |500 |500 |500 |
| 自定义域 [SSL 支持](../articles/app-service/configure-ssl-certificate.md) |Not supported, wildcard certificate for *.azurewebsites.net available by default|Not supported, wildcard certificate for *.azurewebsites.net available by default|无限制的 SNI SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 | 包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接|
| Hybrid connections per plan | | | 5 | 25 | 200 | 200 |
| Integrated load balancer | |X |X |X |X |X<sup>10</sup> |
| [始终打开](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Scheduled backups](../articles/app-service/manage-backup.md) | | | | Scheduled backups every 2 hours, a maximum of 12 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) |
| [自动缩放](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Azure 计划程序](https://azure.microsoft.com/services/scheduler/)支持 | |X |X |X |X |X |
| [终结点监视](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Staging slots](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>除非特别说明，否则应用和存储配额依每个应用服务计划为准。  
<sup>2</sup>可以在这些计算机上托管的应用的实际数目取决于应用的活动、计算机实例的大小和相应的资源利用率。  
<sup>3</sup>专用实例可有不同的大小。 有关详细信息，请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。  
<sup>4</sup>More are allowed upon request.  
<sup>5</sup>The storage limit is the total content size across all apps in the same App service plan. The total content size of all apps across all App service plans in a single resource group and region cannot exceed 500GB.  
<sup>6</sup>这些资源受到专用实例上的物理资源（实例大小和实例数）的限制。  
<sup>7</sup>如果将基本层的某个应用扩展为两个实例，则其中每个实例有 350 个并发连接。 For Standard tier and above, there are no theoretical limits to web sockets, but other factors can limit the number of web sockets. For example, maximum concurrent requests allowed (defined by `maxConcurrentRequestsPerCpu`) are: 7,500 per small VM, 15,000 per medium VM (7,500 x 2 cores), and 75,000 per large VM (18,750 x 4 cores).  
<sup>8</sup>The maximum IP connections are per instance and depend on the instance size: 1,920 per B1/S1/P1V2 instance, 3,968 per B2/S2/P2V2 instance, 8,064 per B3/S3/P3V2 instance.  
<sup>9</sup>The App Service Certificate quota limit per subscription can be increased via a support request to a maximum limit of 200.  
<sup>10</sup>App Service Isolated SKUs can be internally load balanced (ILB) with Azure Load Balancer, so there's no public connectivity from the internet. 因此，必须从能够直接访问 ILB 网络终结点的计算机使用 ILB 独立应用服务的某些功能。  
<sup>11</sup>Run custom executables and/or scripts on demand, on a schedule, or continuously as a background task within your App Service instance. Always On 对于连续执行 Web 作业是必需的。 Azure 计划程序免费或标准版对于计划的 Web 作业是必需的。 There's no predefined limit on the number of WebJobs that can run in an App Service instance. There are practical limits that depend on what the application code is trying to do.  