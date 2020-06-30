---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72e4318bea7245e440db4c7d95bf7f1f38bbe268
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095175"
---
### <a name="send-a-test-notification"></a>发送测试通知

1. 在 [Postman](https://www.postman.com/downloads/) 中打开一个新选项卡。

1. 将请求设置为“POST”，然后输入以下地址：

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. 如果选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分，请确保将请求标头配置为包含 apikey 值。

   | 密钥                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

   > [!NOTE]
   > 可以从 PushDemo 项目的 Config.local_secrets.cs 中找到此项 。

1. 为“正文”选择“原始”选项，接下来从格式选项列表中选择“JSON”，然后包含一些占位符 JSON 内容   ：

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. 选择“代码”按钮，该按钮位于窗口右上角的“保存”按钮下方 。 显示 HTML 请求时，请求应类似于以下示例（具体取决于是否包含 apikey 标头） ：

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. 在一个或两个目标平台（Android 和 iOS）上运行 PushDemo 应用程序  。

    > [!NOTE]
    > 如果你正在 Android 上进行测试，请确保未在“调试”模式中运行，或者如果通过运行应用程序部署了应用，请强制关闭该应用，然后从启动器重新启动 。

1. 在 PushDemo 应用中，点击“注册”按钮 。

1. 返回 [Postman](https://www.postman.com/downloads)，关闭“生成代码片段”窗口（如果尚未关闭），然后单击“发送”按钮  。

1. 验证是否在 [Postman](https://www.postman.com/downloads) 中收到“200 OK”响应，并验证该警报是否显示在应用中，表明“已收到 ActionA 操作”  。  

1. 关闭 PushDemo 应用，然后再次单击 [Postman](https://www.postman.com/downloads) 中的“发送”按钮  。

1. 验证是否在 [Postman](https://www.postman.com/downloads) 中再次收到“200 OK”响应 。 验证 PushDemo 应用的通知区域中是否显示带有正确消息的通知。

1. 点击通知，确认它会打开应用并显示“已收到 ActionA 操作”警报。

1. 返回 [Postman](https://www.postman.com/downloads)，修改之前的请求正文以发送无提示通知，指定“action”值为 action_b 而不是 action_a 。

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. 应用仍处于打开状态的情况下，单击 [Postman](https://www.postman.com/downloads) 中的“发送”按钮 。

1. 验证是否在 [Postman](https://www.postman.com/downloads) 中收到“200 OK”响应，并验证是否在应用中显示该警报，表明“已收到 ActionB 操作”，而不是“已收到 ActionA 操作”   。

1. 关闭 PushDemo 应用，然后再次单击 [Postman](https://www.postman.com/downloads) 中的“发送”按钮  。

1. 验证是否在 [Postman](https://www.postman.com/downloads) 中收到“200 OK”响应，并验证无提示通知是否未显示在通知区域中 。
