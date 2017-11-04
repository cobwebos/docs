---
title: "Azure 中国世纪互联开发人员指南 | Microsoft Docs"
description: "Microsoft 提供所需的工具来帮助开发人员创建云应用程序并将其部署到全球 Azure 和 Azure 中国世纪互联。 了解可在全球 Azure 中和 Azure 中国使用的服务和功能，以及哪些功能可能在中国不可用。"
services: china
cloud: na
documentationcenter: na
author: v-wimarc
manager: edprice
ms.assetid: na
ms.service: china
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: v-wimarc
ms.openlocfilehash: f9b0d28d9f7c2774af6f5ca508c58b22047f52c3
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="developer-guide-for-azure-china-21vianet"></a>Azure 中国世纪互联开发人员指南
Microsoft 提供所需的工具来帮助开发人员创建云应用程序并将其部署到全球 Microsoft Azure 服务（“全球服务”）和世纪互联运营的 Microsoft Azure（Azure 中国世纪互联）。 大多数当前可用的技术内容（例如 [Azure 文档中心](https://azure.microsoft.com/documentation/)）都假设应用程序是针对全球 Azure 开发的，因此，必须了解哪些[服务和功能](/azure/china/china-get-started-service-availability)可在 Microsoft Azure 中国世纪互联中使用。

在提到适用于全球 Azure 服务的公用内容时，请务必修改相关的步骤，或自定义任何指定全球 Azure 服务设置的示例代码。 例如，自定义 [Azure 服务终结点](#Check-endpoints-in-Azure)。

## <a name="migrate-existing-applications-and-workloads"></a>迁移现有的应用程序和工作负荷
迁移在其他位置托管的应用程序或工作负荷需要花费一定的时间，并需要做好规划。 迁移到 Microsoft Azure 中国世纪互联：
- 将经典 Azure 服务管理器 (ASM) 中的[虚拟机移动](/azure/china/china-how-to-rehost)到 Azure 资源管理器。
- [重构其他云提供程序中托管的现有应用程序](/azure/china/china-how-to-refactor)，以便更顺利、更快速地完成迁移。 
- [重新托管全球 Azure 中托管的现有应用程序](/azure/china/china-how-to-rehost)。
- 在测试环境中[使用全局连接工具包运行试验性迁移](https://github.com/Azure/AzureGlobalConnectionToolkit)，以帮助缓解迁移风险。

## <a name="develop-for-azure-users"></a>针对 Azure 用户进行开发
如果你习惯了针对其他区域中的用户开发云服务，请务必考虑中国用户的以下预期：
- **移动优先：**移动设备（而不是电脑）被认为是联网世界的源头。 请确保设计策略以移动为中心。
- **QR 码和屏幕扫描行为：**网站、印刷广告、名片和其他媒体通常包含 QR 码。 可在网站的页首和页脚中包含 QR 码，使访客能够在其手机上快速加载网站的手机版。
- **内容本地化：**本地化不仅仅是指翻译内容。 请花些时间来了解目标市场的数字环境，以及业务决策造成的文化影响，然后相应地定制内容。 

## <a name="use-social-sites-and-media-services-in-china"></a>使用中国的社交网站和媒体服务
在中国，常用的西方社交媒体网站和服务可能已被封锁。 在中国运营 Web 内容时：
- 请避免在网站前端连接到 Google 服务。 Google 连同其所有服务在中国已被封锁。 为获得最佳效果，需在网站中避免使用 Google 服务。 例如，将 Google 地图替换为百度地图，使用本机字体而不要使用 Google 字体。
- 不要嵌入来自 YouTube 或 Vimeo 的视频。 这两个服务在中国已被封锁。 在本地或者在优酷、爱奇艺、土豆等中国视频托管网站中托管视频，或使用 Azure 媒体服务托管视频。 使用搜索引擎优化 (SEO) 审核工具针对百度（在中国最常用的搜索引擎）优化自己的网站。
- 创建适应中国环境的社交网络内容。 全球流行的社交网络，例如 Facebook、Twitter 和 Instagram，已被封锁。 创建专门针对中国社交网络（例如微信和新浪微博）定制的社交营销策略。 Azure 目前不提供本地社交网络集成（即社交标识提供者）。

## <a name="check-endpoints-in-azure"></a>检查 Azure 中的终结点
Microsoft Azure 中国世纪互联与全球 Azure 有所不同，因此，必须更改全球 Azure 源（例如示例代码或已发布的文档）中的所有 Azure 服务终结点。 

下表显示了要更改的终结点。 

另请参阅：
- [Azure 中国应用程序的开发人员说明](https://msdn.microsoft.com/library/azure/dn578439.aspx)
- [中国的 Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=42064) 
- [开发人员指南](https://www.azure.cn/documentation/articles/developerdifferences/#dev-guide)（中文版）。


| 服务类别                      | 全球 Azure URI                                                                                                        | Azure URI（中国）                                                                                                                                  |
|---------------------------------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure（通用）                    | \*.windows.net                                                                                                          | \*.chinacloudapi.cn                                                                                                                                   |
| Azure 计算                         | \*.cloudapp.net                                                                                                         | \*.chinacloudapp.cn                                                                                                                                   |
| Azure 存储                         | \*.blob.core.windows.net \*.queue.core.windows.net \*.table.core.windows.net                                            | \*.blob.core.chinacloudapi.cn \*.queue.core.chinacloudapi.cn \*.table.core.chinacloudapi.cn                                                           |
| Azure 服务管理              | https://management.core.windows.net                                                                                     | [https://management.core.chinacloudapi.cn](https://management.core.chinacloudapi.cn/)                                                                 |
| Azure 资源管理器          | [https://management.azure.com](https://management.azure.com/)                                                           | [https://management.chinacloudapi.cn](https://management.chinacloudapi.cn/)                                                                           |
| Azure 管理门户               | [http://manage.windowsazure.com](http://manage.windowsazure.com/) [https://portal.azure.com](https://portal.azure.com/) | [http://manage.windowsazure.cn](http://manage.windowsazure.cn/) [https://portal.azure.cn](https://portal.azure.cn/)                                   |
| SQL 数据库                          | \*.database.windows.net                                                                                                 | \*.database.chinacloudapi.cn                                                                                                                          |
| SQL Azure 数据库管理 API           | [https://management.database.windows.net](https://management.database.windows.net/)                                     | [https://management.database.chinacloudapi.cn](https://management.database.chinacloudapi.cn/)                                                         |
| Azure 服务总线                     | \*.servicebus.windows.net                                                                                               | \*.servicebus.chinacloudapi.cn                                                                                                                        |
| Azure 访问控制服务          | \*.accesscontrol.windows.net                                                                                            | \*.accesscontrol.chinacloudapi.cn                                                                                                                     |
| Azure HDInsight                       | \*.azurehdinsight.net                                                                                                   | \*.azurehdinsight.cn                                                                                                                                  |
| SQL 数据库导入/导出服务终结点 |                                                                                                                         |  1.中国东部：[https://sh1prod-dacsvc.chinacloudapp.cn/dacwebservice.svc](https://sh1prod-dacsvc.chinacloudapp.cn/dacwebservice.svc) <br>2.中国北部：[https://bj1prod-dacsvc.chinacloudapp.cn/dacwebservice.svc](https://bj1prod-dacsvc.chinacloudapp.cn/dacwebservice.svc) |
| MySQL PaaS                            |                                                                                                                         | \*.mysqldb.chinacloudapi.cn                                                                                                                           |
| Azure Service Fabric 群集          | \*.cloudapp.azure.com                                                                                                   | \*.chinaeast.chinacloudapp.cn                                                                                                                         |
| Azure Active Directory (AD)           | \*.onmicrosoft.com                                                                                                      | \*.partner.onmschina.cn                                                                                                                               |
| Azure AD 登录                        | [https://login.windows.net](https://login.windows.net/)                                                                 | [https://login.chinacloudapi.cn](https://login.chinacloudapi.cn/)                                                                                     |
| Azure AD 图形 API                    | [https://graph.windows.net](https://graph.windows.net/)                                                                 | [https://graph.chinacloudapi.cn](https://graph.chinacloudapi.cn/)                                                                                     |
| 认知服务                    | <https://api.projectoxford.ai/face/v1.0>                                                                                | <https://api.cognitive.azure.cn/face/v1.0>                                                                                                            |
| 使用 PowerShell 登录： <br>- 经典 Azure <br>- Azure 资源管理器  <br>- Azure AD|    - Add-AzureAccount<br>- Add-AzureRmAccount <br> - Connect-msolservice                                                                                                                       |  - Add-AzureAccount -Environment AzureChinaCloud  <br> - Add-AzureRmAccount -Environment AzureChinaCloud <br>- Connect-msolservice -AzureEnvironment AzureChinaCloud |               |                                                                                                                         |
 |


## <a name="next-steps"></a>后续步骤
- [开发人员指南](https://www.azure.cn/documentation/articles/developerdifferences/#dev-guide)（中文版）
- [中国的 Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=42064)
- [管理性能和连接](/azure/china/china-how-to-manage-performance)
- [Azure 体系结构中心](https://docs.microsoft.com/en-us/azure/architecture/)
