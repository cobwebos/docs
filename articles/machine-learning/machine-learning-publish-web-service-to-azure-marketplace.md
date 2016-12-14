---
title: "将机器学习 Web 服务发布到 Azure 应用商店 | Microsoft Docs"
description: "如何将 Azure 机器学习 Web 服务发布到 Azure 应用商店"
services: machine-learning
documentationcenter: 
author: BharathS
manager: jhubbard
editor: cgronlun
ms.assetid: 68e908be-3a99-4cd7-9517-e2b5f2f341b8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: bharaths
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 24b131d3a9a17ce9a2daf349518873871c9d1019


---
# <a name="publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>将 Azure 机器学习 Web 服务发布到 Azure 应用商店
Azure 应用商店提供将 Azure 机器学习 Web 服务发布为付费或免费服务的能力，以供外部客户使用。 本文概述了该过程以及指向入门指南的链接。 使用此过程，可让 Web 服务供其他开发人员在其应用程序中使用。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>发布过程概述
下面是将 Azure 机器学习 Web 服务发布到 Azure 应用商店的步骤：

1. 创建并发布机器学习请求-响应服务 (RRS)
2. 将服务部署到生产，并获取 API 密钥和 OData 终结点信息。
3. 使用已发布 Web 服务的 URL 来发布到 [Azure 应用商店（数据市场）](https://publish.windowsazure.com/workspace/) 
4. 提交后，产品/服务将进行审查并且需要得到批准，然后客户才能开始购买它。 发布过程可能需要几个工作日。 

## <a name="walk-through"></a>演练
### <a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>步骤 1：创建并发布机器学习请求-响应服务 (RRS)
 如果尚未完成此操作，请查看此[演练](machine-learning-walkthrough-5-publish-web-service.md)。

### <a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>步骤 2：将服务部署到生产并获取 API 密钥和 OData 终结点信息
1. 通过 [Azure 经典门户](http://manage.windowsazure.com)，从左侧导航栏中选择“机器学习”选项，然后选择工作区。 
2. 单击“Web 服务”选项卡，然后选择要发布到应用商店的 Web 服务。
   
    ![Azure 应用商店][workspace]
3. 选择希望应用商店使用的终结点。 如果尚未创建任何其他终结点，可以选择“默认”终结点。
4. 单击终结点后，你将能够看到“API 密钥”。 稍后在步骤 3 中将需要此段信息，因此请复制它。
   
    ![Azure 应用商店][apikey]
5. 单击“请求/响应”方法，此时我们不支持将批处理执行服务发布到应用商店。 这将转到请求/响应方法的 API 帮助页。
6. 复制“OData 终结点地址”，稍后将需要在步骤 3 中使用此信息。
   
    ![Azure 应用商店][odata]

将服务部署到生产。

### <a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>步骤 3：使用已发布 Web 服务的 URL 来发布到 Azure 应用商店 (DataMarket)
1. 导航到 [Azure 应用商店 (Data Market)](http://datamarket.azure.com/home) 
2. 单击页面顶部的“发布”链接。 这将转到 [Microsoft Azure 发布门户](https://publish.windowsazure.com)
3. 单击“发布者”部分，以注册为发布者。
4. 创建新产品/服务时，选择“数据服务”，然后单击“创建新数据服务”。 
   
   ![Azure 应用商店][image1]
   
   <br />
5. 在“计划”下，提供有关产品/服务的信息，包括定价计划。 决定是否将提供免费或付费服务。 若要实现付费，请提供付款信息，例如你的银行和税务信息。
6. 在“市场营销”下，提供产品/服务的相关信息，例如产品/服务的标题和描述。
7. 在“定价”下，可以针对特定的国家/地区为计划设置价格，也可以让系统“自动定价”产品/服务。
8. 在“数据服务”选项卡上，单击“Web 服务”作为“数据源”。
   
    ![Azure 应用商店][image2]
9. 从 Azure 经典门户获取 Web 服务 URL 和 API 密钥，如上面的步骤 2 所述。
10. 在“应用商店数据服务设置”对话框中，将 OData 终结点地址粘贴到“服务 URL”文本框中。
11. 对于“身份验证”，选择“标头”作为“身份验证方案”。
    
    * 为“标头名称”输入 "Authorization"。
    * 为“标头值”输入 "Bearer"（不含引号），单击**空格**键，然后粘贴 API 密钥。
    * 选中“此服务是 OData”复选框。
    * 单击“测试连接”以测试连接。
12. 在“类别”下，确保已选中“机器学习”。
13. 在输入完产品/服务相关的所有元数据后，依次单击“发布”和“推送到过渡”。 此时，你将收到有关需要修复的任何剩余问题的通知。
14. 确保已完成所有未完成的问题后，单击“请求批准推送到生产”。 发布过程可能需要几个工作日。 

[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[工作区]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png




<!--HONumber=Nov16_HO3-->


