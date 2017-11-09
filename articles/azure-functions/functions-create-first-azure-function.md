---
title: "通过 Azure 门户创建第一个函数 | Microsoft Docs"
description: "了解如何使用 Azure 门户创建第一个可无服务器执行的 Azure Function。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/17/2017
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 638c3bfb5a312c196d09216f3f7bf95bbc983c10
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="create-your-first-function-in-the-azure-portal"></a>在 Azure 门户中创建第一个函数

Azure Functions 用于在[无服务器](https://azure.microsoft.com/overview/serverless-computing/)环境中执行代码，无需先创建 VM 或发布 Web 应用程序。 在本主题中，了解如何使用 Functions 在 Azure 门户中创建“hello world”函数。

![在 Azure 门户中创建 Function App](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-function-app"></a>创建 Function App

必须使用函数应用托管函数的执行。 Function App 可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

接下来，在新的 Function App 中创建一个函数。

## <a name="create-function"></a>创建 HTTP 触发的函数

1. 展开新的 Function App，并单击 **Functions** 旁边的 **+** 按钮。

2.  在“快速入门”页上，选择“WebHook + API”，选择函数的语言，然后单击“创建此函数”。 
   
    ![Azure 门户中的函数快速入门。](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

使用 HTTP 触发函数模板，采用所选语言创建函数。 可以通过发送 HTTP 请求来运行新函数。

## <a name="test-the-function"></a>测试函数

1. 在新函数中，单击“</> 获取函数 URL”，选择“默认(函数密钥)”，然后单击“复制”。 

    ![从 Azure 门户复制函数 URL](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. 将函数 URL 粘贴到浏览器的地址栏中。 将查询字符串 `&name=<yourname>` 追加到该 URL，然后按键盘上的 `Enter` 键来执行请求。 下面是一个示例，说明了在 Edge 浏览器中由函数返回的响应：

    ![浏览器中的函数响应。](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    请求 URL 包含通过 HTTP 访问函数默认所需的密钥。   

3. 运行函数时，会在日志中写入跟踪信息。 要查看上次执行的跟踪输出，请返回到门户中的函数，并单击屏幕底部的向上箭头以展开“日志”。 

   ![Azure 门户中的“函数日志”查看器。](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

已使用简单的 HTTP 触发函数创建 Function App。  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

有关详细信息，请参阅 [Azure Functions HTTP 和 webhook 绑定](functions-bindings-http-webhook.md)。



