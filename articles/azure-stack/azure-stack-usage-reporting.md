---
title: 向 Azure 报告 Azure Stack 用量数据 | Microsoft Docs
description: 了解如何在 Azure Stack 中设置用量数据报告。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 602cd6c3b2be8881bebbcebe30ec2520358b731f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="report-azure-stack-usage-data-to-azure"></a>向 Azure 报告 Azure Stack 用量数据 

用量数据也称为消耗数据，表示使用的资源量。 

使用基于消耗的计费模式的 Azure Stack 多节点系统应向 Azure 报告用量数据，以便计费。  Azure Stack 操作员应将其 Azure Stack 实例配置为向 Azure 报告用量数据。

> [!NOTE]
> 根据即用即付付费模式购买许可证的 Azure Stack 多节点用户必须配置用量数据报告。 它是可选的容量模式下许可证的用户 (请参阅[如何购买页面](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)。 对于 Azure Stack 开发工具包用户，Azure Stack 操作员可以报告用量数据并测试此功能。 但是，用户无需为产生的任何用量付费。 


![计费流](media/azure-stack-usage-reporting/billing-flow.png)

用量数据通过 Azure Bridge 从 Azure Stack 发送到 Azure。 在 Azure 中，商务系统会处理用量数据并生成帐单。 生成帐单之后，Azure 订阅所有者可以从 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)查看和下载帐单。 若要了解 Azure Stack 的许可方式，请参阅 [Azure Stack 打包和定价文档](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409)。

## <a name="set-up-usage-data-reporting"></a>设置用量数据报告

若要设置用量数据报告，必须[将 Azure Stack 实例注册到 Azure](azure-stack-register.md)。 在注册过程中，将配置 Azure Stack 的 Azure Bridge 组件，以便将 Azure Stack 连接到 Azure 并发送用量数据。 会将以下用量数据从 Azure Stack 发送到 Azure：

- **计数 ID** – 已耗用的资源的唯一 ID。
- **数量**– 指定的资源使用量。
- **位置**– 其中部署当前 Azure 堆栈资源的位置。
- **资源 URI** – 完全限定的用法报告的资源 URI。
- **订阅 ID** – Azure 堆栈用户的订阅 ID。 这是本地（Azure Stack）订阅。
- **时间**– 的使用情况数据的开始和结束时间。 在 Azure Stack 中使用这些资源的时间与向商务系统报告用量数据的时间存在一定的延迟。 Azure Stack 每隔 24 小时聚合一次用量数据，而向 Azure 中的商务管道报告用量数据则需要额外的好几个小时。 因此，在午夜之前短暂发生的用量可能要在第二天才显示在 Azure 中。

## <a name="generate-usage-data-reporting"></a>生成用量数据报告

1. 若要测试用量数据报告，请在 Azure Stack 中创建一些资源。 例如，可以创建[存储帐户](azure-stack-provision-storage-account.md)、[Windows Server VM](azure-stack-provision-vm.md) 以及采用基本和标准 SKU 的 Linux VM，以查看核心用量的报告方式。 不同类型的资源的用量数据通过不同的计量器报告。

2. 让资源运行几个小时。 系统大约每隔一小时收集一次用量信息。 收集之后，此数据将传输到 Azure 并在 Azure 商务系统中处理。 此过程最长可能需要花费几个小时。

## <a name="view-usage---csp-subscriptions"></a>查看用量 - CSP 订阅

如果已使用 CSP 订阅注册 Azure Stack，则可以查看用量和费用，查看方法与查看 Azure 消耗量相同。 Azure Stack 用量将包含在可通过[合作伙伴中心](https://partnercenter.microsoft.com/partner/home)获取的发票和对帐文件中。 对帐文件每月更新。 如果需要访问最新的 Azure Stack 用量信息，可以使用合作伙伴中心 API。

   ![合作伙伴中心](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>查看使用情况 – 企业协议订阅

如果已使用企业协议订阅注册 Azure Stack，则可以在 [EA 门户](https://ea.azure.com/)中查看用量和费用。 Azure Stack 用量包含在高级下载内容中，Azure 用量包含在 EA 门户的报告部分下。 

## <a name="view-usage--other-subscriptions"></a>查看使用情况 – 其他订阅

如果已使用其他任何订阅类型（例如，即用即付订阅）注册 Azure Stack，则可以在 Azure 帐户中心查看用量和费用。 以 Azure 帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)，选择用于注册 Azure Stack 的 Azure 订阅。 可以查看 Azure Stack 用量数据，以及针对使用的每个资源收取的费用，如下图所示：

   ![计费流](media/azure-stack-usage-reporting/pricing-details.png)

对于 Azure Stack 开发工具包，我们不会收取 Azure Stack 资源费用，因此价格显示为 $0.00。 发布 Azure Stack 多节点的正式版后，可以看到其中每个资源的实际费用。

## <a name="which-azure-stack-deployments-are-charged"></a>哪些 Azure Stack 部署需要付费？

在 Azure Stack 开发工具包中使用资源无需付费。 对于 Azure Stack 多节点系统，工作负荷 VM、存储服务与应用服务需要付费。

## <a name="are-users-charged-for-the-infrastructure-vms"></a>使用基础结构 VM 是否需要付费？

不会。 某些 Azure Stack 资源提供程序 VM 的用量数据将报告给 Azure，但这些 VM 以及在部署期间为了启用 Azure Stack 基础结构所创建的 VM 不产生费用。  

用户只需为租户订阅下运行的 VM 付费。 必须将所有工作负荷部署在租户订阅之下，才符合 Azure Stack 的许可条款。

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>我有 Windows Server 的许可证，如何在 Azure Stack 上使用它？

使用现有许可证可避免生成用量计量值。 可以根据 [Azure Stack 许可指南](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409)的“在 Azure Stack 中使用现有软件”部分所述，在 Azure Stack 中使用现有的 Windows Server 许可证。 客户需要根据 [Windows Server 许可证的混合权益](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)主题中所述部署其 Windows Server 虚拟机，才能使用其现有许可证。

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>从哪个订阅中收取消耗的资源费用？
从[将 Azure Stack 注册到 Azure](azure-stack-register.md) 时提供的订阅收费。

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>哪些类型的订阅支持用量数据报告？

对于 Azure Stack 多节点，支持企业协议 (EA) 和 CSP 订阅。 对于 Azure Stack 开发工具包，企业协议 (EA)、即用即付、CSP 和 MSDN 订阅支持用量数据报告。

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>是否可以在主权云中运行用量数据报告？

在 Azure Stack 开发工具包中，用量数据报告需要全球 Azure 系统中创建的订阅。 在某个主权云（Azure 政府、Azure 德国和 Azure 中国云）中创建的订阅无法注册到 Azure，因此不支持用量数据报告。

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>用户如何在 Azure 计费门户中确定 Azure Stack 用量数据？

用户可以在用量详细信息文件中查看 Azure Stack 用量数据。 若要了解如何获取用量详细信息文件，请参阅[从 Azure 帐户中心下载用量文件](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv)一文。 用量详细信息文件包含的 Azure Stack 计量可用于标识 Azure Stack 存储和 VM。 在 Azure Stack 中使用的所有资源都在名为“Azure Stack”的区域下报告。

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>为何 Azure Stack 中报告的用量与从 Azure 帐户中心生成的报告不匹配？

Azure Stack 用量 API 报告用量数据的时间与 Azure 帐户中心报告用量数据的时间始终存在延迟。 之所以发生这种延迟，是因为需要将用量数据从 Azure Stack 上传到 Azure 商务系统。 由于这种延迟，在午夜之前短暂发生的用量可能要在第二天才显示在 Azure 中。 如果使用 [Azure Stack 用量 API](azure-stack-provider-resource-api.md) 并将结果与 Azure 计费门户中报告的用量相比较，则可能会看到差异。

## <a name="next-steps"></a>后续步骤

* [提供者使用情况 API](azure-stack-provider-resource-api.md)  
* [租户使用情况 API](azure-stack-tenant-resource-usage-api.md)
* [使用情况常见问题](azure-stack-usage-related-faq.md)
* [以云服务提供商身份管理使用情况和计费](azure-stack-add-manage-billing-as-a-csp.md)
