---
title: 针对 Azure Stack 中的 Azure 应用服务服务器角色的容量计划 | Microsoft Docs
description: 针对 Azure Stack 中的 Azure 应用服务服务器角色的容量计划
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: a769bb4cce84fe78f442cce8440e6e828ed7f76d
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354132"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>针对 Azure Stack 中的 Azure 应用服务服务器角色的容量计划

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

若要在 Azure Stack 上设置 Azure 应用服务的生产就绪部署，则必须计划你期望系统支持的容量。  

本文提供了有关应当为任何生产部署使用的最少计算实例和计算 SKU 数量的指南。

可以使用这些准则来计划应用服务容量策略。

| 应用服务服务器角色 | 建议的最少实例数 | 建议的计算 SKU|
| --- | --- | --- |
| 控制器 | 2 | A1 |
| 前端 | 2 | A1 |
| 管理 | 2 | A3 |
| 发布者 | 2 | A1 |
| Web 辅助角色 - 共享 | 2 | A1 |
| Web 辅助角色 - 专用 | 每层 2 个 | A1 |

## <a name="controller-role"></a>控制器角色

**建议的最小值**：两个 A1 标准实例

Azure 应用服务控制器通常消耗较少的 CPU、 内存和网络资源。 但是，为了实现高可用性，必须具有两个控制器。 两个控制器也是允许的最大控制器数。 您可以创建第二个网站控制器直接从安装程序在部署过程。

## <a name="front-end-role"></a>前端角色

**建议的最小值**：两个 A1 标准实例

前端将请求路由到具体取决于 web 辅助角色可用性的 web 辅助进程。 以实现高可用性，应配备多个前端，并可以有两个以上。 对于容量计划用途，请考虑每个核心每秒可以处理大约 100 个请求。

## <a name="management-role"></a>管理角色

**建议的最小值**：两个 A3 标准实例

Azure 应用服务管理角色负责应用服务 Azure 资源管理器和 API 终结点、 门户扩展 （管理员、 租户、 Functions 门户） 和数据服务。 管理服务器角色通常仅需要大约 4 GB RAM，在生产环境中。 但是，当执行许多管理任务（例如创建网站）时，它可能会经历很高的 CPU 利用率水平。 为实现高可用性，应当将一台以上服务器分配给此角色，并且每台服务器至少两个核心。

## <a name="publisher-role"></a>发布服务器角色

**建议的最小值**：两个 A1 标准实例

如果许多用户同时发布，发布服务器角色可能会遇到高 CPU 使用率。 对于高可用性，请确保提供多个发布服务器角色，并且。 发布服务器仅处理 FTP/FTPS 流量。

## <a name="web-worker-role"></a>Web 辅助角色

**建议的最小值**：两个 A1 标准实例

以实现高可用性，应具有至少四个 web 辅助角色、 两个用于共享的网站模式和两个用于你计划提供每个专用辅助角色层。 共享和专用计算模式向租户提供不同级别的服务。 如果许多客户都是，则可能需要更多 web 辅助角色：

- 使用专用的计算模式辅助角色层 （这是需要消耗大量资源）。
- 在共享计算模式下运行。

用户已创建的专用的计算模式 SKU 应用服务计划后，不再可供用户在该应用服务计划中指定的 web 辅助进程数。

若要提供 Azure Functions 消耗计划模型中的用户，必须部署共享的 web 辅助角色。

如果决定要使用的共享的 web 辅助角色数，请查看这些注意事项：

- **内存**： 内存是最重要的资源的 web 辅助角色。 从磁盘交换虚拟内存时，内存不足会影响网站性能。 每台服务器的操作系统需要大约 1.2 GB 的 RAM。 可以使用高于此阈值的 RAM 来运行网站。
- **活动网站的百分比**：通常，在 Azure Stack 部署中，Azure 应用服务中大约有 5% 的应用程序处于活动状态。 但是，在任意给定时刻处于活动状态的应用程序所占百分比可能更高或更低。 使用活动的应用程序比率为 5%，将放置在 Azure 应用服务上的 Azure Stack 部署的应用程序的最大数目应不超过 20 次的活动网站 (5 x 20 = 100) 的数量。
- **平均内存占用**：在生产环境中观察到的应用程序平均内存占用大约为 70 MB。 使用此空间，在所有 web 辅助进程角色计算机或 Vm 之间分配的内存可以计算，如下所示：

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   例如，如果有 5,000 个应用程序运行 10 个 web 辅助角色的环境，每个 web 辅助角色 VM 应当具有 7060 MB RAM:

   `5,000 * 70 * 0.05 – (10 * 1044) = 7060 (= about 7 GB)`

   有关添加更多辅助角色实例的信息，请参阅[添加更多辅助角色](azure-stack-app-service-add-worker-roles.md)。

## <a name="file-server-role"></a>文件服务器角色

对于文件服务器角色，可以使用独立文件服务器，用于开发和测试;例如，部署 Azure 应用服务在 Azure Stack 开发工具包 (ASDK) 时可以使用此模板： https://aka.ms/appsvconmasdkfstemplate。 对于生产用途，应使用预配置的 Windows 文件服务器或预配置的非 Windows 文件服务器。

在生产环境中，文件服务器角色会经历密集的磁盘 I/O。 因为它容纳着用户网站的内容和应用程序文件的所有，应预配置此角色的以下资源之一：

- Windows 文件服务器
- Windows 文件服务器群集
- 非 Windows 文件服务器
- 非 Windows 文件服务器群集
- NAS （网络附加存储） 设备

有关详细信息，请参阅[预配文件服务器](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)。

## <a name="next-steps"></a>后续步骤

请参阅以下文章了解详细信息：

[开始使用 Azure Stack 上的应用服务之前](azure-stack-app-service-before-you-get-started.md)
