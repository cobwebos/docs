---
title: 教程：使用 Azure 门户模拟 API 响应 | Microsoft Docs
description: 本教程介绍如何使用 API 管理 (APIM) 对 API 设置一个策略，使该 API 返回模拟响应。 当后端不可用于发送实际响应时，开发人员可以使用此方法继续实现和测试 API 管理实例。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4383ce3788f6fade5299d69ef99b80221c58d9e7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="mock-api-responses"></a>模拟 API 响应

可将后端 API 导入 APIM API，或者手动创建和管理后端 API。 本教程中的步骤说明如何使用 APIM 创建空白 API 并手动对其进行管理。 本教程介绍如何对 API 设置一个策略，使该 API 返回模拟响应。 即使后端不可用于发送实际响应，开发人员也仍可以使用此方法继续实现和测试 APIM 实例。 在如下所述的多种情况下，如果能够模拟响应，则很有帮助：

+ 当以 API 的结构设计为主，后端实现为辅时。 或者并行开发后端时。
+ 当后端暂时不可正常运行或不可缩放时。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建测试 API 
> * 将操作添加到测试 API
> * 启用响应模拟
> * 测试模拟 API

![模拟操作响应](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>先决条件

完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。

## <a name="create-a-test-api"></a>创建测试 API 

本部分中的步骤说明如何创建不带后端的空白 API。 此外，说明如何将某个操作添加到该 API。 完成本部分中的步骤之后调用该操作会生成错误。 完成“启用响应模拟”部分中的步骤后，不会出现任何错误。

1. 在“API 管理”下面选择“API”。
2. 在左侧菜单中，选择“+ 添加 API”。
3. 从列表中选择“空白 API”。
4. 输入“测试 API”作为“显示名称”。
5. 输入“无限制”作为“产品”。
6. 选择**创建**。

## <a name="add-an-operation-to-the-test-api"></a>将操作添加到测试 API

1. 选择上一步中创建的 API。
2. 单击“+ 添加操作”。

    ![模拟操作响应](./media/mock-api-responses/mock-api-responses02.png)

    |设置|值|说明|
    |---|---|---|
    |**URL**（HTTP 谓词）|GET|可以选择预定义的 HTTP 谓词之一。|
    |**URL** |*/test*|API 的 URL 路径。 |
    |**显示名称**|测试调用|显示在**开发人员门户**中的名称。|
    |**说明**||输入用于在**开发人员门户**中通过此 API 向开发人员提供文档的操作的说明。|
    |“查询”选项卡||可以添加查询参数。 除了提供名称和说明以外，还可以提供可分配到此参数的值。 其中一个值可被标记为默认（可选）。|
    |“请求”选项卡||可以定义请求内容类型、示例和架构。 |
    |“响应”选项卡|请参阅此表格后面的步骤。|定义响应状态代码、内容类型、示例和架构。|

3. 选择“URL”、“显示名称”和“说明”字段下面的“响应”选项卡。
4. 单击“+ 添加响应”。
5. 从列表中选择“200 OK”。
6. 在右侧的“表示形式”标题下，选择“+ 添加表示形式”。
7. 在搜索框中输入“application/json”，选择“application/json”内容类型。
8. 在“示例”文本框中，输入“{sampleField' : 'test'}”。
9. 选择“保存”。

## <a name="enable-response-mocking"></a>启用响应模拟

1. 选择在“创建测试 API”步骤中创建的 API。
2. 选择添加的测试操作。
2. 在右侧窗口中，单击“设计”选项卡。
3. 在“入站处理”窗口中，单击铅笔图标。
4. 在“模拟”选项卡中，为“模拟行为”选择“静态响应”。
5. 在“API 管理返回以下响应:”文本框中，键入 **200 OK, application/json**。 此项选择指示 API 应返回上一部分中定义的响应示例。
6. 选择“保存”。

## <a name="test-the-mocked-api"></a>测试模拟 API

1. 选择在“创建测试 API”步骤中创建的 API。
2. 打开“测试”选项卡。
3. 确保“测试调用”API 已选中。

    > [!TIP]
    > 包含文本“模拟已启用”的黄色条形表示从 API 管理返回的响应发送了模拟策略，而不是实际的后端响应。

3. 选择“发送”以发出测试调用。
4. “HTTP 响应”显示提供的 JSON，如本教程第一部分中的示例所示。

## <a name="video"></a>视频

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 创建测试 API
> * 将操作添加到测试 API
> * 启用响应模拟
> * 测试模拟 API

转到下一教程：

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
