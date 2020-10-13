---
title: 用于语言理解的预生成模型
titleSuffix: Azure Cognitive Services
description: LUIS 提供了一组预生成模型，用以快速添加常用的对话式用户方案。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2080139732d71aa5df9a9d4ad13fcc4545128dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541911"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>为常用使用方案添加预生成模型

LUIS 提供了一组预生成模型，用以快速添加常用的对话式用户方案。 这是一种快速简单地向对话式客户端应用程序添加功能的方法，不需要为这些功能设计模型。

## <a name="add-a-prebuilt-domain"></a>添加预生成域

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。

1. 在左侧工具栏中选择“预生成域”。

1. 找到要添加到应用的域，然后选择“添加域”按钮。

    > [!div class="mx-imgBorder"]
    > ![添加“日历”预生成域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>添加预生成意向

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。

1. 在“意向”页面上，从意向列表上方的工具栏中选择“添加预生成域意向” 。

1. 从弹出的对话框中选择一个意向。

    > [!div class="mx-imgBorder"]
    > ![添加预生成意向](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. 选择“完成”按钮。****

## <a name="add-a-prebuilt-entity"></a>添加预构建实体
1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。
1. 在左侧选择“实体”。

1. 在“实体”页上，选择“添加预生成实体” 。

1. 在“添加预生成实体”对话框中，选择预生成实体。

    > [!div class="mx-imgBorder"]
    > ![“添加预构建实体”对话框](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. 选择“完成”  。 添加实体后，你不需要训练应用。

## <a name="add-a-prebuilt-domain-entity"></a>添加预生成域实体
1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。
1. 在左侧选择“实体”。

1. 在“实体”页上，选择“添加预生成域实体” 。

1. 在“添加预生成域模型”对话框中，选择“预生成域实体”。

1. 选择“完成”  。 添加实体后，你不需要训练应用。

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>发布并查看预测终结点的预生成模型

查看预生成模型的值的最简单方法是从已发布的终结点进行查询。

## <a name="entities-containing-a-prebuilt-entity-token"></a>包含预生成实体令牌的实体

如果你的机器学习实体需要预生成实体的必需功能，请向机器学习实体添加一个子实体，然后添加预生成实体的“必需”功能。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用 REST API 从 .csv 生成模型](./luis-tutorial-node-import-utterances-csv.md)
