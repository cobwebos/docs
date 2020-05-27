---
title: 使用 Azure 资源管理器模板部署服务
titleSuffix: Azure Cognitive Search
description: 可以使用 Azure 资源管理器模板快速部署 Azure 认知搜索服务实例。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 670cebe1c0f1c9002e33b729d0db9ee9f9a01283
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682527"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>快速入门：使用资源管理器模板部署认知搜索

本文引导你完成在 Azure 门户中使用资源管理器模板部署 Azure 认知搜索资源的过程。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-cognitive-search-service"></a>创建认知搜索服务

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-azure-search-create/)。

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50,70-85":::

此模板中定义的 Azure 资源：

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices)：创建 Azure 认知搜索服务

### <a name="deploy-the-template"></a>部署模板

选择下图登录到 Azure 并打开一个模板。 该模板将创建一个 Azure 认知搜索资源。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

门户将显示一个窗体，可在其中轻松提供参数值。 某些参数已由模板中的默认值预先填充。 需要提供自己的订阅、资源组、位置和服务名称。 若要在 [AI 扩充](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)管道中使用认知服务（例如，要分析文本的二进制图像文件），请选择同时提供认知搜索和认知服务的位置。 对于 AI 扩充工作负载，这两个服务必须位于同一区域。 填写窗体后，需要同意条款和条件，然后选择“购买”按钮以完成部署。

> [!div class="mx-imgBorder"]
> ![Azure 门户中的模板屏幕截图](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>查看已部署的资源

部署完成后，可以在门户中访问新的资源组和新的搜索服务。

## <a name="clean-up-resources"></a>清理资源

其他认知搜索快速入门和教程都是在本快速入门的基础上编写的。 如果你打算继续学习后续快速入门和教程，可能需要保留这些资源。 如果不再需要，可以删除资源组，这会删除认知搜索服务和相关资源。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Azure 资源管理器模板创建了一个认知搜索服务，并验证了部署。 若要详细了解认知搜索和 Azure 资源管理器，请继续阅读以下文章。

 - 阅读 [Azure 认知搜索概述](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - 为搜索服务[创建索引](https://docs.microsoft.com/azure/search/search-get-started-portal)
 - 使用门户向导[创建搜索应用](https://docs.microsoft.com/azure/search/search-create-app-portal)
 - [创建技能集](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)，用于从数据中提取信息


