---
title: 保护 Azure App Service web 应用和 Api
description: 本文介绍如何在 Azure 安全中心中开始保护 Azure App Service 的 web 应用和 Api。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 81ef598c846d98548be2d3e7647166d655398921
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912812"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>保护 Azure App Service web 应用和 Api

Azure App Service 是一种完全托管的平台，用于生成和托管 web 应用和 Api，而无需考虑管理基础结构。 它提供管理、监视和操作见解，以满足企业级的性能、安全性和合规性要求。 有关详细信息，请参阅[Azure App Service](https://azure.microsoft.com/services/app-service/)。

若要为 Azure App Service 计划启用高级威胁防护，必须执行以下操作：

* 订阅 Azure 安全中心的标准定价层
* 启用应用服务计划，如下所示。 安全中心与应用服务进行本机集成，无需部署和载入-集成是透明的。
* 具有与专用计算机关联的应用服务计划。 支持的计划包括：基本、标准、高级、隔离或 Linux。 安全中心不支持免费计划、共享计划或消耗计划。 有关详细信息，请参阅[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。

启用应用服务计划后，安全中心会评估应用服务计划涵盖的资源并根据其发现生成安全建议。 安全中心保护你的应用服务正在其中运行的 VM 实例和管理界面。 此外，它还监视向/从应用服务中运行的应用发送的请求和响应。

安全中心利用云的规模和 Azure 作为云提供商的可见性来监视常见的 web 应用攻击。 安全中心可能会发现对你的应用程序的攻击并识别新兴攻击，即使攻击者处于侦测阶段，扫描还可以识别多个 Azure 托管应用程序的漏洞。 作为 Azure 本机服务，安全中心也处于独一无二的位置，可提供基于主机的安全分析，涵盖此 PaaS 的基础计算节点，使安全中心能够检测到对已被利用的 web 应用程序的攻击。 有关 Azure App Service 安全中心威胁检测警报的详细信息，请参阅[云本机计算的威胁检测](security-center-alerts-compute.md#azure-app-service-)。


## <a name="enabling-monitoring-and-protection-of-app-service"></a>启用应用服务的监视和保护

1. 在 Azure 门户中，选择 "安全中心"。
2. 请参阅**定价 & 设置**并选择订阅。
3. 在“定价层”下面的“应用服务”行中，将计划切换为“已启用”。

    [![在标准层订阅中启用应用服务](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> 为**资源数量**列出的实例数表示此订阅的所有应用服务计划中的计算实例总数，在打开 "定价层" 边栏选项卡时，将会运行。
>
> Azure App Service 提供各种计划。 应用服务计划定义要运行的 web 应用的一组计算资源。 它们等效于传统 web 托管中的服务器场。 可将一个或多个应用配置为在相同的计算资源中（或相同的应用服务计划中）运行。
>
>若要验证计数，请转到 Azure 门户中的 "应用服务计划"，可以在其中查看每个计划使用的计算实例数。 






若要禁用应用服务的监视和建议，请重复此过程，并将**应用服务**计划切换为“已禁用”。



## <a name="see-also"></a>另请参阅
本文介绍了如何使用 Azure 安全中心的监视功能。 若要了解有关 Azure 安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)：了解如何在 Azure 安全中心配置安全设置。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [应用服务](security-center-virtual-machine-protection.md#app-services)：查看具有运行状况摘要的应用服务环境的列表。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题](security-center-faq.md)：查找有关使用服务的常见问题。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/):.查找关于 Azure 安全性及合规性的博客文章
