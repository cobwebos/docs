---
title: "关于如何创建产品/服务并将其发布到应用商店的概述 | Microsoft Docs"
description: "了解在 Azure 应用商店中成为经批准的 Microsoft 开发人员，以及创建和部署虚拟机映像、模板、数据服务或开发人员服务所需的步骤"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: aed47a7e9aa06e48199a71f67ae6d82bcfe27c9a


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>如何在 Azure 应用商店中发布和管理产品/服务
本文旨在帮助开发人员创建、部署和管理在 Azure 应用商店中列出的解决方案，以便其他 Azure 客户和合作伙伴可进行购买和利用。

## <a name="what-is-the-azure-marketplace"></a>什么是 Azure 应用商店？
在 Azure 应用商店中，Azure 订阅方可以找到相关服务，以促进本地或基于云的解决方案和应用程序的开发。 使用 [Azure 认证的](http://azure.com/certified)服务作为构建基块，可以面向业务范围和其他 Azure 订阅方快速开发创新的应用程序或服务。

作为 Azure 发布者，Azure 应用商店允许你面向希望快速开发基于云的应用程序和移动解决方案的其他开发人员、ISV 和 IT 专业人员，分发和销售创新的解决方案或服务。

## <a name="supported-types-of-offers"></a>支持的产品/服务类型
作为一名发布者，首先要做的是定义公司将提供哪种类型的解决方案。 Azure 应用商店支持三种类型的产品/服务：

* **虚拟机映像**是预配置的映像，它们具有一个完整安装的操作系统以及一个或多个应用程序。 虚拟机映像提供在 Azure 虚拟机服务中创建和部署虚拟机所需的信息。

  > [!NOTE]
  > **例如，**作为 Azure 发布者，你已创建并验证了具有创新数据库服务的 VM，使其极具吸引力，从而让其他 Azure 订阅方都愿意采购此 VM 并将其部署到云服务环境中。
  >
  >
* **开发人员服务**是用于应用程序开发或系统管理的完全托管的服务。 它们提供的功能可在 Azure 上快速开发云规模的应用程序。

  > [!NOTE]
  > **例如，**作为 Azure 发布者，你已开发 API 可访问的服务（托管在 Azure 或其他位置），该服务提供基于历史数据的预测。 而且这是一项服务，其他生成解决方案的 Azure 订阅方可能需要利用它。 可以将此服务部署到 Azure 应用商店，以便其他用户查找、采购并在其各自的服务中使用。
  >
  >
* **解决方案模板**是可以引用一个或多个不同的 Azure 服务的数据结构（包括由其他卖方发布的服务），允许 Azure 订阅方以单一协同方式部署一个或多个产品/服务。

  > [!NOTE]
  > **例如，**作为 Azure 发布者，你已跨 Azure 捆绑服务集，只需几次单击即可快速部署安全、高可用性且负载平衡的云服务。 其他 Azure 订阅方可以通过采购此解决方案模板（而不是手动标识和配置相同或类似的 Azure 服务），从而在省时方面获益。
  >
  >

某些步骤在不同的解决方案类型中是通用的。 本文简要概述了针对任何解决方案类型需要完成的步骤。

## <a name="1-pre-requisites"></a>1.先决条件
> [!NOTE]
> 在开始在 Azure 应用商店上进行任何工作前，必须通过[预批准](http://azure.com/certified)。
>
>

1. [申请 Microsoft Azure 认证的预批准](marketplace-publishing-azure-certification.md)
2. [创建并注册 Microsoft 开发人员帐户](marketplace-publishing-accounts-creation-registration.md)
3. [完成非技术先决条件](marketplace-publishing-pre-requisites.md)

## <a name="2-publishing-your-offer"></a>2.发布产品/服务
### <a name="21-complete-offer-specific-technical-pre-requisites"></a>2.1 完成产品/服务特定的技术先决条件
* [VM 技术先决条件](marketplace-publishing-vm-image-creation-prerequisites.md)
* [解决方案模板技术先决条件](marketplace-publishing-solution-template-creation-prerequisites.md)

### <a name="22-create-your-offer"></a>2.2 创建产品/服务
1. 使用以下产品/服务特定的指南创建产品/服务。
   * [创建 VM 产品/服务](marketplace-publishing-vm-image-creation.md)
   * [创建解决方案模板产品/服务](marketplace-publishing-solution-template-creation.md)
2. [创建产品/服务市场营销内容](marketplace-publishing-push-to-staging.md)

### <a name="23-test-your-offer-in-staging"></a>2.3 在过渡环境中测试产品/服务
* [在过渡环境中测试 VM 产品/服务](marketplace-publishing-vm-image-test-in-staging.md)
* [在过渡环境中测试解决方案模板产品/服务](marketplace-publishing-solution-template-test-in-staging.md)

### <a name="24-deploy-your-offer-to-the-marketplace"></a>2.4 将产品/服务部署到应用商店
* [将产品/服务部署到 Azure 应用商店](marketplace-publishing-push-to-production.md)
* [应用商店中的常见发布问题疑难解答](marketplace-publishing-support-common-issues.md)
* 若要了解有关所使用门户的详细信息，请参阅[所需的门户](marketplace-publishing-portals.md)。

### <a name="virtual-machine-image-specific"></a>虚拟机映像专用
* [在本地创建 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)
* [在 Azure 门户中创建运行 Windows 的虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [在 Azure 门户中创建运行 Linux 的虚拟机](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [对 VHD 创建期间遇到的常见问题进行故障排除](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="3-post-publishing-management-of-your-offer"></a>3.产品/服务的发布后管理
* [虚拟机产品/服务后期制作指南](marketplace-publishing-vm-image-post-publishing.md)
* [如何更新产品/服务或 SKU 的非技术详细信息](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [如何更新产品/服务或 SKU 的技术详细信息](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [如何在已列出的产品/服务下添加新 SKU](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [如何更改已列出 SKU 的数据磁盘计数](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [如何从 Azure 应用商店中删除已列出的产品/服务](marketplace-publishing-vm-image-post-publishing.md)
* [如何从 Azure 应用商店中删除已列出的 SKU](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [如何从 Azure 应用商店中删除已列出的 SKU 的当前版本](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [如何将定价还原为生产值](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [如何将计费模型还原为生产值](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [如何将列出的 SKU 的可见性设置还原为生产值](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [如何更改云解决方案提供商分销商激励](marketplace-publishing-csp-incentive.md)
* [了解卖方 Insight 报表](marketplace-publishing-report-seller-insights.md)
* [了解付款报表](marketplace-publishing-report-payout.md)
* [作为发布者获取支持](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>其他资源
* [设置 Azure PowerShell](marketplace-publishing-powershell-setup.md)



<!--HONumber=Nov16_HO3-->


