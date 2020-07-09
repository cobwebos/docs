---
title: 什么是体验版？ Microsoft 商业市场
description: Marketplace 测试驱动器功能的说明
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: 95ca527d8f7912e2c455f94036fe61828b220257
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121463"
---
# <a name="what-is-a-test-drive"></a>什么是体验版？

测试驱动器是向潜在客户展示您的产品/服务的一种好方法，通过向客户提供产品/服务，使其能够在*购买之前进行试用*，从而增加了转换和生成高度合格的潜在客户。 在实际实施方案中，测试驱动器将使你的产品进入生活，同时还会生成高合格的潜在顾客。

测试驱动器是按需为请求客户端的客户部署解决方案或应用程序的托管实例。 分配一个测试驱动器实例后，可将其用于一定的时间，然后将其删除以便为另一个客户腾出空间。

作为发布者，你可以在合作伙伴中心管理和配置测试驱动器设置。 技术配置详细信息因所使用的产品/服务类型而异。 有关详细指南，请参阅本主题末尾的[下一步](#next-step)下的链接。

潜在客户在商业市场上发现你的测试驱动器。 它们提供了其联系信息并同意你的产品/服务的条款和隐私策略，然后在一段固定的时间内访问预配置的环境以试用该服务。 客户可获得对你的产品关键功能和权益的动手、自行指导的试用版，你将获得宝贵的潜在顾客。

## <a name="how-does-it-work"></a>工作原理

作为发布者，你可以在合作伙伴中心内管理和配置测试驱动器设置。 安装完成后，它将成为托管实例，该实例将按需为请求它的客户部署。 分配一个测试驱动器实例后，可将其用于一定的时间，然后将其删除以便为另一个客户腾出空间。

## <a name="types-of-test-drives"></a>测试驱动器的类型

选择产品/服务的商用 marketplace 提供有不同的测试驱动器，具体取决于所使用的产品、方案和 marketplace 类型：

- Azure 资源管理器
- 托管的测试驱动器
    - Dynamics 365 for Business Central
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- 逻辑应用
- Power BI

有关配置其中一个测试驱动器的详细信息，请参阅本主题末尾的[下一步](#next-step)下的链接。

### <a name="azure-resource-manager-test-drive"></a>Azure 资源管理器测试驱动器

此部署模板包含构成解决方案的所有 Azure 资源。 适合此方案的产品仅使用 Azure 资源。 这是虚拟机或 Azure 应用产品/服务的唯一测试驱动器选项。

### <a name="hosted-test-drive"></a>托管的测试驱动器

托管的测试驱动器通过让 Microsoft 主机和维护执行测试驱动器用户预配、部署和取消设置的服务，消除了安装的复杂性。 如果有 Microsoft AppSource 上的产品/服务，请构建你的测试驱动器，以连接到除 Azure 之外的 Dynamics AX/CRM 实例或其他任何资源。 此类型适用于 AppSource 产品/服务，可与以下 Dynamics 365 产品/服务进行连接：

- 使用[Dynamics 365 进行](partner-center-portal/create-new-operations-offer.md)业务中心企业资源规划系统，如财务、运营、供应链和 CRM。
- 使用[Dynamics 365 为](partner-center-portal/create-new-customer-engagement-offer.md)客户参与系统（如销售、服务、项目服务和现场服务）提供客户参与。
- 使用[Dynamics 365 进行](partner-center-portal/create-new-operations-offer.md)财务和运营企业资源规划系统（如财务、运营和制造、供应链）的操作。

### <a name="logic-app-test-drive"></a>逻辑应用测试驱动器

此类型的测试驱动器不由 Microsoft 托管。 使用它连接 Dynamics 365 产品/服务或其他自定义资源。

### <a name="power-bi-test-drive"></a>Power BI 体验版

这只是一个指向自定义生成的仪表板的嵌入链接。 仅演示交互 Power BI 视觉对象的任何产品都应使用这种类型的测试驱动器。

## <a name="transforming-examples"></a>转换示例

将资源体系结构转换为测试驱动器的过程可能会很困难。 请查看这些有关如何最好地转换当前体系结构的示例。

[将网站模板转换为测试驱动器](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[将虚拟机模板转换为测试驱动器](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[将现有资源管理器模板转换为测试驱动器](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>从测试驱动器生成潜在顾客

商业市场测试驱动器是营销人员的极佳工具。 建议在启动时将其合并到市场推广工作中，以便为业务生成更多的潜在顾客。 有关详细指南，请参阅[商业 marketplace 产品/服务的客户领导](https://github.com/partner-center-portal/commercial-marketplace-get-customer-leads.md)。

如果你结束了对测试驱动器的潜在客户的交易，请确保在[Microsoft 合作伙伴销售连接](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect)上注册它。 此外，我们还希望知道你的客户入选，其中的测试驱动器扮演了某个角色。

## <a name="other-resources"></a>其他资源

其他体验版资源：

- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF；确保弹出窗口阻止程序处于禁用状态）

## <a name="next-step"></a>后续步骤

- [体验版技术配置](test-drive-technical-configuration.md)
