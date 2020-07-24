---
title: 保护 Azure 应用服务 Web 应用和 API
description: 本文帮助你开始在 Azure 安全中心保护 Azure 应用服务 Web 应用和 API。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 35b91aab8c228fc7dced5dfe06e33f939f4634f5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080788"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>保护 Azure 应用服务 Web 应用和 API

Azure 应用服务是一种完全托管的平台，用于生成和托管 Web 应用和 API，无需考虑基础结构的管理。 它提供管理、监视和操作见解，以满足企业级的性能、安全性和符合性要求。 有关详细信息，请参阅 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)。

若要为 Azure App Service 计划启用高级威胁防护，必须执行以下操作：

* 订阅 Azure 安全中心的标准定价层
* 启用应用服务计划，如下所示。 安全中心与应用服务进行本机集成，无需部署和载入-集成是透明的。
* 具有与专用计算机关联的应用服务计划。 支持的计划包括：基本、标准、高级、隔离或 Linux。 安全中心不支持免费计划、共享计划或消耗计划。 有关详细信息，请参阅[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。

启用应用服务计划后，安全中心会评估应用服务计划涵盖的资源并根据其发现生成安全建议。 安全中心保护运行应用服务的 VM 实例以及管理接口。 此外，它还监视向/从应用服务中运行的应用发送的请求和响应。

安全中心利用云的规模和 Azure 作为云提供商拥有的可见性来监视常见的 Web 应用攻击。 安全中心可能会发现对你的应用程序的攻击并识别正在出现的攻击，即使攻击者处于侦测阶段，正在通过扫描来识别多个 Azure 托管应用程序的漏洞。 作为 Azure 本机服务，安全中心还在独特的立场提供基于主机的安全分析（涵盖此 PaaS 的底层计算节点），因此，安全中心可以检测已遭恶意利用的 Web 应用程序受到的攻击。 有关更多详细信息，请参阅[Azure App Service 威胁防护](threat-protection.md#app-services)。


## <a name="enabling-monitoring-and-protection-of-app-service"></a>启用应用服务的监视和保护

1. 在 Azure 门户中，选择“安全中心”。
2. 转到“定价和设置”并选择一个订阅。****
3. 在“定价层”下面的“应用服务”行中，将计划切换为“已启用”。************

    [![启用标准层订阅中的应用服务](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> 针对“资源数量”列出的实例数表示此订阅的所有应用服务计划中的计算实例总数，在打开定价层边栏选项卡时运行。****
>
> Azure 应用服务提供各种计划。 应用服务计划为要运行的 Web 应用定义一组计算资源。 它们等效于传统 Web 托管中的服务器场。 可将一个或多个应用配置为在相同的计算资源中（或相同的应用服务计划中）运行。
>
>若要验证计数，请转到 Azure 门户中的 "应用服务计划"，可以在其中查看每个计划使用的计算实例数。 






若要禁用应用服务的监视和建议，请重复此过程，并将**应用服务**计划切换为“已禁用”。****



## <a name="see-also"></a>另请参阅
本文介绍了如何使用 Azure 安全中心的监视功能。 若要了解有关 Azure 安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)：了解如何在 Azure 安全中心配置安全设置。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [应用服务](security-center-virtual-machine-protection.md#app-services)：查看具有运行状况摘要的应用服务环境的列表。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状况。
* [Azure 安全博客](https://blogs.msdn.com/b/azuresecurity/)：查找有关 Azure 安全性和符合性的博客文章。
