---
title: 将有限的试用项目移到 Azure
titleSuffix: Azure Cognitive Services
description: 自定义视觉中是否有有限试用项目？ 本文介绍如何使用迁移脚本将它移动到 Azure。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 0a2b48a6c046150f6e685ecda0c0d765342e0194
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818949"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>如何将有限试用项目迁移到 Azure

随着自定义影像服务完成其迁移到 Azure，在 Azure 外部对有限试用项目的支持即将结束。 本文档将演示如何使用自定义视觉 Api 将有限试用项目复制到 Azure 资源。

在2019年3月25日结束了[自定义视觉网站](https://customvision.ai)上的查看有限试用项目的支持。 本文档现在演示如何通过 GitHub 上的[迁移 python 脚本](https://github.com/Azure-Samples/custom-vision-move-project)使用自定义视觉 api，将项目复制到 Azure 资源。

有关更多详细信息，包括有限试用版过程中的关键截止时间，请参阅[发行说明](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019)或发送给有限试用项目所有者的电子邮件通信。

[迁移脚本](https://github.com/Azure-Samples/custom-vision-move-project)允许您通过下载并上传当前迭代中的所有标记、区域和图像来重新创建项目。 这样，你就可以在新的订阅中保留新的项目，然后你可以进行训练。

## <a name="prerequisites"></a>先决条件

- 需要一个有效的 Azure 订阅，该订阅与要用于登录[自定义视觉网站](https://customvision.ai)的 Microsoft 帐户或 AZURE ACTIVE DIRECTORY （AAD）帐户相关联。 
    - 如果没有 Azure 帐户，请免费[创建帐户](https://azure.microsoft.com/free/)。
    - 有关订阅和资源的 Azure 概念的简介，请参阅[azure 开发人员指南。](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>在 Azure 门户中创建自定义视觉资源

若要在 Azure 中使用自定义影像服务，你将需要在[Azure 门户](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)中创建自定义视觉训练和预测资源。 

可以将多个项目关联到单个资源。 提供有关[定价和限制](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas)的更多详细信息。 若要继续使用自定义影像服务免费，你可以在 Azure 门户中选择 F0 层。 

> [!NOTE]
> 将自定义视觉项目移到 Azure 资源时，它将继承该 Azure 资源的基础[权限]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。 如果组织中的其他用户是你的项目所在的 Azure 资源的所有者，他们将能够在[自定义视觉网站](https://customvision.ai)上访问你的项目。 同样，删除资源将删除项目。  

## <a name="find-your-limited-trial-project-information"></a>查找有限试用版项目信息

若要移动项目，你将需要尝试迁移的项目的 "_项目 ID_ " 和 "_培训密钥_"。 如果没有此信息，请访问[https://www.customvision.ai/projects](https://www.customvision.ai/projects)获取每个项目的 ID 和密钥。 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>使用 Python 示例代码将项目复制到 Azure

遵循[示例代码说明](https://github.com/Azure-Samples/custom-vision-move-project)，使用有限试用密钥和项目 ID 作为 "源" 材料，并使用你创建的新 Azure 资源中的密钥作为 "目标"。

默认情况下，所有有限试用项目都托管在美国中南部 Azure 区域中。

## <a name="next-steps"></a>后续步骤

你的项目现在已移动到 Azure 资源。 需要在已编写的任何应用程序中更新训练和预测密钥。

若要查看[自定义视觉网站](https://customvision.ai)上的项目，请使用用于登录 Azure 门户的同一帐户登录。 如果看不到你的项目，请确认你位于[自定义视觉网站](https://customvision.ai)的同一目录中，该目录位于 Azure 门户中的资源所在的目录。 在 "Azure 门户" 和 "CustomVision.ai" 中，可以从屏幕右上角的下拉 "用户" 菜单中选择目录。