---
title: 为 Marketplace 创建解决方案模板指南 | Microsoft Docs
description: 有关如何创建、验证和部署在 Azure Marketplace 上提供购买的多 VM 映像解决方案模板的详细说明。
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: mbaldwin
ms.openlocfilehash: 83e69701aa5e8c11354e7551d66f3fdd18289000
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>为 Azure Marketplace 创建解决方案模板指南
完成步骤 1 [帐户创建和注册][link-acct-creation]后，我们已通过[创建解决方案模板的技术先决条件](marketplace-publishing-solution-template-creation-prerequisites.md)指导创建 Azure 兼容的解决方案模板。 现在，我们指导完成在 Azure Marketplace 的[发布门户][link-pubportal]上为多个 VM 创建解决方案模板的步骤。

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>在发布门户中创建解决方案模板产品/服务
转到 [https://publish.windowsazure.com](http://publish.windowsazure.com)。首次登录到[发布门户](https://publish.windowsazure.com/)时，使用的帐户应与注册公司卖方配置文件时所使用的帐户相同。 稍后，可以添加公司的任何员工作为发布门户中的共同管理员。

### <a name="1-select-solution-templates"></a>1.选择“解决方案模板”
  ![绘制][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2.创建新的解决方案模板
  ![绘制][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3.从拓扑开始
解决方案模板是其所有拓扑的“父级”。 可以在一个产品/解决方案模板中定义多个拓扑。 将产品推送到过渡环境时，它会随其所有拓扑一起推送。 按照以下步骤定义产品/服务：     

* 创建拓扑：“拓扑标识符”通常是解决方案模板的拓扑名称。 拓扑标识符会在 URL 中使用，如下所示：

  Azure Marketplace：http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Azure 门户：https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* 添加新版本。

### <a name="4-get-your-topology-versions-certified"></a>4.认证拓扑版本
上传包含所有必需文件的 zip 文件，以便预配该特定版本的拓扑。 此 zip 文件包含以下内容：

* 位于其根目录中的 *mainTemplate.json* 和 *createUiDefinition.json* 文件。
* 任何链接的模板和所有必需的脚本。

  > [!TIP]
  > 当开发人员正在创建解决方案模板拓扑并使其获得认证时，公司的业务、市场营销和/或法律部门可以处理市场营销和法律内容。
  >
  >

## <a name="next-steps"></a>后续步骤
既然已创建解决方案模板并且已上传 zip 文件，在将产品/服务推送到过渡环境前，请遵循 [Marketplace 市场营销内容指南](marketplace-publishing-push-to-staging.md)中的说明。 要查看完整的商城发布文章集，请访问[入门：如何将产品/服务发布到 Azure Marketplace](marketplace-publishing-getting-started.md)。

也有可能对下列相关文章感兴趣：

* VM 映像：[关于 Azure 中的虚拟机映像](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* VM 扩展：[ VM 代理和 VM 扩展概述](https://msdn.microsoft.com/library/azure/dn832621.aspx)和 [Azure VM 扩展和功能](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure 资源管理器：[创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)和[简单的模板示例](https://github.com/rjmax/ArmExamples)
* 存储帐户限制：[如何监视存储帐户限制](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx)和[高级存储](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
