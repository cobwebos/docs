---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095178"
---
#### <a name="no-response-from-the-backend-service"></a>后端服务没有响应

在本地进行测试时，请确保后端服务正在运行，并且正在使用正确的端口。

如果要对 Azure API 应用进行测试，请检查服务是否正在运行、是否已部署并且已正确启动。

通过客户端进行测试时，请确保在 [Postman](https://www.postman.com/downloads) 或移动应用配置中正确地指定了基址。 在本地进行测试时，基址应显示为 `https://<api_name>.azurewebsites.net/` 或 `https://localhost:5001/`。

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>开始或停止调试会话后，Android 上没有收到通知

请确保在启动或停止调试会话后重新注册。 调试器将导致生成新的 Firebase 令牌。 还必须更新通知中心安装。

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>从后端服务收到 401 状态代码

验证是否正在设置 apikey 请求标头，以及此值是否与你为后端服务配置的值相匹配。

如果在本地进行测试时收到此错误，请确保在客户端配置中定义的密钥值与 [API](#create-the-api-app) 使用的 Authentication:ApiKey 用户设置值相匹配。

如果要对 API 应用进行测试，请确保客户端配置文件中的密钥值与你在 [API 应用](#create-the-api-app)中使用的 Authentication:ApiKey 应用程序设置相匹配 。

> [!NOTE]
> 如果在部署后端服务后创建或更改了此设置，则必须重启该服务才能使其生效。

如果选择不完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分，请确保未将 Authorize 特性应用到 NotificationsController 类 。

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>从后端服务收到 404 状态代码

验证终结点和 HTTP 请求方法是否正确。 例如，终结点应显示为：

- **[PUT]** `https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[DELETE]** `https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[POST]** `https://<api_name>.azurewebsites.net/api/notifications/requests`

如果在本地进行测试，应为：

- **[PUT]** `https://localhost:5001/api/notifications/installations`
- **[DELETE]** `https://localhost:5001/api/notifications/installations/<installation_id>`
- **[POST]** `https://localhost:5001/api/notifications/requests`

在客户端应用中指定基址时，请确保它以 `/` 结尾。 在本地进行测试时，基址应显示为 `https://<api_name>.azurewebsites.net/` 或 `https://localhost:5001/`。

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>无法注册并显示通知中心错误消息

验证测试设备是否已连接到网络。 然后，通过设置断点来确定 Http 响应状态代码，以检查 HttpResponse 中的 StatusCode 属性值 。

根据状态代码，查看以前的故障排除建议（如果适用）。

在为相应的 API 返回这些特定状态代码的行上设置断点。 然后，在本地进行调试时，尝试调用后端服务。

使用适当的有效负载，通过 [Postman](https://www.postman.com/downloads) 验证后端服务是否按预期方式工作。 使用客户端代码为相关平台创建的实际有效负载。

查看特定于平台的配置部分，确保没有遗漏任何步骤。 检查是否为相应平台的 `installation id` 和 `token` 变量解析了合适的值。

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>无法解析显示的设备错误消息的 ID

查看特定于平台的配置部分，确保没有遗漏任何步骤。
