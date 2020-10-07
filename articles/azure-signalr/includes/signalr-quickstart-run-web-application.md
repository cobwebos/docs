---
title: include 文件
description: include 文件
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 2af9c1dfd13d3aeafa7cf1ac76537117ecc15aff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "84317485"
---
## <a name="run-the-web-application"></a>运行 Web 应用程序

1. 若要简化客户端测试，请打开浏览器进入示例单页 Web 应用程序 [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)。 

    > [!NOTE]
    > HTML 文件的源位于 [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html)。 如果你想要自行托管 HTML，请在 /docs/demo/chat-v2 目录中启动本地 HTTP 服务器，如 [http-server](https://www.npmjs.com/package/http-server)。 请确保将原点添加到 local.settings.json 中与示例类似的 `CORS` 设置。
    > 
    > ```javascript
    > "Host": {
    >  "LocalHttpPort": 7071,
    >  "CORS": "http://localhost:8080,https://azure-samples.github.io",
    >  "CORSCredentials": true
    > }
    >
    > ```

1. 系统提示输入函数应用的基 URL 时，输入 `http://localhost:7071`。

1. 出现提示时输入用户名。

1. Web 应用程序调用函数应用中的 *GetSignalRInfo* 函数来检索连接信息以连接到 Azure SignalR 服务。 连接完成后，将显示聊天消息输入框。

1. 键入消息，然后按 Enter。 应用程序将该消息发送到 Azure Function 应用中的 *SendMessage* 函数，然后后者使用 SignalR 输出绑定将该消息广播到所有连接的客户端。 如果一切正常工作，该消息应该出现在应用程序中。

    ![运行应用程序](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. 在另一个浏览器窗口中打开另一个 Web 应用程序实例。 你将看到发送的任何消息都将出现在应用程序的所有实例中。
