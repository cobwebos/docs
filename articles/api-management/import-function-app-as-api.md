---
title: 在 Azure 门户中将 Azure Functions 应用作为 API 导入 | Microsoft Docs
description: 本教程演示如何使用 Azure API 管理将 Azure Functions 应用作为 API 导入。
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
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239046"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>将 Azure Functions 应用作为 API 导入

本文演示如何将 Azure Functions 应用作为 API 导入。 本文还介绍如何测试 Azure API 管理 API。

在本文中，学习如何：

> [!div class="checklist"]
> * 将 Functions 应用作为 API 导入
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

## <a name="prerequisites"></a>先决条件

+ 完成快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
+ 确保订阅中有 Azure Functions 应用。 有关详细信息，请参阅[创建 Functions 应用](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)。
+ 为 Azure Functions 应用[创建 OpenAPI 定义](../azure-functions/functions-openapi-definition.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>导入和发布后端 API

1. 在“API 管理”下选择“API”。
2. 在“添加新的 API”列表中选择“Functions 应用”。

    ![Functions 应用](./media/import-function-app-as-api/function-app-api.png)
3. 选择“浏览”，查看订阅中的 Functions 应用列表。
4. 选择应用。 API 管理找到与所选应用关联的 swagger 后，将其提取并导入。 
5. 添加 API URL 后缀。 后缀是用于在该 API 管理实例中标识此特定 API 的名称。 在该 API 管理实例中，后缀必须唯一。
6. 通过关联 API 与产品来发布 API。 本例中使用了“无限制”产品。 若要发布 API 并使其可供开发人员使用，请将该 API 添加到产品中。 可以在创建 API 时将其添加到产品中，也可以在以后添加。

    产品是一个或多个 API 的关联。 可以包含多个 API，并通过开发人员门户将其提供给开发人员。 开发人员必须先订阅产品才能访问 API。 开发人员在订阅时会得到一个订阅密钥，适用于该产品中的任何 API。 如果你创建了 API 管理实例，你就是管理员。 默认情况下，管理员订阅每个产品。

    默认情况下，每个 API 管理实例附带两个示例产品：

    * **入门**
    * **不受限制**   
7. 选择**创建**。

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>在 Azure API 管理中填充 Azure 函数密钥

如果导入的 Azure Functions 应用受密钥保护，则 API 管理会自动为它们创建*命名值*。 API 管理不使用机密来填充条目。 针对每个条目完成以下步骤：  

1. 在 API 管理实例中，选择“命名值”选项卡。
2. 选择某个条目，然后选择侧栏中的“显示值”。

    ![命名值](./media/import-function-app-as-api/apim-named-values.png)

3. 如果“值”框中显示的文本类似于 **\<Azure Functions 名称\> 的代码**，请转到“Functions 应用”，然后转到“Functions”。
4. 选择“管理”，然后根据应用的身份验证方法复制相关密钥。

    ![Functions 应用 - 复制密钥](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. 粘贴“值”框中的密钥，然后选择“保存”。

    ![Functions 应用 - 粘贴密钥值](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>在 Azure 门户中测试新的 API 管理 API

可直接从 Azure 门户调用操作。 使用 Azure 门户可以方便地查看和测试 API 的操作。  

1. 选择在上一部分创建的 API。
2. 选择“测试”选项卡。
3. 选择操作。

    该页将显示查询参数的字段和标头的字段。 其中一个标头是 **Ocp-Apim-Subscription-Key**，适用于和此 API 关联的产品订阅密钥。 如果创建了 API 管理实例，那么你已是管理员，因此会自动填充该密钥。 
4. 选择“发送”。

    后端以“200 正常”和某些数据做出响应。

## <a name="call-operation"></a>从开发人员门户调用操作

也可从开发人员门户调用操作来测试 API。 

1. 选择在[导入和发布后端 API](#create-api) 中创建的 API。
2. 选择“开发人员门户”。

    开发人员门户站点随即打开。
3. 选择创建的 **API**。
4. 选择要测试的操作。
5. 选择“试用”。
6. 选择“发送”。
    
    调用操作后，开发人员门户会显示**响应状态**、**响应标头**以及任何**响应内容**。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)