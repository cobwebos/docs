---
title: Azure 中的 Postman FHIR 服务器 - Azure API for FHIR
description: 在本教程中，我们将逐步介绍使用 Postman 访问 FHIR 服务器所需的步骤。 Postman 有助于调试用于访问 API 的应用程序。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: mihansen
author: hansenms
ms.date: 02/07/2019
ms.openlocfilehash: 6e0851a55673792adc905d27fdd3f5c13d572032
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87563953"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>使用 Postman 访问 Azure API for FHIR

客户端应用程序可通过 [REST API](https://www.hl7.org/fhir/http.html) 访问 FHIR API。 在生成应用程序时，出于调试等目的，你可能还需要与 FHIR 服务器直接交互。 在本教程中，我们将逐步介绍使用 [Postman](https://www.getpostman.com/) 访问 FHIR 服务器所需的步骤。 在生成用于访问 API 的应用程序时，Postman 是常用于调试的工具。

## <a name="prerequisites"></a>先决条件

- Azure 中存在 FHIR 终结点。 可使用托管的 Azure API for FHIR 或 Azure 的开放源代码 FHIR 服务器设置该终结点。 使用 [Azure 门户](fhir-paas-portal-quickstart.md)、[PowerShell](fhir-paas-powershell-quickstart.md) 或 [Azure CLI](fhir-paas-cli-quickstart.md) 设置托管的 Azure API for FHIR。
- 要用于访问 FHIR 服务的[客户端应用程序](register-confidential-azure-ad-client-app.md)
- 已安装 Postman。 可以从 [https://www.getpostman.com](https://www.getpostman.com) 获取它。

## <a name="fhir-server-and-authentication-details"></a>FHIR 服务器和身份验证详细信息

为了使用 Postman，需要以下详细信息：

- FHIR 服务器 URL，例如 `https://MYACCOUNT.azurehealthcareapis.com`
- FHIR 服务器的标识提供者 `Authority`，例如 `https://login.microsoftonline.com/{TENANT-ID}`
- 已配置的 `audience`。 这通常是 FHIR 服务器的 URL，例如 `https://MYACCOUNT.azurehealthcareapis.com` 或仅 `https://azurehealthcareapis.com`。
- 要用于访问 FHIR 服务的[客户端应用程序](register-confidential-azure-ad-client-app.md)的 `client_id`（或应用程序 ID）。
- 客户端应用程序的 `client_secret`（或应用程序机密）。

最后，应检查 `https://www.getpostman.com/oauth2/callback` 是否是客户端应用程序的已注册回复 URL。

## <a name="connect-to-fhir-server"></a>连接到 FHIR 服务器

使用 Postman，对 `https://fhir-server-url/metadata` 执行 `GET` 请求：

![Postman 元数据功能语句](media/tutorial-postman/postman-metadata.png)

用于 Azure API for FHIR 的元数据 URL 是 `https://MYACCOUNT.azurehealthcareapis.com/metadata`。 在此示例中，FHIR 服务器 URL 是 `https://fhirdocsmsft.azurewebsites.net`，服务器的功能语句在 `https://fhirdocsmsft.azurewebsites.net/metadata` 中提供。 无需身份验证即可访问该终结点。

如果你尝试访问受限资源，则应会收到“身份验证失败”响应：

![身份验证失败](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>获取访问令牌

若要获取有效的访问令牌，请选择“授权”，并选择类型“OAuth 2.0”：

![设置 OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

点击“获取新的访问令牌”，随即显示一个对话框：

![请求新的访问令牌](media/tutorial-postman/postman-request-token.png)

你将需要一些详细信息：

| 字段                 | 示例值                                                                                                   | 注释                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| 令牌名称            | MYTOKEN                                                                                                         | 选择的名称          |
| 授权类型            | 授权代码                                                                                              |                            |
| 回调 URL          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| 身份验证 URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` 是 Azure API for FHIR 的 `https://MYACCOUNT.azurehealthcareapis.com` |
| 访问令牌 URL      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| 客户端 ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | 应用程序 ID             |
| 客户端机密         | `XXXXXXXX`                                                                                                        | 机密客户端密钥          |
| 范围 | `<Leave Blank>` |
| 状态                 | `1234`                                                                                                            |                            |
| 客户端身份验证 | 在正文中发送客户端凭据                                                                                 |                 

点击“请求令牌”，系统将指导你完成 Azure Active Directory 身份验证流，并且令牌将返回到 Postman。 如果遇到问题，请通过“查看”->“显示 Postman 控制台”菜单项打开 Postman 控制台。

在返回的令牌屏幕上向下滚动，并点击“使用令牌”：

![使用令牌](media/tutorial-postman/postman-use-token.png)

现在“访问令牌”字段中应已填充令牌，可从“可用令牌”中选择令牌。 如果你再次点击“发送”重复 `Patient` 资源搜索，则应会获取状态 `200 OK`：

![200 正常](media/tutorial-postman/postman-200-OK.png)

在本例中，数据库中没有任何患者，搜索结果为空。

如果使用 [https://jwt.ms](https://jwt.ms) 等工具检查访问令牌，应会看到如下内容：

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

在进行故障排除时，最好首先验证你是否拥有正确的受众（`aud` 声明）。 如果令牌来自正确的颁发者（`iss` 声明）并且你拥有正确的受众（`aud` 声明），但仍无法访问 FHIR API，则用户或服务主体（`oid` 声明）可能无权访问 FHIR 数据平面。 建议[使用 Azure 基于角色的访问控制 (Azure RBAC)](configure-azure-rbac.md)，向用户分配数据平面角色。 如果针对数据平面使用外部的辅助 Azure Active Directory 租户，则需要 [配置本地 RBAC 分配](configure-local-rbac.md)。

此外，还可以[使用 Azure CLI 获取用于 Azure API for FHIR 的令牌](get-healthcare-apis-access-token-cli.md)。 如果使用通过 Azure CLI 获取的令牌，则应使用授权类型“持有者令牌”，并直接将令牌粘贴到其中。

## <a name="inserting-a-patient"></a>插入患者

你现在已拥有一个有效的访问令牌。 可以插入新患者。 切换到方法“POST”并在请求正文中添加以下 JSON 文档：

[!code-json[](samples/sample-patient.json)]

点击“发送”，你应可看到患者已成功创建：

![已创建患者](media/tutorial-postman/postman-patient-created.png)

如果重复患者搜索，现在应会看到患者记录：

![已创建患者](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你使用 Postman 访问了一个 FHIR API。 在支持的功能部分了解支持的 API 功能。
 
>[!div class="nextstepaction"]
>[受支持的功能](fhir-features-supported.md)
