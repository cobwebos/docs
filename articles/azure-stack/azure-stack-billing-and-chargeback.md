---
title: 客户计费和 Azure Stack 中的 chargeback |Microsoft Docs
description: 了解如何从 Azure Stack 中检索资源使用情况信息。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: a5f3b206b83beb15ee3b29d5d5b9e389e85a91fb
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466978"
---
# <a name="usage-and-billing-in-azure-stack"></a>Azure Stack 中的使用情况和计费

此文介绍向 Azure Stack 用户计收资源使用费的方式。 可在其中了解如何访问计费信息，以分析费用和申请退款。

Azure Stack 收集，并使用资源的使用情况数据进行分组。 然后 Azure Stack 将此数据转发到 Azure Commerce。 Azure Commerce 计收 Azure Stack 使用情况与它向你收费的 Azure 使用情况的方式相同。

此外可以获取使用情况数据和导出到您自己的计费或费用使用计费适配器备份系统或将其导出到 Microsoft Power BI 等的商业智能工具。

## <a name="usage-pipeline"></a>使用情况管道

Azure Stack 中的每个资源提供程序会根据资源使用情况发布使用情况数据。 使用情况服务定期 （每小时或每天） 聚合使用情况数据，并将它存储使用情况数据库中。 Azure Stack 操作员和用户可以通过 Azure Stack 资源使用情况 Api 访问存储的使用情况数据。 

如果已[将 Azure Stack 实例注册到 Azure](azure-stack-register.md)，则 Azure Stack 会配置为将使用情况数据发送到 Azure Commerce。 将数据上传到 Azure 后，您可以通过计费门户或通过使用 Azure 资源使用状况 Api 来访问它。 若要详细了解哪些使用情况数据报告到 Azure，请参阅[用量数据报告](azure-stack-usage-reporting.md)。  

下图显示了使用情况管道中的关键组件： 

![使用情况管道](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>可以找到哪些使用情况信息，如何查找？

Azure Stack 资源提供程序 （如计算、 存储和网络） 生成使用情况数据以小时为间隔为每个订阅。 使用情况数据包含有关资源使用，例如资源名称、 订阅和使用的数量的信息。 若要了解计量 ID 资源，请参阅[使用情况 API 常见问题解答](azure-stack-usage-related-faq.md)。

在收集使用情况数据后，它将[报告给 Azure](azure-stack-usage-reporting.md)来生成帐单，可以通过 Azure 计费门户查看账单。 

> [!NOTE]  
> 使用情况数据报告不需要为 Azure Stack 开发工具包 (ASDK) 和在容量模型下获得许可证的 Azure Stack 集成系统用户。 若要详细了解 Azure Stack 中的许可，请参阅[打包和定价](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)数据表。

Azure 计费门户显示应计费资源的使用情况数据。 除了应计费资源，Azure Stack 还会捕获更广泛的一组资源，使用户能够通过 REST Api 或 PowerShell cmdlet 在 Azure Stack 环境中的使用情况数据。 Azure Stack 操作员可以获取所有用户订阅的使用情况数据。 单个用户只能获取其自己的使用情况详细信息。 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>多租户云服务提供商的使用情况报告

有许多 Azure Stack 客户的多租户云服务提供商 (CSP) 可以单独报告每个客户的使用情况，以便向不同的 Azure 订阅收取使用费。 

每个客户具有不同的 Azure Active Directory (Azure AD) 租户表示的标识。 Azure Stack 支持向每个 Azure AD 租户分配一个 CSP 订阅。 可将租户及其订阅添加到基本 Azure Stack 注册。 将会针对所有 Azure Stack 订阅执行基本注册。 如果订阅未注册为租户中，用户仍可以使用 Azure Stack，其使用情况将发送到用于基本注册的订阅。 

## <a name="next-steps"></a>后续步骤

- [注册到 Azure Stack](azure-stack-registration.md)
- [向 Azure 报告 Azure Stack 使用情况数据](azure-stack-usage-reporting.md)
- [提供程序资源使用情况 API](azure-stack-provider-resource-api.md)
- [租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)
- [有关使用情况的常见问题解答](azure-stack-usage-related-faq.md)