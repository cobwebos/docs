---
title: 将有限的试用版项目移到 Azure
titlesuffix: Azure Cognitive Services
description: 了解如何将限时试用项目移到 Azure。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: a9f49af54f391b159f8b3d626fffc36635f5e51f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821296"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>如何将你的限时试用项目移到 Azure 中使用 CustomVision.ai 站点


因为自定义影像服务现在正在[Azure 预览版](https://azure.microsoft.com/services/preview/)，结束对 Azure 外部的限时试用项目的支持。 本文档将指导您学习如何使用[自定义视觉网站](https://customvision.ai)移动限时试用项目要与 Azure 资源相关联。 

> [!NOTE]
> 当您将自定义视觉项目移动到 Azure 资源，它们继承基础[权限]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)该 Azure 资源。 如果你的组织中的其他用户是你的项目是在 Azure 资源的所有者，他们将能够访问你的项目上[自定义视觉网站](https://customvision.ai)。 同样，删除你的资源将删除你的项目。  


有关订阅和资源的 Azure 概念的简介，请参阅[Azure 开发人员指南。](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>必备组件

你将需要有效的 Azure 订阅与同一个 Microsoft 帐户或用于登录到 Azure Active Directory (AAD) 帐户相关联[自定义视觉网站](https://customvision.ai)。 

如果还没有 Azure 帐户，[创建一个帐户](https://azure.microsoft.com/free/)免费。


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>在 Azure 门户中创建自定义视觉资源
若要与 Azure 结合使用自定义影像服务，你将需要创建自定义视觉训练和预测中的资源[Azure 门户](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)。 

 若要将使用此项目移[自定义视觉网站](https://customvision.ai)体验，必须创建你的资源在美国中南部区域中，因为所有的限时试用项目托管在美国中南部。 

多个项目可以对单个资源相关联。 有关更多详细信息[定价和限制](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas)可用。 若要继续免费使用自定义影像服务，可以在 Azure 门户中选择 F0 层。 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>将有限的试用版项目移到 Azure 资源

1.  在 web 浏览器中，导航到[自定义视觉网站](https://customvision.ai)，然后选择__登录__。 打开你想要迁移到 Azure 帐户的项目。 
2.  通过单击屏幕顶部右下角的齿轮图标打开设置页为您的项目。 

    ![项目设置是在项目页面的右上方的齿轮图标。](./media/move-your-project-to-azure/settings-icon.png)


3. 单击__移到 Azure__。

    ![转到 Azure 按钮位于左下角的项目设置页上。](./media/move-your-project-to-azure/move-to-azure.jpg)


4. 从下拉列表中上__移到 Azure__按钮，选择你想要移动到你的项目的 Azure 资源。 单击__移动__。 

5. 如果没有看到你以前为自定义影像服务创建的 Azure 资源，则它可能在另一个目录。 若要将项目移到另一个目录中的资源，请执行下面的说明。 

    ![项目的迁移时窗。](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-project-to-another-azure-directory"></a>将项目移动到另一个 Azure 目录 

> [!NOTE]
> 在 Azure 门户和 CustomVision.ai 上，你可以从屏幕的右上角的下拉列表用户菜单选择你的目录。   


1. 标识 Azure 资源位于哪个目录。 您可以找到列出的 Azure 门户菜单栏右上用户名下的目录。 

    ![你的目录下列出你在 Azure 门户菜单栏的右上方的用户名。 .](./media/move-your-project-to-azure/identify_directory.jpg)

2. 找到自定义视觉训练资源的资源 ID。 您可以在 Azure 门户中找到此通过打开自定义视觉训练资源并在"资源管理"部分下选择"属性"。 您的资源 ID 会在那里。 

    ![通过打开自定义视觉训练资源并在"资源管理"部分下选择"属性"在 Azure 门户中找到您的资源 ID。](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. 或者，可以直接在自定义视觉网站中找到你的自定义视觉资源的资源 ID[设置页]( https://www.customvision.ai/projects#/settings)。 您需要切换到 Azure 资源位于同一个目录。

    ![您的资源 ID 列出每个资源上在自定义视觉网站上的设置页。](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. 现在，你已有资源 ID，返回到要移到 Azure 资源的限时试用自定义视觉项目。 请注意，您可能需要切换回原始目录找到它。 按照提供的说明[上面](#move-your-limited-trial-project-to-an-azure-resource)若要打开在项目设置页上，然后选择__将移到 Azure__。 


5. 在移动到 Azure 窗口中，选中"移动到其他 Azure 目录？"复选框。 选择你想要移动到你的项目并输入要移动到你的项目资源的资源 ID 的目录。 单击__移动__。 



5. 请记住，你的项目现在位于不同目录。 若要查找你的项目，您需要切换到你的项目是在自定义视觉 web 门户上的同一目录。 在 Azure 门户和[自定义视觉网站](https://customvision.ai)，可以从屏幕的右上角的下拉列表帐户菜单中选择你的目录。 

## <a name="next-steps"></a>后续步骤

你的项目现在已移至 Azure 资源。 您需要更新训练和预测中任何已编写的应用程序密钥。
