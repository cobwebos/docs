---
title: 教程 - 在 API 管理中模拟 API 响应 - Azure 门户 |Microsoft Docs
description: 在本教程中，你将使用 API 管理在 API 上设置策略，以便在后端无法发送实际响应时返回模拟响应。
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 78743c5f045f2544cafe88414ed996d08bacd2a0
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631070"
---
# <a name="tutorial-mock-api-responses"></a>教程：模拟 API 响应

可将后端 API 导入 API 管理 (APIM) API，或者手动创建和管理后端 API。 本教程中的步骤说明如何使用 APIM 创建空白 API 并手动对其进行管理，然后在 API 上设置策略，使其返回模拟响应。 即使后端不可用于发送实际响应，开发人员也仍可以使用此方法继续实现和测试 APIM 实例。 

在如下所述的多种情况下，能够模拟响应会很有帮助：

+ 当以 API 的结构设计为主，后端实现为辅时。 或者并行开发后端时。
+ 当后端暂时不可正常运行或不可缩放时。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建测试 API 
> * 将操作添加到测试 API
> * 启用响应模拟
> * 测试模拟 API


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="模拟 API 响应":::

## <a name="prerequisites"></a>先决条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 了解 [Azure API 管理中策略的概念](api-management-howto-policies.md)。
+ 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。

## <a name="create-a-test-api"></a>创建测试 API 

本部分中的步骤说明如何创建不带后端的空白 API。 


1. 登录到 Azure 门户，并导航到 API 管理实例。
1. 选择“API” > “+ 添加 API” > “空白 API”  。
1. 在“创建空白 API”窗口中，选择“完整” 。
1. 输入“测试 API”作为“显示名称”。
1. 为“产品”选择“无限制” 。
1. 请确保已在“网关”中选择“托管” 。
1. 选择“创建”  。

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="模拟 API 响应":::

## <a name="add-an-operation-to-the-test-api"></a>将操作添加到测试 API

API 公开一个或多个操作。 在本部分中，将操作添加到你创建的空白 API。 完成本部分中的步骤之后调用该操作会生成错误。 完成后面[启用响应模拟](#enable-response-mocking)部分中的步骤后，不会出现任何错误。

1. 选择上一步中创建的 API。
1. 选择“+ 添加操作”。****
1. 在“前端”窗口中，输入以下值。

     | 设置             | 值                             | 说明                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **显示名称**    | 测试调用                        | 显示在[开发人员门户](api-management-howto-developer-portal.md)中的名称。                                                                                                                                       |
    | **URL**（HTTP 谓词） | GET                               | 选择预定义的 HTTP 谓词之一。                                                                                                                                         |
    | **URL**             | */test*                           | API 的 URL 路径。                                                                                                                                                                       |
    | **说明**     |                                   |  操作的可选说明，用于在开发人员门户中向使用此 API 的开发人员提供文档。                                                    |
    
1. 选择“URL”、“显示名称”和“说明”字段下面的“响应”选项卡。 在此选项卡上输入设置以定义响应状态代码、内容类型、示例和架构。
1. 选择“+ 添加响应”，并从列表中选择“200 OK” 。
1. 在右侧的“表示形式”标题下，选择“+ 添加表示形式”。  
1. 在搜索框中输入“application/json”，选择“application/json”内容类型。
1. 在“示例”文本框中，输入 `{ "sampleField" : "test" }`。
1. 选择“保存”。

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="模拟 API 响应" border="false":::

虽然此示例中不需要，但也可以在其他选项卡上配置 API 操作的其他设置，包括：


|选项卡      |说明  |
|---------|---------|
|**查询**     |  添加查询参数。 除了提供名称和说明以外，还可以提供可分配给查询参数的值。 其中一个值可被标记为默认（可选）。        |
|**请求**     |  定义请求内容类型、示例和架构。       |

## <a name="enable-response-mocking"></a>启用响应模拟

1. 选择在[创建测试 API](#create-a-test-api) 中创建的 API。
1. 选择添加的测试操作。
1. 在右侧窗口中，确保已选中“设计”选项卡。
1. 在“入站处理”窗口中，选择“+ 添加策略” 。

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="模拟 API 响应" border="false":::

1. 从库中选择“模拟响应”。

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="模拟 API 响应" border="false":::

1. 在“API 管理响应”文本框中，键入 **200 OK, application/json**。 此项选择指示 API 应返回上一部分中定义的响应示例。

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="模拟 API 响应":::

1. 选择“保存”。

    > [!TIP]
    > API 包含文本“模拟已启用”的黄色条形表示从 API 管理返回的响应发送了模拟策略，而不是实际的后端响应。

## <a name="test-the-mocked-api"></a>测试模拟 API

1. 选择在[创建测试 API](#create-a-test-api) 中创建的 API。
1. 选择“测试”选项卡。
1. 确保“测试调用”API 已选中。  选择“发送”以发出测试调用。 

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="模拟 API 响应":::

1. “HTTP 响应”显示提供的 JSON，如本教程第一部分中的示例所示。 

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="模拟 API 响应":::

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建测试 API
> * 将操作添加到测试 API
> * 启用响应模拟
> * 测试模拟 API

转到下一教程：

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
