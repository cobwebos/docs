---
title: 为语言理解预生成的模型
titleSuffix: Azure Cognitive Services
description: LUIS 包含一组预构建的模型，用于快速添加常见的对话用户方案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013579"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>为常见使用方案添加预构建的模型 

LUIS 包含一组预构建的模型，用于快速添加常见的对话用户方案。 这是向对话客户端应用程序添加功能的一种快速简便的方法，无需为这些功能设计模型。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>添加预生成域

1. 在“我的应用”页面上，选择你的应用。 这将打开你的应用并显示该应用的“生成”部分。 

1. 从左侧工具栏选择 "预生成的**域**"。 

1. 找到要添加到应用的域，然后选择 "**添加域**" 按钮。

    > [!div class="mx-imgBorder"]
    > ![添加日历预生成域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>添加预生成意向

1. 在“我的应用”页面上，选择你的应用。 这将打开你的应用并显示该应用的“生成”部分。 

1. **在 "方法" 页上**，从 "方法" 列表上方的工具栏中选择 "添加预生成的**域意向**"。 

1. 从弹出对话框中选择“Utilities.Cancel”意向。 

    > [!div class="mx-imgBorder"]
    > ![添加预构建意向](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. 选择“完成”按钮。

## <a name="add-a-prebuilt-entity"></a>添加预构建实体

1. 单击“我的应用”页上的应用名称打开应用，然后单击左侧的“实体”。 

1. 在“实体”页上，单击“添加预构建实体”。

1. 在 "**添加预生成实体**" 对话框中，选择预生成的实体。 

    > [!div class="mx-imgBorder"]
    > !["添加预生成实体" 对话框](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. 选择“完成”。 添加实体后，你不需要训练应用。 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>发布以查看从预测终结点预生成的模型

查看预先生成的模型的值的最简单方法是从已发布的终结点进行查询。 

## <a name="entities-containing-a-prebuilt-entity-token"></a>包含预生成实体标记的实体
 
如果拥有由预生成的实体约束的计算机学习的实体，请将子组件添加到计算机学习的实体，然后添加预生成实体的约束。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [通过 REST Api 从 .csv 生成模型](./luis-tutorial-node-import-utterances-csv.md)
