---
title: 使用 Azure 门户导入 OpenAPI 规范 | Microsoft Docs
description: 了解如何使用 API 管理导入 OpenAPI 规范。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: f5132215b1fda93c62c1fbea46c3266fcc44ec46
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="import-an-openapi-specification"></a>导入 OpenAPI 规范

本文介绍如何导入 http://conferenceapi.azurewebsites.net?format=json 中的“OpenAPI 规范”后端 API。 此后端 API 由 Microsoft 提供并托管在 Azure 上。 此外，介绍如何测试 APIM API。

> [!IMPORTANT]
> 如需与 OpenAPI 导入相关的重要信息和提示，请参阅此[文档](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/)。

在本文中，学习如何：

> [!div class="checklist"]
> * 导入“OpenAPI 规范”后端 API
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

## <a name="prerequisites"></a>先决条件

完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>导入和发布后端 API

1. 在“API 管理”下面选择“API”。
2. 从“添加新的 API”列表中选择“OpenAPI 规范”。
    ![OpenAPI 规范](./media/import-api-from-oas/oas-api.png)
3. 输入相应的设置。 可以在创建过程中设置所有 API 值。 或者，以后可以转到“设置”选项卡来设置一部分值。 <br/> 如果按 **Tab** 键，部分（或所有）字段中会填充来自指定后端服务的信息。

    ![创建 API](./media/api-management-get-started/create-api.png)

    |设置|值|说明|
    |---|---|---|
    |**OpenAPI 规范**|http://conferenceapi.azurewebsites.net?format=json|引用实现 API 的服务。 API 管理将请求转发到此地址。|
    |**显示名称**|演示会议 API|如果在输入服务 URL 后按 Tab 键，APIM 将根据 json 中的内容填充此字段。 <br/>此名称显示在开发人员门户中。|
    |**Name**|*demo-conference-api*|提供 API 的唯一名称。 <br/>如果在输入服务 URL 后按 Tab 键，APIM 将根据 json 中的内容填充此字段。|
    |**说明**|提供 API 的可选说明。|如果在输入服务 URL 后按 Tab 键，APIM 将根据 json 中的内容填充此字段。|
    |**API URL 后缀**|会议|此后缀附加到 API 管理服务的基础 URL。 API 管理通过其后缀区分 API，因此后缀对给定发布者上的每个 API 必须唯一。|
    |**URL 方案**|*HTTPS*|确定可用于访问 API 的协议。 |
    |**产品**|*不受限制*| 通过关联 API 与产品来发布 API。 若要根据需要将此新 API 添加到某个产品，请键入该产品的名称。 多次重复此步骤以将 API 添加到多个产品。<br/>产品是一个或多个 API 的关联。 可包含多个 API，并通过开发人员门户将其提供给开发人员。 开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果创建了 APIM 实例，那么你已是管理员，因此默认情况下订阅了每个产品。<br/> 每个 API 管理实例默认附带两个示例产品：“入门”和“无限制”。 |

4. 选择**创建**。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>在 Azure 门户中测试新的 APIM API

可直接从 Azure 门户调用操作，这样可以方便地查看和测试 API 的操作。

1. 选择上一步中创建的 API。
2. 按“测试”选项卡。

    ![测试 API](./media/api-management-get-started/test-api.png)
1. 单击“GetSpeakers”。

    该页显示查询参数的字段，但在此示例中，我们并没有这些字段。 此外，还会显示标头的字段。 其中一个标头是“Ocp-Apim-Subscription-Key”，适用于和此 API 关联的产品订阅密钥。 如果创建了 APIM 实例，那么你已是管理员，因此会自动填充该密钥。
4. 按“发送”。

    后端以“200 正常”和某些数据做出响应。

## <a name="call-operation"> </a>从开发人员门户调用操作

此外，也可从“开发人员门户”调用操作来测试 API。

1. 选择在“导入和发布后端 API”步骤中创建的 API。
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

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
