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
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: abf149d621d283744a71e08ea4ac2199a1994d57
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680956"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>为常见使用方案添加预构建的模型

LUIS 包含一组预构建的模型，用于快速添加常见的对话用户方案。 这是向对话客户端应用程序添加功能的一种快速简便的方法，无需为这些功能设计模型。

## <a name="add-a-prebuilt-domain"></a>添加预生成域

1. 登录到[LUIS 门户](https://www.luis.ai)，选择**订阅**和**创作资源**，查看分配给该创作资源的应用。
1. 通过在 **"我的应用**" 页上选择应用程序的名称来打开应用。

1. 从左侧工具栏选择 "预生成的**域**"。

1. 找到要添加到应用的域，然后选择 "**添加域**" 按钮。

    > [!div class="mx-imgBorder"]
    > ![添加“日历”预生成域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>添加预生成意向

1. 登录到[LUIS 门户](https://www.luis.ai)，选择**订阅**和**创作资源**，查看分配给该创作资源的应用。
1. 通过在 **"我的应用**" 页上选择应用程序的名称来打开应用。

1. **在 "方法" 页上**，从 "方法" 列表上方的工具栏中选择 "添加预生成的**域意向**"。

1. 从弹出对话框中选择一个意向。

    > [!div class="mx-imgBorder"]
    > ![添加预生成意向](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. 选择“完成”按钮。****

## <a name="add-a-prebuilt-entity"></a>添加预构建实体
1. 登录到[LUIS 门户](https://www.luis.ai)，选择**订阅**和**创作资源**，查看分配给该创作资源的应用。
1. 通过在 **"我的应用**" 页上选择应用程序的名称来打开应用。
1. 选择左侧的**实体**。

1. 在 "**实体**" 页上，选择 "**添加预建实体**"。

1. 在 "**添加预生成实体**" 对话框中，选择预生成的实体。

    > [!div class="mx-imgBorder"]
    > ![“添加预构建实体”对话框](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. 选择“完成”。 添加实体后，你不需要训练应用。

## <a name="add-a-prebuilt-domain-entity"></a>添加预生成的域实体
1. 登录到[LUIS 门户](https://www.luis.ai)，选择**订阅**和**创作资源**，查看分配给该创作资源的应用。
1. 通过在 **"我的应用**" 页上选择应用程序的名称来打开应用。
1. 选择左侧的**实体**。

1. 在 "**实体**" 页上，选择 "**添加预生成的域实体**"。

1. 在 "**添加预生成的域模型**" 对话框中，选择 "预生成的域" 实体。

1. 选择“完成”。 添加实体后，你不需要训练应用。

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>发布以查看从预测终结点预生成的模型

查看预先生成的模型的值的最简单方法是从已发布的终结点进行查询。

## <a name="entities-containing-a-prebuilt-entity-token"></a>包含预生成实体标记的实体

如果你的机器学习实体需要预先生成的实体的必需功能，请将子实体添加到机器学习实体，然后添加预生成实体的_所需_功能。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [通过 REST Api 从 .csv 生成模型](./luis-tutorial-node-import-utterances-csv.md)
