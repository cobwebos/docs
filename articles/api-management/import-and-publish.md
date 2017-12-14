---
title: "在 Azure API 管理中导入和发布第一个 API | Microsoft Docs"
description: "了解如何在 API 管理中导入和发布第一个 API。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: cd6ceaf5f8cdcfbde5d0d2bebb4b89488d0122e9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="import-and-publish-your-first-api"></a>导入和发布第一个 API 

本教程演示如何导入 http://conferenceapi.azurewebsites.net?format=json 中的“OpenAPI 规范”后端 API。 此后端 API 由 Microsoft 提供并托管在 Azure 上。 

后端 API 导入到 API 管理 (APIM) 之后，APIM API 即成为后端 API 的外观。 在导入后端 API 时，源 API 和 APIM API 均相同。 通过 APIM，无需触摸后端 API 即可根据需要自定义外观。 有关详细信息，请参阅[转换和保护 API](transform-api.md)。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 导入第一个 API
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

![新的 API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>先决条件

完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>导入和发布后端 API

本部分演示如何导入和发布 OpenAPI 规范后端 API。
 
1. 从“API 管理”下选择“API”。
2. 从列表中选择“OpenAPI 规范”。

    ![创建 API](./media/api-management-get-started/create-api.png)

    可在创建时设置 API 或稍后转到“设置”选项卡进行设置。  

    |设置|值|说明|
    |---|---|---|
    |**OpenAPI 规范**|http://conferenceapi.azurewebsites.net?format=json|引用实现 API 的服务。 API 管理将请求转发到此地址。|
    |**显示名称**|演示会议 API|如果在输入服务 URL 后按 Tab，APIM 将根据 json 中的内容填充此字段。 <br/>该名称显示在开发人员门户中。|
    |**Name**|demo-conference-api|提供 API 的唯一名称。 <br/>如果在输入服务 URL 后按 Tab，APIM 将根据 json 中的内容填充此字段。|
    |**说明**|提供 API 的可选说明。|如果在输入服务 URL 后按 Tab，APIM 将根据 json 中的内容填充此字段。|
    |**API URL 后缀**|会议|此后缀附加到 API 管理服务的基础 URL。 API 管理通过其后缀区分 API，因此后缀对给定发布者上的每个 API 必须唯一。|
    |**URL 方案**|*HTTPS*|确定可用于访问 API 的协议。 |
    |**产品**|*不受限制*| 通过关联 API 与产品来发布 API。 若要根据需要将此新 API 添加到某个产品，请键入该产品的名称。 多次重复此步骤以将 API 添加到多个产品。<br/>产品是一个或多个 API 的关联。 可包含多个 API，并通过开发人员门户将其提供给开发人员。 开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果创建了 APIM 实例，那么你已是管理员，因此默认情况下订阅了每个产品。<br/> 默认情况下，每个 API 管理实例附带两个示例产品：“入门”和“不受限制”。 |
3. 选择“创建” 。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>在 Azure 门户中测试新的 APIM API

可直接从 Azure 门户调用操作，这样可以方便地查看和测试 API 的操作。  
1. 选择上一步中创建的 API。
2. 按“测试”选项卡。

    ![测试 API](./media/api-management-get-started/test-api.png)
3. 单击“GetSpeakers”。

    该页显示查询参数的字段，但在此示例中，我们并没有这些字段。 此外，还会显示标头的字段。 其中一个标头是“Ocp-Apim-Subscription-Key”，适用于和此 API 关联的产品订阅密钥。 如果创建了 APIM 实例，那么你已是管理员，因此会自动填充该密钥。 
4. 按“发送”。

    后端以“200 正常”和某些数据做出响应。

## <a name="call-operation"> </a>从开发人员门户调用操作

此外，也可从“开发人员门户”将操作调用到测试 API。 

1. 选中在“导入和发布后端 API”步骤中创建的 API。
2. 按“开发人员门户”。

    ![在开发人员门户中进行测试](./media/api-management-get-started/developer-portal.png)

    “开发人员门户”站点随即打开。
3. 选择“API”。
4. 选择“演示会议 API”。
5. 单击“GetSpeakers”。
    
    该页显示查询参数的字段，但在此示例中，我们并没有这些字段。 此外，还会显示标头的字段。 其中一个标头是“Ocp-Apim-Subscription-Key”，适用于和此 API 关联的产品订阅密钥。 如果创建了 APIM 实例，那么你已是管理员，因此会自动填充该密钥。
6. 按“试用”。
7. 按“发送”。
    
    调用操作后，开发人员门户会显示**响应状态**、**响应标头**以及任何**响应内容**。

## <a name="next-steps"> </a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 导入第一个 API
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

转到下一教程：

> [!div class="nextstepaction"]
> [创建和发布产品](api-management-howto-add-products.md)