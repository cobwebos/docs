---
title: 如何测试 SaaS 产品并将其发布到 Microsoft 商业应用商店
description: 使用合作伙伴中心提交你的 SaaS 产品/服务进行预览、预览你的产品/服务、测试，然后将其发布到 Microsoft 商用 marketplace。
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: bef59ea9e7de77e7f9a80cc3950762ea70238b87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380662"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>如何测试 SaaS 产品并将其发布到商业应用商店

本文介绍如何使用合作伙伴中心提交你的 SaaS 产品/服务进行发布、预览你的产品并进行测试，然后将其发布到商业市场。 你必须已创建想要发布的产品/服务。

## <a name="submit-your-offer-for-publishing"></a>提交产品/服务进行发布

1. 登录到 [合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)的 "商用 marketplace" 仪表板。
1. 在 " **概述** " 页上，选择要发布的产品/服务。
1. 在门户的右上角，选择 " **查看并发布**"。
2. 请确保每个页面的 " **状态** " 列显示 " **完成**"。 三种可能的状态如下所示：

   - **未启动** –页面不完整。
   - **不完整** –该页缺少必需的信息或有需要修复的错误。 需要返回到页面并对其进行更新。
   - **完成** –该页已完成。 提供所有所需的数据，并且没有错误。

1. 如果任何页面的状态不是 " **完成**"，请选择页面名称，纠正问题，保存页面，然后选择 " **检查并再次发布** " 以返回到此页面。
1. 完成所有页面后，请在 " **认证说明** " 框中，向认证团队提供测试说明，以确保正确测试应用。 提供任何有助于了解应用的补充说明。
1. 若要启动产品/服务的发布过程，请选择 " **发布**"。 " **产品概述** " 页将出现，并显示产品/服务的 **发布状态**。

在发布过程中，产品/服务的发布状态将发生变化。 有关此过程的详细信息，请参阅 [验证和发布步骤](review-publish-offer.md#validation-and-publishing-steps)。

## <a name="preview-and-test-your-offer"></a>预览和测试产品/服务

如果产品/服务已准备就绪，则会向你发送一封电子邮件，请求你查看和批准产品/服务预览版。 您还可以在浏览器中刷新 " **产品/服务概述** " 页，以查看您的产品/服务是否已到达发布者签署阶段。 如果有，则会提供 **"上线" 按钮和** 预览链接。 根据你在创建产品/服务时选择的选项，Microsoft AppSource 预览、Azure Marketplace 预览或两者都有链接。 如果你选择通过 Microsoft 销售您的产品/服务，则已添加到预览版观众的任何人都可以测试您的产品/服务的购买和部署，以确保在此阶段满足您的要求。

以下屏幕截图显示了 SaaS 产品的 **产品/服务概述** 页，其中有两个预览链接，位于 " **上线** " 按钮下。 你将在此页上看到的验证步骤因你创建产品/服务时所做的选择而异。

![说明合作伙伴中心提供的产品/服务概述页。 显示 "上线" 按钮和预览链接。](media/publish-status-publisher-signoff.png)

使用以下步骤来预览你的产品/服务。

1. 在 " **产品/服务概述** " 页上，选择 " **开始** 使用" 按钮下的 "预览" 链接。 

1. 若要验证端到端购买和设置流程，请在产品/服务预览版中购买产品/服务。 首先，向 Microsoft 通知一个 [支持票证](https://aka.ms/marketplacesupport) ，以确保不会处理费用。

1. 如果你 [的 SaaS 服务支持使用商业 marketplace 计量服务按流量](./partner-center-portal/saas-metered-billing.md)计费，请查看并遵循 [marketplace 计费 api](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)中所述的测试最佳实践。

1. 查看并按照 [Microsoft 商业应用商店中的 SaaS 履单 api 版本2中](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) 的测试说明进行操作，确保产品/服务已成功与 api 集成，然后再发布产品/服务。

1. 如果需要在预览和测试产品/服务后进行更改，可以编辑和重新提交以发布新预览。 有关详细信息，请参阅 [在商业应用商店中更新现有产品/服务](./partner-center-portal/update-existing-offer.md)。

## <a name="publish-your-offer-live"></a>实时发布产品/服务

完成预览版中的所有测试后，选择 "上线" **，将产品** /服务实时发布到商业市场。

   > [!TIP]
   > 如果产品/服务已在商业应用商店中，则 **在选择 "上线"** 之前，你所做的任何更新都不会生效。

现在，你已选择在商业应用商店中提供产品/服务，我们将执行一系列的最终验证检查，以确保实时产品/服务的配置与预览版的预览版本一样。 有关这些验证检查的详细信息，请参阅 [发布阶段](review-publish-offer.md#publish-phase)。

完成这些验证检查后，你的产品/服务将在 marketplace 中推出。

## <a name="next-step"></a>后续步骤

[访问合作伙伴中心的商业 marketplace 分析报表](./partner-center-portal/analytics.md)