---
title: 在 Azure 中创建由 Blob 存储触发的函数
description: 使用 Azure Functions 创建由添加到 Blob 存储容器的项调用的无服务器函数。
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122936"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>在 Azure 中创建由 Blob 存储触发的函数

了解如何创建在文件上传到 Blob 存储容器时或在 Blob 存储容器中更新时触发的函数。

## <a name="prerequisites"></a>先决条件

+ Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-function-app"></a>创建 Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

已成功创建新的函数应用。

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="函数应用已成功创建。" border="true":::

接下来，在新的 Function App 中创建一个函数。

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>创建 Azure Blob 存储触发的函数

1. 选择“函数”，然后选择“+ 添加”以添加新函数。

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="在 Azure 门户中选择函数模板。" border="true":::

1. 选择“Azure Blob 存储触发器”模板。

1. 使用图像下的表中指定的设置。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="命名并配置 Blob 存储触发的函数。" border="true":::

    | 设置 | 建议的值 | 说明 |
    |---|---|---|
    | **新建函数** | 在 Function App 中唯一 | 此 Blob 触发函数的名称。 |
    | **路径**   | samples-workitems/{name}    | 所监视的 Blob 存储中的位置。 blob 的文件名将作为 _name_ 参数传入绑定。  |
    | **存储帐户连接** | AzureWebJobsStorage | 可以使用 Function App 已在使用的存储帐户连接，也可以创建一个新的存储帐户连接。  |

1. 选择“创建函数”以创建函数。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="创建 Blob 存储触发的函数。" border="true":::

接下来，创建“samples-workitems”容器。

## <a name="create-the-container"></a>创建容器

1. 在函数的“概览”页上，选择资源组。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="选择 Azure 门户资源组。" border="true":::

1. 查找并选择资源组的存储帐户。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="访问存储帐户。" border="true":::

1. 选择“容器”，然后选择“+容器”。 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="将容器添加到 Azure 门户中的存储帐户。" border="true":::

1. 在“名称”字段中键入 `samples-workitems`，然后选择“创建”。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="命名存储容器。" border="true":::

现在已有 blob 容器，可以通过将文件上传到该容器来测试函数。

## <a name="test-the-function"></a>测试函数

1. 返回到 Azure 门户中，浏览到函数，展开页面底部的“日志”并确保日志流式处理未暂停。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="在 Azure 门户中展开日志。" border="true":::

1. 在单独的浏览器窗口中，转到 Azure 门户中的资源组，然后选择存储帐户。

1. 选择“容器”，然后选择“samples-workitems”容器。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="转到 Azure 门户中的 samples-workitems 容器。" border="true":::

1. 选择“上传”，然后选择文件夹图标以选择要上传的文件。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="将文件上传到 blob 容器。" border="true":::

1. 浏览到本地计算机上的文件（如图像文件），选择该文件。 选择“打开”，然后选择“上传” 。

1. 返回到函数日志并验证是否已读取 blob。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="查看日志中的消息。" border="true":::

    >[!NOTE]
    > 当 Function App 在默认消耗计划中运行时，添加或更新 blob 与触发函数之间可能会有多达几分钟的延迟。 如果需要在 blob 触发的函数中降低延迟，请考虑在应用服务计划中运行 Function App。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

已创建在 Blob 存储中添加或更新 blob 时运行的函数。 有关 Blob 存储触发器的详细信息，请参阅 [Azure Functions Blob 存储绑定](functions-bindings-storage-blob.md)。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
