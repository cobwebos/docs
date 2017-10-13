---
title: "测试应用商店的数据服务产品/服务 | Microsoft Docs"
description: "了解如何测试 Azure 应用商店的数据服务产品/服务。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e861bd11-f74d-4d77-b4b5-23fb463644ad
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 56a8aad7484fed18b74200ffa7acf22363625a15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="testing-your-data-service-offer-in-staging"></a>在过渡环境中测试数据服务产品/服务
> [!IMPORTANT]
> **本次我们不再载入任何新的数据服务发布服务器。新的 dataservices 将不会获准出现在列表中。** 如果想要在 AppSource 上发布 SaaS 业务应用程序，可以在[此处](https://appsource.microsoft.com/partners)找到更多信息。 如果想要在 Azure 应用商店上发布 IaaS 应用程序或开发人员服务，可以在[此处](https://azure.microsoft.com/marketplace/programs/certified/)找到更多信息。
> 
> 

完成[创建 Microsoft 开发人员帐户](marketplace-publishing-accounts-creation-registration.md)和[在发布门户中创建数据服务产品/服务](marketplace-publishing-data-service-creation.md)的前两个步骤后，即可在 Azure 应用商店中提供产品/服务。 本主题指导完成名为“过渡”的第一个中间步骤

过渡表示将产品/服务部署到专用“沙盒”，可先在其中进行测试，再发布到生产。 产品/服务将出现在过渡环境中，就像客户已对它进行部署一样。

## <a name="step-1-pushing-your-offer-to-staging"></a>步骤 1。 将产品/服务推送到过渡环境
通过将产品/服务推送到过渡环境，可以先测试产品/服务，然后再将其提供给未来的订阅方。  可以查看产品/服务将如何面向订阅数据的用户显示和运行。  

  ![绘制](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1. 登录到[发布门户](https://publish.windowsazure.com)
2. 在左侧导航窗口中选择“数据服务”
3. 选择要推送到过渡环境的产品/服务。 会看到上述屏幕。
4. 单击“推送到过渡环境”按钮。  
5. 如果在推送到过渡环境前需要完成的产品/服务有问题，会看到显示的列表。  通过单击列表中每个项，更正这些项。 进行所有更正后，再次单击“推送到过渡环境”按钮。

如果产品/服务没有任何问题，会看到下面的弹出窗口。  

如果未计划/未获准在 Azure 门户中展示产品/服务（当前容量有限），只需关闭弹出窗口。

要在 Azure 门户中（除 DataMarket 门户外）测试数据服务，将需要 Azure 订阅 ID 来进行测试。  此订阅 ID 将标识有权测试产品/服务的帐户。  

剪切并粘贴订阅 ID，单击复选标记以继续。

  ![绘制](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [!NOTE]
> 仅在 [Azure 管理门户](https://manage.windowsazure.com)中进行测试和暂存时，才需要这些 Azure 订阅 ID。 不需要它们在 Azure 应用商店中进行测试。
> 
> 

接下来出现的屏幕会显示正在进行发布，即在下面显示以黄色突出显示的“正在进行”图标。 推送到过渡环境需要 10 到 15 分钟。  如果需要更长的时间，请先刷新浏览器（在 IE 中按 F5）。  在极少数情况下，一小时过后产品/服务仍推送到过渡环境，此时单击可联系我们的链接，以便让我们知道存在问题。

  ![绘制](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

当推送到过渡环境完成时，“正在进行”图标将停止移动，并且状态将更新为“已暂存”。  现在，可以测试产品/服务。  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>步骤 2. 在 DataMarket 中测试暂存的产品/服务
单击文本**“看到服务产品/服务位于…”**后面的链接 以显示屏幕，它会在产品/服务投入生产时显示给订阅方并且会在 DataMarket 中显示。

  ![绘制](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

测试或验证以上标记的所有 12 个项，以确保所有徽标、价格/交易、文本、图像、文档和链接均正确并且能正常运行。  这是确保在创建产品/服务时输入的任何测试值都已替换为实际值的好时机。

1. 产品/服务徽标
2. 产品名称
3. 发布者名称/指向公司网站的链接
4. 产品/服务的搜索类别
5. 产品/服务的支持链接，用于帮助订阅方
6. 产品/服务的上下文描述
7. 描述计费详细信息的产品/服务计划
8. 指向实现代码的链接
9. 说明产品/服务数据用法的示例图像
10. 产品/服务内每个服务的输入/输出元数据
11. 产品/服务使用条款
12. 产品/服务的数据预览

最后，通过单击链接“浏览此数据集”，检查服务将通过 Datamarket。  新窗口会在工具中（我们称它为“服务资源管理器”）打开，以便可以根据服务预览查询的结果。  在此窗口中，可以输入所需的参数，并查看根据服务查询所显示的结果。   此外，显示的内容将是查询的 URL。  

> [!NOTE]
> 请务必查看显示在顶部的服务的文本说明。  如果服务包含多个服务调用，请单击底部选项卡，以切换到要查看和测试的下一服务。
> 
> 

## <a name="next-step"></a>后续步骤
如果遇到问题，并且需要获得解决问题方面的帮助，请联系 [Azure 发布者支持](http://go.microsoft.com/fwlink/?LinkId=272975)。

如果符合条件并且可随时发布产品/服务，请阅读[请求批准推送到生产](marketplace-publishing-push-to-production.md)文档。

## <a name="see-also"></a>另请参阅
* [入门教程：如何将供应项发布到 Azure 应用商店](marketplace-publishing-getting-started.md)

