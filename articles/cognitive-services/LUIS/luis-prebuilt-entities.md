---
title: 用于语言理解 (LUIS) 的预生成实体
titleSuffix: Azure Cognitive Services
description: LUIS 包括一组预构建实体，用于识别常见类型的信息，如日期、时间、数字、度量和货币。 预构建实体支持因 LUIS 应用的区域性而异。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2f7c724b14efd569a5993f9a9319c9004874bc43
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647589"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>用于识别常见数据类型的预生成实体

LUIS 包括一组预生成实体，用于识别常见类型的信息，如日期、时间、数字、度量和货币。 

## <a name="add-a-prebuilt-entity"></a>添加预构建实体

1. 单击“我的应用”页上的应用名称打开应用，然后单击左侧的“实体”。 

1. 在“实体”页上，单击“管理预构建实体”。

1. 在“添加预构建实体”对话框中，选择 datetimeV2 预构建实体。 

    ![“添加预构建实体”对话框](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. 选择“完成”。

## <a name="publish-the-app"></a>发布应用

查看预构建实体的值的最简单方法是从已发布的终结点进行查询。 

1. 在顶部工具栏中，选择“发布”。 发布到“生产”。 

1. 当出现绿色的成功通知后，选择“参阅终结点列表”链接来查看终结点。

1. 选择一个终结点。 将会在新的浏览器选项卡中打开该终结点。 使浏览器选项卡保持打开状态，并继续执行“测试”部分。

## <a name="test"></a>测试
添加实体后，你不需要训练应用。 

通过为 **q** 参数添加值在终结点上测试新意向。 使用下表为 **q** 提供建议的话语：

|测试话语|实体值|
|--|:--|
|预订明天的航班|2018-10-19|
|取消在 3 月 3 日的约会|LUIS 返回了已过去的最近的 3 月 3 日 (2018-03-03) 和将来的 3 月 3 日 (2019-03-03)，因为话语没有指定年份。|
|安排在上午 10 点开会|10:00:00|


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [预构建实体参考](./luis-reference-prebuilt-entities.md)
