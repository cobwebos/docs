---
title: 语言理解的预构建模型
titleSuffix: Azure Cognitive Services
description: LUIS 包括一组预构建模型，用于快速添加常见会话用户方案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013579"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>为常见使用方案添加预构建模型 

LUIS 包括一组预构建模型，用于快速添加常见会话用户方案。 这是一种快速简便的方法，无需为这些功能设计模型，即可向会话客户端应用程序添加功能。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>添加预生成域

1. 在“我的应用”**** 页面上，选择你的应用。 这将打开你的应用并显示该应用的“生成”部分。**** 

1. 从左侧工具栏中选择**预构建域**。 

1. 找到要添加到应用的域，然后选择 **"添加域**"按钮。

    > [!div class="mx-imgBorder"]
    > ![添加“日历”预生成域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>添加预生成意向

1. 在“我的应用”**** 页面上，选择你的应用。 这将打开你的应用并显示该应用的“生成”部分。**** 

1. 在 **"意向"** 页上，从意向列表上方的工具栏**中选择"添加预构建的域意图**"。 

1. 从弹出对话框中选择“Utilities.Cancel”**** 意向。 

    > [!div class="mx-imgBorder"]
    > ![添加预生成意向](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. 选择“完成”按钮。****

## <a name="add-a-prebuilt-entity"></a>添加预构建实体

1. 单击“我的应用”**** 页上的应用名称打开应用，然后单击左侧的“实体”****。 

1. 在“实体”**** 页上，单击“添加预构建实体”****。

1. 在 **"添加预构建实体"** 对话框中，选择预构建的实体。 

    > [!div class="mx-imgBorder"]
    > ![“添加预构建实体”对话框](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. 选择 **"完成**"。 添加实体后，你不需要训练应用。 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>发布以从预测终结点查看预构建模型

查看预构建模型值的最简单方法是从已发布的终结点进行查询。 

## <a name="entities-containing-a-prebuilt-entity-token"></a>包含预构建实体令牌的实体
 
如果具有受预构建实体约束的机器学习实体，则向机器学习实体添加子组件，然后添加预构建实体的约束。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用 REST API 从 .csv 生成模型](./luis-tutorial-node-import-utterances-csv.md)
