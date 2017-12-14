---
title: "向 Azure 报告 Azure 堆栈使用情况数据 |Microsoft 文档"
description: "了解如何设置 Azure 堆栈中的报表的使用情况数据。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2A397C83-1A6C-4290-B0EF-FDC34EE290CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: mabrigg;AlfredoPizzirani
ms.openlocfilehash: 451e8c604e48d9cfc42284f2413fa5ed4fea03bc
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="report-azure-stack-usage-data-to-azure"></a>向 Azure 报告 Azure 堆栈使用情况数据 

使用情况数据，也称为消耗数据，表示使用的资源量。 

使用基于的消费的计费模式的 azure 堆栈多节点系统应报告使用情况数据到 Azure 计费目的。  Azure 堆栈运算符应配置到 Azure 的报告使用情况数据其 Azure 堆栈实例。

> [!NOTE]
> 使用情况数据报告是必需的 Azure 堆栈多节点许可证的用户作为你的使用付费模式下。 它是可选的容量模式下许可证的用户 (请参阅[如何购买页面](https://azure.microsoft.com/overview/azure-stack/how-to-buy/ to learn more about pricing in Azure Stack))。 对于 Azure 堆栈开发工具包用户，Azure 堆栈运算符可以报告使用情况数据，并测试功能。 但是，用户不会收取它们会产生任何使用。 


![计费流](media/azure-stack-usage-reporting/billing-flow.png)

使用情况数据从 Azure 堆栈发送到 Azure 通过 Azure 桥。 在 Azure 中，商务系统处理的使用情况数据，并生成帐单。 生成清单后，Azure 订阅所有者可以查看和下载从[Azure 帐户中心](https://account.windowsazure.com/Subscriptions)。 若要了解有关如何获取 Azure 堆栈的许可证，请参阅[Azure 堆栈打包和定价文档](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409)。

## <a name="set-up-usage-data-reporting"></a>设置使用情况数据报告

若要设置了使用情况数据报告，你必须[向 Azure 注册你的 Azure 堆栈实例](azure-stack-register.md)。 作为注册过程的一部分，Azure 堆栈，以连接到 Azure 的 Azure 堆栈并将发送使用情况数据，将该 Azure 桥组件配置。 以下使用情况数据从 Azure 堆栈发送到 Azure:

- **计数 ID** – 已耗用的资源的唯一 ID。
- **数量**– 指定的资源使用量。
- **位置**– 其中部署当前 Azure 堆栈资源的位置。
- **资源 URI** – 完全限定的用法报告的资源 URI。
- **订阅 ID** – Azure 堆栈用户的订阅 ID。 这是本地的 （Azure 堆栈） 订阅。
- **时间**– 的使用情况数据的开始和结束时间。 之间不存在一定的延迟时间和使用情况数据报告给商务时 Azure 堆栈中会占用这些资源。 每 24 小时的 azure 堆栈聚合使用情况数据和报表使用情况数据到 Azure 中的商务管道所需的其他几个小时。 因此，发生前不久午夜的使用情况可能会显示在 Azure 中下一天。

## <a name="generate-usage-data-reporting"></a>生成使用情况数据报告

1. 若要测试报告的使用情况数据，请在 Azure 堆栈中创建几个资源。 例如，你可以创建[存储帐户](azure-stack-provision-storage-account.md)， [Windows Server VM](azure-stack-provision-vm.md)和 Linux VM 使用基本和标准 Sku，若要查看如何报告内核使用情况。 不同类型的资源的使用情况数据报告在不同米下。

2. 将保留你运行几个小时内的资源。 大约一次每小时收集使用情况的信息。 收集后，此数据传输到 Azure 并在 Azure 商务系统处理。 此过程可能需要到几个小时。

## <a name="view-usage---csp-subscriptions"></a>查看使用情况-CSP 订阅

如果你注册使用 CSP 订阅你 Azure 堆栈，请可以与在其中查看 Azure 消耗相同的方式来查看使用情况和费用。 Azure 堆栈使用将在你的发票和对帐文件，可通过包含[合作伙伴中心](https://partnercenter.microsoft.com/partner/home)。 每月更新对帐文件。 如果你需要访问新 Azure 堆栈使用情况信息，你可以使用合作伙伴中心 Api。

   ![合作伙伴中心](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>查看使用情况 – 企业协议订阅

如果你注册使用的企业协议订阅你 Azure 堆栈，则可以查看使用情况和费用中的[EA 门户](https://ea.azure.com/)。 Azure 堆栈使用将包括在高级下载以及 EA 门户中的报表部分下的 Azure 使用情况。 

## <a name="view-usage--other-subscriptions"></a>查看使用情况 – 其他订阅

如果你注册你的 Azure 栈使用任何其他订阅类型，例如，即付订阅，你可以在 Azure 帐户中心查看使用情况和费用。 登录到[Azure 帐户中心](https://account.windowsazure.com/Subscriptions)作为 Azure 的帐户管理员联系并选择用于注册 Azure 堆栈的 Azure 订阅。 你可以查看 Azure 堆栈使用情况数据，如以下图像中所示为每个使用的资源向收取的金额：

   ![计费流](media/azure-stack-usage-reporting/pricing-details.png)

为 Azure 堆栈开发工具包，Azure 堆栈的资源不会收费以便价格显示为 0.00 美元。 当 Azure 堆栈的多节点变为正式发布时，你可以为每个这些资源中看到的实际费用。

## <a name="which-azure-stack-deployments-are-charged"></a>哪些 Azure 堆栈部署进行收费？

资源使用是免费的 Azure 堆栈开发工具包。 而对于 Azure 堆栈多节点系统中，工作负荷 Vm、 存储服务和应用程序服务会收费。

## <a name="are-users-charged-for-the-infrastructure-vms"></a>用户会对基础结构 Vm 进行收费？

不能。 某些 Azure 堆栈资源提供程序的虚拟机的使用情况数据报告给 Azure，但这些 Vm，或在部署期间创建的 Vm，若要启用 Azure 堆栈基础结构没有费用是多少。  

用户仅收取在租户订阅下运行的 Vm。 所有工作负荷必须部署在租户订阅，以符合 Azure 堆栈的许可条款。

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>我有我想要使用 Azure 堆栈上的 Windows Server 许可证，如何执行此操作？

使用现有的许可证，则可避免生成用量计量表。 现有 Windows Server 许可证可在 Azure 堆栈中的"与 Azure 堆栈中使用现有的软件"部分所述[Azure 堆栈许可指南](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409)。 客户需要将其 Windows Server 虚拟机部署中所述[的 Windows Server 许可证的混合权益](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)才能使用其现有的许可证的主题。

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>哪种订阅占用的资源收费？
时提供的订阅[向 Azure 注册 Azure 堆栈](azure-stack-register.md)需要支付费用。

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>使用情况数据报告支持哪些类型的订阅？

对于 Azure 堆栈多节点，企业协议 (EA) 和 CSP 订阅支持。 对于 Azure 堆栈开发工具包，企业协议 (EA)、 即用即付、 CSP 和 MSDN 订阅支持使用情况数据报告。

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>报告中最高的云的工作的使用情况数据？

在 Azure 堆栈开发工具包中，使用情况数据报告要求在全球 Azure 系统中创建的订阅。 无法使用 Azure，注册一个最高的云 （Azure 政府版、 Azure 德国和 Azure 中国云） 中创建的订阅，因此它们不支持使用情况数据报告。

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>用户如何标识 Azure 计费门户中的 Azure 堆栈使用情况数据？

用户可以看到的使用情况详细信息文件中的 Azure 堆栈使用情况数据。 若要了解有关如何获取的使用情况详细信息文件，请参阅[Azure 帐户中心文章中的说明下载使用量文件](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv)。 使用情况详细信息文件包含标识 Azure 堆栈存储和虚拟机的 Azure 堆栈计量。 使用 Azure 堆栈中的所有资源都报告下名为"Azure 堆栈。"的区域

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>为什么 Azure 堆栈中报告的用法不匹配从 Azure 帐户中心生成报表？

始终是通过 Azure 堆栈使用情况 Api 和报告的 Azure 帐户中心使用情况数据报告使用情况数据 delaybetween... 此延迟是上载到 Azure 商务的使用情况数据从 Azure 堆栈所需的时间。 由于这种延迟，很快在午夜之前发生的使用情况可能会显示在 Azure 中下一天。 如果你使用[Azure 堆栈使用情况 Api](azure-stack-provider-resource-api.md)，并比较 Azure 计费门户中报告的使用情况的结果，您可以看到不同。

## <a name="next-steps"></a>后续步骤

* [提供者使用情况 API](azure-stack-provider-resource-api.md)  
* [租户使用情况 API](azure-stack-tenant-resource-usage-api.md)
* [使用情况常见问题](azure-stack-usage-related-faq.md)
