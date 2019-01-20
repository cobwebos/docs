---
title: 手动运行非 HTTP 触发的 Azure Functions
description: 通过 HTTP 请求运行非 HTTP 触发的 Azure Functions
services: functions
keywords: ''
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 6c77e58e626ba370a6278a0f01b09578930cba09
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247106"
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

## <a name="get-the-functions-master-key"></a>获取函数的主密钥

在 Azure 门户中导航到函数，单击“管理”，找到“主机密钥”部分。 单击 *_master* 行中的“复制”按钮，将主密钥复制到剪贴板。

![从“函数管理”屏幕复制主密钥](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

复制主密钥以后，单击函数名称，返回到代码文件窗口。 接下来，单击“日志”选项卡。手动从 Postman 运行已记录函数时，会在此处看到该函数中的消息。

> [!CAUTION]  
> 由于函数应用中提升的权限由主密钥授予，因此不应与第三方共享此密钥或在应用程序中分发此密钥。

## <a name="call-the-function"></a>调用该函数

打开 Postman 并执行以下步骤：

1. **在 URL 文本框中输入请求位置**。
2. 确保 HTTP 方法设置为 **POST**。
3. **单击**“标头”选项卡。
4. 输入 **x-functions-key** 作为第一个**密钥**，并将主密钥（从剪贴板）粘贴到“值”框中。
5. 输入 **Content-Type** 作为第二个**密钥**，然后输入 **application/json** 作为**值**。

    ![Postman 标头设置](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **单击**“正文”选项卡。
7. 输入 **{ "input": "test" }** 作为请求的正文。

    ![Postman 正文设置](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. 单击“Send”。

    ![通过 Postman 发送请求](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman 然后会报告状态“202 已接受”。

接下来，返回到 Azure 门户中的函数。 找到“日志”窗口，此时会看到手动调用函数后出来的消息。

![函数日志源于手动调用](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure Functions 中测试代码的策略](./functions-test-a-function.md)
- [Azure 函数事件网格触发器本地调试](./functions-debug-event-grid-trigger-local.md)
