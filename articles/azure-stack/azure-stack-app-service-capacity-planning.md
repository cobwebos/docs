---
title: 针对 Azure Stack 中的 Azure 应用服务服务器角色的容量计划 | Microsoft Docs
description: 针对 Azure Stack 中的 Azure 应用服务服务器角色的容量计划
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
ms.date: 06/28/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: f54481fe59df21b500ee860d1e9a202ed32bdd87
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097142"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>针对 Azure Stack 中的 Azure 应用服务服务器角色的容量计划

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

若要设置 Azure 堆栈上的 Azure App Service 的生产就绪部署，你必须规划希望系统上以便支持的容量。  

本文提供的最小计算实例和计算用于任何生产部署时，你应的 Sku 的指导。

可以使用这些准则来计划应用服务容量策略。 将来版本的 Azure Stack 将支持应用服务的高可用性选项。

| 应用服务服务器角色 | 建议的最少实例数 | 建议的计算 SKU|
| --- | --- | --- |
| 控制器 | 2 | A1 |
| 前端 | 2 | A1 |
| 管理 | 2 | A3 |
| 发布者 | 2 | A1 |
| Web 辅助角色 - 共享 | 2 | A1 |
| Web 辅助角色 - 专用 | 每层 2 个 | A1 |

## <a name="controller-role"></a>控制器角色

**推荐的最小**: A1 标准的两个实例

Azure 应用服务控制器通常消耗较少的 CPU、内存和网络资源。 但是，为了实现高可用性，必须具有两个控制器。 两个控制器也是允许的最大控制器数。 在部署期间可以直接通过安装程序创建第二个网站控制器。

## <a name="front-end-role"></a>前端角色

**推荐的最小**: A1 标准的两个实例

前端根据 Web 辅助角色可用性将请求路由到 Web 辅助角色。 为实现高可用性，应配备一个以上前端，并且可以配备两个以上。 对于容量计划用途，请考虑每个核心每秒可以处理大约 100 个请求。

## <a name="management-role"></a>管理角色

**推荐的最小**： 两个标准 A3 实例

Azure 应用服务管理角色负责管理应用服务 Azure 资源管理器和 API 终结点、门户扩展（管理员门户、租户门户、Functions 门户）和数据服务。 在生产环境中，管理服务器角色通常仅需要大约 4-GB RAM。 但是，当执行许多管理任务（例如创建网站）时，它可能会经历很高的 CPU 利用率水平。 为实现高可用性，应当将一台以上服务器分配给此角色，并且每台服务器至少两个核心。

## <a name="publisher-role"></a>发布服务器角色

**推荐的最小**: A1 标准的两个实例

如果多个用户同时发布，发布服务器角色可能会遇到大量的 CPU 使用率。 为实现高可用性，提供了一个以上发布服务器角色。  发布服务器仅处理 FTP/FTPS 流量。

## <a name="web-worker-role"></a>Web 辅助角色

**推荐的最小**: A1 标准的两个实例

为实现高可用性，应当配备至少四个 Web 辅助角色，两个用于共享网站模式，两个用于你计划提供的每个专用辅助角色层。 共享计算模式和专用计算模式向租户提供不同的服务级别。 如果您的许多客户，你可能需要更多 Web 工作进程：

- 使用专用的计算模式辅助角色层 （它们是资源密集型。）
- 在共享的计算模式下运行。

在用户为专用计算模式 SKU 创建应用服务计划后，在该应用服务计划中指定的 Web 辅助角色将不再可供用户使用。

若要在消耗计划模型下向用户提供 Azure Functions，则必须部署共享 Web 辅助角色。

决定要使用的共享 Web 辅助角色数量时，请查看以下注意事项：

- **内存**：内存是对 Web 辅助角色最重要的资源。 从磁盘交换虚拟内存时，内存不足会影响网站性能。 每台服务器的操作系统需要大约 1.2 GB 的 RAM。 可以使用高于此阈值的 RAM 来运行网站。
- **活动网站的百分比**：通常，在 Azure Stack 部署中，Azure 应用服务中大约有 5% 的应用程序处于活动状态。 但是，在任意给定时刻处于活动状态的应用程序所占百分比可能更高或更低。 活动应用程序所占百分比为 5% 时，在 Azure Stack 部署中，要放置在 Azure 应用服务中的最大应用程序数应当小于：
  - 活动网站数量的 20 倍 (5 x 20 = 100)。
- **平均内存占用**：在生产环境中观察到的应用程序平均内存占用大约为 70 MB。 使用此需求量，所有 Web 辅助进程角色计算机或 Vm 间分配的内存可计算，如下所示：

    *预配的应用程序数 * 70 MB * 5% - (Web 辅助角色数 * 1044 MB)*

   例如，如果在运行 10 个 Web 辅助角色的环境中有 5,000 个应用程序，则每个 Web 辅助角色 VM 应当具有 7060 MB RAM：

   5000 * 70 * 0.05 – (10 * 1044) = 7060 （= 大约为 7 GB）

   有关添加更多辅助实例的信息，请参阅[添加多个辅助角色](azure-stack-app-service-add-worker-roles.md)。

## <a name="file-server-role"></a>文件服务器角色

对于文件服务器角色中，可使用独立文件服务器，进行开发和测试，例如，在部署 Azure App Service 上 Azure 堆栈开发工具包时可以使用此模板中- <https://aka.ms/appsvconmasdkfstemplate>。 对于生产用途，应当使用预先配置的 Windows 文件服务器，或使用预先配置的非 Windows 文件服务器。

在生产环境中，文件服务器角色会经历密集的磁盘 I/O。 因为它容纳着用户网站的所有内容和应用程序文件，则应当为此角色预先配置下列项之一：

- Windows 文件服务器
- Windows 文件服务器群集
- 非 Windows 文件服务器
- 非 Windows 文件服务器群集
- NAS （网络附加存储） 设备

有关详细信息，请参阅[设置的文件服务器](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)。

## <a name="next-steps"></a>后续步骤

[开始使用 Azure Stack 上的应用服务之前](azure-stack-app-service-before-you-get-started.md)
