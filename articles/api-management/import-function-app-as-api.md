---
title: 在 API 管理中将 Azure Function App 作为 API 导入
titleSuffix: Azure API Management
description: 本教程演示如何将 Azure Function App 作为 API 导入 Azure API 管理中。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 6f4626a8c42f3a50fa273c55099158750241bfee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202914"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>在 Azure API 管理中将 Azure Function App 作为 API 导入

Azure API 管理支持将 Azure Function App 作为新 API 导入或将其追加到现有 API。 此过程会在 Azure Function App 中自动生成一个主机密钥，然后将其分配给 Azure API 管理中的命名值。

本文详细介绍如何在 Azure API 管理中将 Azure Function App 作为 API 导入。 此外还介绍测试过程。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 将 Azure Function App 作为 API 导入
> * 将 Azure Function App 追加到 API
> * 查看新的 Azure Function App 主机密钥和 Azure API 管理命名值
> * 在 Azure 门户中测试 API
> * 在开发人员门户中测试 API

## <a name="prerequisites"></a>先决条件

* 完成快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
* 确保订阅中有 Azure Functions 应用。 有关详细信息，请参阅[创建 Azure Function App](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)。 它必须包含 Functions 并将 HTTP 触发器和授权级别设置设为“匿名”或“函数”。  

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> 将 Azure Function App 作为新 API 导入

执行以下步骤，通过 Azure Function App 创建新的 API。

1. 在 Azure 门户中导航到 API 管理服务，然后从菜单中选择“API”  。

2. 在“添加新 API”列表中，选择“Function App”。  

    ![从 Function App 添加](./media/import-function-app-as-api/add-01.png)

3. 单击“浏览”，选择要导入的 Functions。 

    ![从 Function App 添加](./media/import-function-app-as-api/add-02.png)

4. 单击“Function App”部分，从可用 Function App 的列表中进行选择。 

    ![从 Function App 添加](./media/import-function-app-as-api/add-03.png)

5. 找到要从其导入 Functions 的 Function App，然后单击它并按“选择”。 

    ![从 Function App 添加](./media/import-function-app-as-api/add-04.png)

6. 选择要导入的 Functions，然后单击“选择”。 

    ![从 Function App 添加](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > 可以只导入基于 HTTP 触发器的 Functions，并将授权级别设置设为“匿名”或“函数”。  

7. 切换到“完整”视图并将“产品”分配到新 API   。 如果需要，请在创建过程中指定其他字段，也可以稍后转到“设置”  选项卡来配置这些字段。在[导入和发布第一个 API](import-and-publish.md#-import-and-publish-a-backend-api) 教程中对这些设置进行了说明。
8. 单击“创建”。 

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> 将 Azure Function App 追加到现有 API

执行以下步骤，将 Azure Function App 追加到现有 API。

1. 在 **Azure API 管理**服务实例中，从左侧的菜单选择“API”。 

2. 选择要将 Azure Function App 导入到其中的 API。 单击“...”，并从上下文菜单中选择“导入”。  

    ![从 Function App 追加](./media/import-function-app-as-api/append-01.png)

3. 单击“Function App”磁贴。 

    ![从 Function App 追加](./media/import-function-app-as-api/append-02.png)

4. 在弹出窗口中，单击“浏览”。 

    ![从 Function App 追加](./media/import-function-app-as-api/append-03.png)

5. 单击“Function App”部分，从可用 Function App 的列表中进行选择。 

    ![从 Function App 添加](./media/import-function-app-as-api/add-03.png)

6. 找到要从其导入 Functions 的 Function App，然后单击它并按“选择”。 

    ![从 Function App 添加](./media/import-function-app-as-api/add-04.png)

7. 选择要导入的 Functions，然后单击“选择”。 

    ![从 Function App 添加](./media/import-function-app-as-api/add-05.png)

8. 单击“导入”  。

    ![从 Function App 追加](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a> 授权

导入 Azure Function App 会自动生成：

* 主机密钥，该密钥位于 Function App 中，其名称为 apim-{*你的 Azure API 管理服务实例名称*}；
* 命名值，该值位于 Azure API 管理实例中，其名称为 {*你的 Azure Function App 实例名称*}-key，其中包含创建的主机密钥。

对于 2019 年 4 月 4 日以后创建的 API，主机密钥将通过 HTTP 请求从 API 管理传递到标头中的 Function App。 较旧的 API 将主机密钥作为[查询参数](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization)传递。 可以通过对与 Function App 关联的*后端*实体进行 `PATCH Backend` [REST API 调用](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract)来更改此行为。

> [!WARNING]
> 删除或更改 Azure Function App 主机密钥或 Azure API 管理命名值的值会导致服务之间的通信中断。 这些值不自动同步。
>
> 如需轮换主机密钥，请确保 Azure API 管理中的命名值也得到修改。

### <a name="access-azure-function-app-host-key"></a>访问 Azure Function App 主机密钥

1. 导航到 Azure Function App 实例。

2. 从概览中选择“Function App 设置”。 

    ![从 Function App 添加](./media/import-function-app-as-api/keys-02-a.png)

3. 密钥位于“主机密钥”部分。 

    ![从 Function App 添加](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>访问 Azure API 管理中的命名值

导航到 Azure API 管理实例，然后在左侧菜单中选择“命名值”。  Azure Function App 密钥存储在该处。

![从 Function App 添加](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> 在 Azure 门户中测试新的 API

可直接从 Azure 门户调用操作。 使用 Azure 门户可以方便地查看和测试 API 的操作。  

1. 选择在上一部分创建的 API。

2. 选择“测试”选项卡。 

3. 选择操作。

    该页将显示查询参数的字段和标头的字段。 其中一个标头是 **Ocp-Apim-Subscription-Key**，适用于和此 API 关联的产品订阅密钥。 如果创建了 API 管理实例，那么你已是管理员，因此会自动填充该密钥。 

4. 选择“发送”。 

    后端以“200 正常”和某些数据做出响应  。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
