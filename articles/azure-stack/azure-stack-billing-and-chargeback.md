---
title: "客户计费和 Azure 堆栈中的 Chargeback |Microsoft 文档"
description: "了解如何从 Azure 堆栈中检索资源使用情况信息。"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>使用情况和 Azure 堆栈中的计费

使用情况表示使用用户的资源的数量。 Azure 堆栈收集的每个用户的使用情况信息，并使用它来向他们计费。 本文介绍了如何对 Azure 堆栈用户进行计费的资源使用状况，以及用于分析、 chargeback 等访问计费信息的方式。

Azure 堆栈包含的基础结构来收集和聚合使用情况数据的所有资源，并将 Azure 商务到此数据转发。 您可以访问此数据和将其导出为计费系统，通过使用计费适配器，或将其导出到 Microsoft Power BI 之类的商业智能工具。 导出后，是用于分析此计费信息或将其传输到费用分摊系统中。

![计费适配器连接到计费的 Azure 堆栈的概念模型应用程序](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>使用管道

Azure 堆栈中的每个资源提供程序发出根据资源利用率的使用情况数据。 使用率服务定期 （每小时或每天） 聚合此使用数据，并将其存储使用情况数据库中。 通过使用使用情况 Api 以本地方式，可以通过 Azure 堆栈操作员和用户访问的存储使用情况数据。 

如果你有[向 Azure 注册了 Azure 堆栈实例](azure-stack-register.md)，用法桥配置为向 Azure 商务发送使用率数据。 有可用在 Azure 中的数据后，你可以通过计费门户或通过使用 Azure 使用情况 API 的访问它。 请参阅[使用情况数据报告](azure-stack-usage-reporting.md)主题，以了解更多有关哪些使用情况数据报告到 Azure。 

下图显示了在用法管道中的关键组件：

![使用管道](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>可以查找哪些使用情况信息，以及如何？

Azure 堆栈资源提供程序，如计算、 存储和网络，以小时为间隔为每个订阅生成使用情况数据。 使用情况数据包含有关使用例如资源名称、 使用订阅、 使用的数量的资源等信息。若要了解有关米 ID 资源信息，请参见[使用情况 API 常见问题](azure-stack-usage-related-faq.md)文章。 

在收集使用情况数据后，它是[报告给 Azure](azure-stack-usage-reporting.md)生成帐单，可以通过 Azure 计费门户中查看。 

> [!NOTE]
> 使用情况数据报告则不需要为 Azure 堆栈开发工具包和容量模式下获得许可证的 Azure 堆栈集成系统用户。 若要了解有关 Azure 堆栈中许可的更多信息，请参阅[打包和定价](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)数据表。

Azure 计费门户将显示只对计费的资源使用情况数据。 除了可计费的资源，Azure 堆栈捕获广泛的资源，你可以访问你通过 REST Api 或 PowerShell 的 Azure 堆栈环境中的使用情况数据。 Azure 堆栈运算符可以检索所有用户的订阅的使用情况数据，而用户可以获得仅其用法的详细信息。

## <a name="retrieve-usage-information"></a>检索使用情况信息

若要生成使用情况数据，应具有运行并积极地使用系统，例如的资源、 活动的虚拟机或包含一些数据等的存储帐户。如果你不确定是否具有运行 Azure 堆栈应用商店中任何资源，部署虚拟机 (VM)，并验证 VM 监视边栏选项卡以确保它在运行。 使用以下 PowerShell cmdlet 来查看使用情况数据：

1. [安装适用于 Azure 堆栈 PowerShell。](azure-stack-powershell-install.md)
2. [配置 Azure 堆栈用户](user/azure-stack-powershell-configure-user.md)或[Azure 堆栈运算符](azure-stack-powershell-configure-admin.md)PowerShell 环境 

3. 若要检索使用情况数据，请使用[Get UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell cmdlet:

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>后续步骤

[向 Azure 报告 Azure 堆栈使用情况数据](azure-stack-usage-reporting.md)

[提供程序资源使用情况 API](azure-stack-provider-resource-api.md)

[租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)

[使用情况相关的常见问题](azure-stack-usage-related-faq.md)

