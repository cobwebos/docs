---
title: 手动运行非 HTTP 触发的 Azure Functions
description: 通过 HTTP 请求运行非 HTTP 触发的 Azure Functions
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: fd7b0be967c7a0bbc605c51408448917b5222d36
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121717"
---
# <a name="manually-run-a-non-http-triggered-function"></a>手动运行非 HTTP 触发的函数

本文演示如何通过特殊格式的 HTTP 请求手动运行非 HTTP 触发的函数。

在某些上下文中，可能需要“按需”运行间接触发的 Azure Functions。  例如，[按计划函数](./functions-create-scheduled-function.md)或者根据[其他资源的操作](./functions-create-storage-blob-triggered-function.md)来运行的函数属于间接触发的函数。 

以下示例中使用了 [Postman](https://www.getpostman.com/)，但也可以使用 [cURL](https://curl.haxx.se/)、[Fiddler](https://www.telerik.com/fiddler) 或任何其他类似的工具来发送 HTTP 请求。

## <a name="define-the-request-location"></a>定义请求位置

若要运行非 HTTP 触发的函数，需要通过某种方式向 Azure 发送运行函数的请求。 用于发出该请求的 URL 采用特定的形式。

![定义请求位置：主机名 + 文件夹路径 + 函数名](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **主机名：** 函数应用的公共位置，由函数应用的名称和 *azurewebsites.net* 或自定义域组成。
- **文件夹路径：** 若要通过 HTTP 请求访问非 HTTP 触发的函数，需通过 *admin/functions* 文件夹发送请求。
- **函数名：** 要运行的函数的名称。

请将 Postman 中的此请求位置与函数发送给 Azure 的请求中的主密钥配合使用，以便运行函数。

> [!NOTE]
> 在本地运行时，不需要函数的主密钥。 可以直接[调用函数](#call-the-function)，省略 `x-functions-key` 标头。

## <a name="get-the-functions-master-key"></a>获取函数的主密钥

1. 在 Azure 门户中导航到你的函数，选择“函数密钥”。 然后选择要复制的函数密钥。 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="找到要复制的主密钥。" border="true":::

1. 在“编辑密钥”部分中，将密钥值复制到剪贴板，然后选择“确定”。

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="将主密钥复制到剪贴板。" border="true":::

1. 复制主密钥之后，选择“代码 + 测试”，然后选择“日志”。 手动从 Postman 运行已记录函数时，会在此处看到该函数中的消息。

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="查看日志以了解主密钥测试结果。" border="true":::

> [!CAUTION]  
> 由于函数应用中提升的权限由主密钥授予，因此不应与第三方共享此密钥或在应用程序中分发此密钥。

## <a name="call-the-function"></a>调用该函数

打开 Postman 并执行以下步骤：

1. **在 URL 文本框中输入请求位置**。
1. 确保 HTTP 方法设置为 **POST**。
1. 选择“标头”选项卡。
1. 键入“x-functions-key”作为第一个密钥，并粘贴主密钥（在剪贴板中）作为值。
1. 键入“Content-Type”作为第二个密钥，然后键入“application/json”作为值。

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Postman 标头设置。" border="true":::

1. 选择“正文”选项卡。
1. 键入“{ "input": "test" }”作为请求的正文。

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Postman 正文设置。" border="true":::

1. 选择“发送”。
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="通过 Postman 发送请求。" border="true":::

    Postman 然后会报告状态“202 已接受”。

1. 接下来，返回到 Azure 门户中的函数。 查看日志，你会看到手动调用函数后显示的消息。

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="查看日志以了解主密钥测试结果。" border="true":::

## <a name="next-steps"></a>后续步骤

- [在 Azure Functions 中测试代码的策略](./functions-test-a-function.md)
- [Azure 函数事件网格触发器本地调试](./functions-debug-event-grid-trigger-local.md)
