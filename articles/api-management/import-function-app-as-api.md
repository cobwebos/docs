---
title: 使用 Azure 门户将函数应用导入为 API | Microsoft Docs
description: 本教程演示如何使用 API 管理 (APIM) 将函数应用导入为 API。
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
ms.openlocfilehash: 1962a4aac8e2d15caf4ec33998da1985d3b8a9af
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934781"
---
# <a name="import-a-function-app-as-an-api"></a>将函数应用导入为 API

本文演示如何将函数应用导入为 API。 此外，介绍如何测试 APIM API。

在本文中，学习如何：

> [!div class="checklist"]
> * 将函数应用导入为 API
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

## <a name="prerequisites"></a>先决条件

+ 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)
+ 确保订阅中已有函数应用。 有关详细信息，请参阅[创建函数应用](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>导入和发布后端 API

1. 在“API 管理”下面选择“API”。
2. 从“添加新的 API”列表中选择“函数应用”。

    ![函数应用](./media/import-function-app-as-api/function-app-api.png)
3. 按“浏览”查看订阅中的函数应用列表。
4. 选择应用。 APIM 找到与所选应用关联的 swagger 后，将其提取并导入。 
5. 添加 API URL 后缀。 后缀是用于在该 APIM 实例中标识此特定 API 的名称。 它在此 APIM 实例中必须唯一。
6. 通过关联 API 与产品来发布 API。 本例中使用了“无限制”产品。  如果想要发布 API 并使其对开发人员可用，请将其添加到产品中。 可在 API 创建期间执行此操作，或稍后进行设置。

    产品是一个或多个 API 的关联。 可包含多个 API，并通过开发人员门户将其提供给开发人员。 开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果创建了 APIM 实例，那么你已是管理员，因此默认情况下订阅了每个产品。

    默认情况下，每个 API 管理实例附带两个示例产品：

    * **入门**
    * **不受限制**   
7. 选择**创建**。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>在 Azure 门户中测试新的 APIM API

可直接从 Azure 门户调用操作，这样可以方便地查看和测试 API 的操作。  

1. 选择上一步中创建的 API。
2. 按“测试”选项卡。
3. 选择某个操作。

    该页将显示查询参数的字段和标头的字段。 其中一个标头是“Ocp-Apim-Subscription-Key”，适用于和此 API 关联的产品订阅密钥。 如果创建了 APIM 实例，那么你已是管理员，因此会自动填充该密钥。 
1. 按“发送”。

    后端以“200 正常”和某些数据做出响应。

## <a name="call-operation"> </a>从开发人员门户调用操作

此外，也可从“开发人员门户”调用操作来测试 API。 

1. 选择在“导入和发布后端 API”步骤中创建的 API。
2. 按“开发人员门户”。

    “开发人员门户”站点随即打开。
3. 选择创建的 **API**。
4. 单击要测试的操作。
5. 按“试用”。
6. 按“发送”。
    
    调用操作后，开发人员门户会显示**响应状态**、**响应标头**以及任何**响应内容**。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)