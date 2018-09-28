---
title: include 文件
description: include 文件
services: signalr
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 133fa1063a08303bfe4b4a973801b6bc22766ac3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006196"
---
## <a name="run-the-web-application"></a>运行 Web 应用程序

1. 为方便起见，GitHub 中有一个示例单页 Web 应用程序。 将浏览器打开至 [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/)。

    > [!NOTE]
    > HTML 文件的源位于 [/docs/demo/chat/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat/index.html)。

1. 系统提示输入函数应用的基 URL 时，输入 *http://localhost:7071*。

1. 出现提示时输入用户名。

1. Web 应用程序调用函数应用中的 *GetSignalRInfo* 函数来检索连接信息以连接到 Azure SignalR 服务。 连接完成后，将显示聊天消息输入框。

1. 键入消息，然后按 Enter。 应用程序将该消息发送到 Azure Function 应用中的 *SendMessage* 函数，然后后者使用 SignalR 输出绑定将该消息广播到所有连接的客户端。 如果一切正常工作，该消息应该出现在应用程序中。

    ![运行应用程序](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. 在另一个浏览器窗口中打开另一个 Web 应用程序实例。 你将看到发送的任何消息都将出现在应用程序的所有实例中。