---
title: Azure 堆栈中的 Azure App Service 服务器角色的容量规划 |Microsoft 文档
description: Azure 堆栈中的 Azure App Service 服务器角色的容量规划
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 8926955d5e0260b5971e07b6988bb21df9980847
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29388577"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Azure 堆栈中的 Azure App Service 服务器角色的容量规划
*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

若要设置 Azure 堆栈上的 Azure App Service 的生产就绪部署，你必须规划希望系统上以便支持的容量。  下面是实例和计算用于任何生产部署时，你应的 Sku 的最小数量的指南。

你可以规划 App Service 容量策略使用这些准则。 Azure 堆栈的未来版本将提供 App Service 的高可用性选项。

| App Service 服务器角色 | 建议的实例数最小值 | 建议的计算 SKU|
| --- | --- | --- |
| 控制器 | 2 | A1 |
| 前端 | 2 | A1 |
| 管理 | 2 | A3 |
| 发布者 | 2 | A1 |
| Web 辅助进程的共享 | 2 | A1 |
| Web 辅助进程的专用 | 每层 2 | A1 |

## <a name="controller-role"></a>控制器角色

**推荐的最小**: A1 标准的两个实例

Azure 应用程序服务控制器遇到的情况通常 CPU、 内存和网络资源用量较低。 但是，以实现高可用性，你必须具有两个控制器。 两个控制器也是最大允许的控制器数。 你可以创建第二个网站控制器直接从安装程序在部署过程。

## <a name="front-end-role"></a>前端角色

**推荐的最小**: A1 标准的两个实例

前端根据将请求路由到 Web 工作进程 Web 工作进程的可用性。 为实现高可用性，应配备多个前端，并可以有两个以上。 对于容量规划用途，请考虑每个核心可处理大约 100 个请求每秒。

## <a name="management-role"></a>管理角色

**推荐的最小**: A3 的两个实例

Azure 应用程序服务管理角色负责应用服务 Azure 资源管理器和 API 终结点、 门户 （管理员、 租户、 函数门户） 的扩展和数据服务。 管理服务器角色通常需要仅涉及在生产环境中的 4 GB RAM。 但是，它可能会遇到高 cpu 利用率水平，在执行大量管理任务 （如网站创建） 时。 为实现高可用性，应具有分配给此角色的多个服务器和每个服务器至少两个核心。

## <a name="publisher-role"></a>发布服务器角色

**推荐的最小**: A1 的两个实例

如果多个用户同时发布，发布服务器角色可能会遇到大量的 CPU 使用率。 为实现高可用性，提供多个发布服务器角色。  发布服务器仅处理 FTP/FTPS 流量。

## <a name="web-worker-role"></a>Web 工作进程角色

**推荐的最小**: A1 的两个实例

为实现高可用性，你应至少配备四个 Web 工作进程角色，两个用于共享网站模式，两个用于每个专用辅助角色层你计划提供。 共享和专用的计算模式向租户提供服务的级别不同。 如果你有许多客户，你可能需要更多 Web 工作进程：
 - 使用专用的计算模式辅助角色层 （它们都是资源密集型）
 - 在共享的计算模式下运行。

之后用户已创建 App Service 计划为专用的计算模式 SKU，指定在于 App Service 计划将不再可供用户的 Web 辅助进程数。

若要在消耗计划模型中向用户提供 Azure 函数，你必须部署共享 Web 工作进程。

在上的共享 Web 工作进程角色数决定时要使用，查看这些注意事项：

- **内存**： 内存是对 Web 工作进程角色最重要的资源。 内存不足在从磁盘交换虚拟内存时影响网站性能。 每个服务器的操作系统需要大约需要 1.2 GB 的 RAM。 此阈值以上的 RAM 可用于运行网站。
- **活动网站的百分比**： 通常，大约 5%的 Azure 堆栈部署在 Azure App Service 中的应用程序处于活动状态。 但是，任何给定时刻处于活动状态的应用程序的百分比可能要高或较低。 替换为活动的应用程序的比率为 5%，应为应用程序置于 Azure 堆栈部署上的 Azure 应用程序服务的最大数量小于：
    - 20 倍的活动网站 (5 x 20 = 100) 数。
- **平均内存占用量**： 在生产环境中观察到的应用程序的平均内存占用量为约为 70 MB。 因此，可以按以下方式计算所有 Web 辅助进程角色计算机或 Vm 间分配的内存：

    *已设置应用程序数目 * 70 MB * 5%-(的 Web 工作进程角色数 * 1044 MB)*

   例如，如果有 5000 上运行 10 个 Web 工作进程角色的环境中的应用程序，则每个 Web 工作进程角色 VM 应有 7060 MB 的 RAM:

   5,000 * 70 * 0.05 – (10 * 1044) = 7060 (=about 7 GB)

   有关添加更多辅助实例的信息，请参阅[添加多个辅助角色](azure-stack-app-service-add-worker-roles.md)。

## <a name="file-server-role"></a>文件服务器角色

对于文件服务器角色中，可使用独立文件服务器，进行开发和测试，例如，在部署 Azure App Service 上 Azure 堆栈开发工具包时可以使用此模板中-https://aka.ms/appsvconmasdkfstemplate。 对于生产用途，应使用预先配置的 Windows 文件服务器或预先配置的非 Windows 文件服务器。

在生产环境中，大量磁盘 i/o 数量很文件服务器角色。 由于它容纳所有用户网站的内容和应用程序文件，你应预先配置为此角色下列项之一：
- Windows 文件服务器
- 文件服务器群集
- 非 Windows 文件服务器
- 文件服务器群集
- NAS （网络附加存储） 设备的详细信息，请参阅[设置的文件服务器](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)。

## <a name="next-steps"></a>后续步骤

[之前开始使用 Azure 堆栈上的 App Service](azure-stack-app-service-before-you-get-started.md)
