---
title: 在 Azure 安全中心保护应用服务 | Microsoft Docs
description: 本文帮助你开始在 Azure 安全中心保护应用服务。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: monhaber
ms.openlocfilehash: ea738535ae9326109a7c3fdd0b5d0c4f4691fdf0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878624"
---
# <a name="protect-app-service-with-azure-security-center"></a>使用 Azure 安全中心保护应用服务
本文帮助你使用 Azure 安全中心来监视和保护在应用服务上运行的应用程序。

应用服务允许采用所选编程语言生成和托管 Web 应用程序，无需管理基础结构。 应用服务提供自动缩放和高可用性，支持 Windows 和 Linux，以及从 GitHub、Azure DevOps 或任何 Git 存储库进行的自动部署。 

攻击者经常利用 Web 应用程序中的漏洞，因为这些应用程序在 Internet 上几乎为每家组织提供一个通用动态接口。 向应用服务上运行的应用程序发出的请求将会通过全球 Azure 数据中心部署的多个网关发送。这些网关负责将每个请求路由到其相应的应用程序。 

Azure 安全中心可以在 VM 或按需实例的沙盒中运行评估，并针对应用服务中运行的应用程序提供建议。 安全中心利用 Azure 云提供商提供的可见性分析应用服务的内部日志，以监视多个目标经常遭到的常见 Web 应用攻击。

安全中心利用云的规模来识别应用服务应用程序受到的攻击，并专注于新兴攻击；当攻击者处于侦测阶段时，它会执行扫描来识别 Azure 中托管的多个网站上的漏洞。 安全中心使用分析和机器学习模型来全面检测所有接口，使客户能够与其应用程序交互（不管是通过 HTTP 还是管理方法）。 此外，作为 Azure 中的第一方服务，安全中心还在独特的立场提供基于主机的安全分析（涵盖此 PaaS 的底层计算节点），因此，安全中心可以检测已遭恶意利用的 Web 应用程序受到的攻击。

## <a name="prerequisites"></a>必备组件

若要监视和保护应用服务，必须创建一个与专用计算机关联的应用服务计划。 这些计划包括：“基本”、“标准”、“高级”、“独立”或“Linux”。 Azure 安全中心不支持“免费”、“共享”或“消耗”计划。 有关详细信息，请参阅[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。

## <a name="security-center-protection"></a>安全中心保护

Azure 安全中心保护运行应用服务的 VM 实例以及管理接口。 此外，它还监视向/从应用服务中运行的应用发送的请求和响应。

安全中心原生与应用服务集成，因此不需要部署和载入 - 集成是完全透明的。



## <a name="enabling-monitoring-and-protection-of-app-service"></a>启用应用服务的监视和保护

1. 在 Azure 中，选择“安全中心”。
2. 转到“安全策略”并选择一个订阅。
3. 在订阅行的末尾，单击“编辑设置”。
4. 在“定价层”下面的“应用服务”行中，将计划切换为“已启用”。

![应用服务切换](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> 为资源数量列出的实例数表示在打开定价层边栏选项卡时，处于活动状态的相关应用服务实例的数目。 由于此数字会根据所选缩放选项而变化，因此，付费的实例数会相应地修改。

若要禁用应用服务的监视和建议，请重复此过程，并将**应用服务**计划切换为“已禁用”。



## <a name="see-also"></a>另请参阅
本文介绍了如何使用 Azure 安全中心的监视功能。 若要了解更多有关 Azure 安全中心的详细信息，请参阅以下内容：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)：了解如何在 Azure 安全中心配置安全设置。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [应用服务](security-center-virtual-machine-protection.md#app-services)：查看包含运行状况摘要的应用服务环境列表。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状况。
* [Azure 安全中心常见问题解答](security-center-faq.md)：查找有关如何使用服务的常见问题。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/)：查找关于 Azure 安全性及合规性的博客文章。
