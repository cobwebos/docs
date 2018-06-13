---
title: Azure Stack 中的客户计费和退款 | Microsoft Docs
description: 了解如何从 Azure Stack 中检索资源使用情况信息。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: eca335797f48b7c44351655f17c8b6499f3d5999
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877477"
---
# <a name="usage-and-billing-in-azure-stack"></a>Azure Stack 中的使用情况和计费

本指南介绍了如何将 Azure 堆栈用户计费针对资源使用情况。 你可以了解以便进行分析和计费的访问的计费信息的方式。

Azure 堆栈收集和分组，所使用的所有资源的使用情况数据，并将此数据转发到 Azure 商务。 Azure 商务账单你为 Azure 堆栈使用相同的方式为它将向你收取 Azure 使用情况。

你还可以获取使用情况数据和导出到你自己的计费或费用使用计费适配器，备份系统或将其导出到 Microsoft Power BI 之类的商业智能工具并将其用于分析。


## <a name="usage-pipeline"></a>使用情况管道

Azure 堆栈中的每个资源提供程序会发出每个资源使用情况的使用情况数据。 使用率服务定期 （每小时和每天） 聚合使用情况数据，并将其存储使用情况数据库中。 Azure 堆栈操作员和用户可以通过 Azure 堆栈资源使用情况 Api 访问存储的使用率数据。 

如果你有[向 Azure 注册了 Azure 堆栈实例](azure-stack-register.md)，Azure 堆栈配置为向 Azure 商务发送使用率数据。 数据上载到 Azure 后，你可以通过计费门户或通过使用 Azure 资源使用情况 Api 来访问它。 请参阅[使用情况数据报告](azure-stack-usage-reporting.md)主题，以了解更多有关哪些使用情况数据报告到 Azure。  

下图显示了使用情况管道中的关键组件： 

![使用情况管道](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>可以找到哪些使用情况信息，如何查找？

Azure Stack 资源提供程序（例如计算、存储和网络）每隔一小时为每个订阅生成使用情况数据。 使用情况数据包含有关使用例如资源名称、 使用、 订阅和使用的数量的资源信息。 若要了解有关米 ID 资源信息，请参见[使用情况 API 常见问题](azure-stack-usage-related-faq.md)文章。

在收集使用情况数据后，它将[报告给 Azure](azure-stack-usage-reporting.md)来生成帐单，可以通过 Azure 计费门户查看账单。 


> [!NOTE]
> 对于 Azure Stack 开发工具包和在容量模型下许可的 Azure Stack 集成系统用户，不需要进行使用情况数据报告。 若要详细了解 Azure Stack 中的许可，请参阅[打包和定价](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)数据表。

Azure 计费门户将显示可计费的资源的使用情况数据。 除了应计费资源之外，Azure Stack 还会捕获更广范围内资源的使用情况数据，可以通过 REST API 或 PowerShell 在 Azure Stack 环境中访问这些数据。 Azure 堆栈运算符可以获取所有用户的订阅使用情况数据。 单个用户只能获取其用法的详细信息。 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>使用情况报告多租户云服务提供商

多租户云服务提供程序 (CSP) 有许多客户使用 Azure 堆栈可能想要分别报告每个客户使用情况，以便该提供程序可以向收取使用情况与不同 Azure 订阅。 

每个客户将具有不同的 Azure Active Directory (Azure AD) 租户中由其标识。 Azure 堆栈支持分配给每个 Azure AD 租户的一个 CSP 订阅。 你可以将租户和其订阅添加到基 Azure 堆栈注册。 所有 Azure 堆栈都完成基本的注册。 如果订阅未注册为租户，用户仍可以使用 Azure 堆栈，其使用情况将发送到用于基注册的订阅。 


## <a name="next-steps"></a>后续步骤

[注册 Azure 堆栈](azure-stack-registration.md)

[向 Azure 报告 Azure Stack 使用情况数据](azure-stack-usage-reporting.md)

[提供程序资源使用情况 API](azure-stack-provider-resource-api.md)

[租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)

[有关使用情况的常见问题解答](azure-stack-usage-related-faq.md)