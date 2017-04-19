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
ms.date: 01/05/2017
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 9d7176b018d25b2a60b2175c289dde7490f8d101
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>如何在 Azure 应用商店中发布和管理产品/服务
本文旨在帮助开发人员创建、部署和管理在 Azure 应用商店中列出的解决方案，以便其他 Azure 客户和合作伙伴可进行购买和利用。

## <a name="what-is-the-azure-marketplace"></a>什么是 Azure 应用商店？
作为 Azure 发布者，Azure 应用商店允许你面向希望快速开发基于云的应用程序和移动解决方案的其他开发人员、ISV 和 IT 专业人员，分发和销售创新的解决方案或服务。 如果解决方案面向业务用户，则可能需要考虑 [AppSource](http://appsource.microsoft.com) 应用商店。


## <a name="supported-types-of-solutions"></a>支持的解决方案类型
作为一名发布者，首先要做的是定义公司将提供哪种类型的解决方案。 Azure 应用商店支持以下类型的产品/服务：

|解决方案类型|虚拟机|解决方案模板|
|---|---|---|
|定义|预配置的映像，其具有一个完整安装的操作系统以及一个或多个应用程序。 虚拟机映像提供在 Azure 虚拟机服务中创建和部署虚拟机所需的信息。|可以引用一个或多个不同的 Azure 服务的数据结构（包括由其他卖方发布的服务），允许 Azure 订阅方以单一协同方式部署一个或多个产品/服务。|
|示例|**例如，**作为 Azure 发布者，你已创建并验证了具有创新数据库服务的 VM，使其极具吸引力，从而让其他 Azure 订阅方都愿意采购此 VM 并将其部署到云服务环境中。|**例如，**作为 Azure 发布者，你已跨 Azure 捆绑服务集，从而可快速部署负载均衡的云服务、增强安全性和高可用性。 其他 Azure 订户可以通过采购满足其目标的解决方案模板来节省时间，而无需手动查找、采购、部署和配置相同或相似的 Azure 服务。|

> [!NOTE]
> 请注意一些步骤将在不同类型的解决方案之间共享，而另一些步骤则因解决方案类型而异。 本文简要概述了针对任何解决方案类型需要完成的步骤。

## <a name="how-to-publish-a-solution"></a>如何发布解决方案
![绘制](media/marketplace-publishing-getting-started/img01.png)

### <a name="1-nominate-your-solution-for-pre-approval"></a>1.提名解决方案以便预批准
- 请在[此处](https://createopportunity.azurewebsites.net)完成**经过 Microsoft Azure 认证的虚拟机**的解决方案提名表单

>[!NOTE]
> 如果你正在与合作伙伴客户经理或 DX 合作伙伴经理协作，请让他们对 Azure 认证计划提名你的解决方案，或转到 [Microsoft Azure 认证](http://createopportunity.azurewebsites.net)网页上，填写应用程序表单，并在 Microsoft 发起人联系方式字段中输入你的合作伙伴客户经理或 DX 合作伙伴经理的电子邮件。

根据 [Azure 应用商店参与策略](http://go.microsoft.com/fwlink/?LinkID=526833)，如果符合资格条件，并且你的应用程序获得了批准，我们将开始协助你将解决方案发布到 Azure 应用商店。

### <a name="2-register-your-account-as-a-microsoft-seller"></a>2.将你的帐户注册为 Microsoft 卖方
- [将你的 Microsoft 帐户注册为 Microsoft 开发人员帐户](marketplace-publishing-accounts-creation-registration.md)

### <a name="3-publish-your-solution"></a>3.发布解决方案
1. 满足非技术要求
  - [完成非技术先决条件](marketplace-publishing-pre-requisites.md)
  - [VM 技术先决条件](marketplace-publishing-vm-image-creation-prerequisites.md)
  - [解决方案模板技术先决条件](marketplace-publishing-solution-template-creation-prerequisites.md)
2. 创建产品/服务
  - [虚拟机](marketplace-publishing-vm-image-creation.md)
  - [解决方案模板](marketplace-publishing-solution-template-creation.md)
3. [创建产品/服务市场营销内容](marketplace-publishing-push-to-staging.md)
4. 在过渡环境中测试产品/服务
  - [在过渡环境中测试 VM 产品/服务](marketplace-publishing-vm-image-test-in-staging.md)
  - [在过渡环境中测试解决方案模板产品/服务](marketplace-publishing-solution-template-test-in-staging.md)
5. [将产品/服务部署到 Azure 应用商店](marketplace-publishing-push-to-production.md)


### <a name="virtual-machine-image-specific"></a>虚拟机映像专用
* [在本地创建 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)
* [在 Azure 门户中创建运行 Windows 的虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [在 Azure 门户中创建运行 Linux 的虚拟机](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [对 VHD 创建期间遇到的常见问题进行故障排除](marketplace-publishing-vm-image-creation-troubleshooting.md)

## <a name="how-to-manage-your-solution"></a>如何管理解决方案
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

