---
title: 如何配置 Postman-Azure 数字孪生 |Microsoft Docs
description: 了解如何配置和使用 Postman 来测试 Azure 数字孪生 Api。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 377639d7a88478308709743ab842db71028686ed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023304"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>如何为 Azure 数字孪生配置 Postman

本文介绍如何配置 Postman REST 客户端以与 Azure 数字孪生管理 API 进行交互并对其进行测试。 具体而言，本文介绍以下内容：

* 如何将 Azure Active Directory 应用程序配置为使用 OAuth 2.0 隐式授权流。
* 如何使用 Postman REST 客户端向管理 API 发出令牌承载 HTTP 请求。
* 如何使用 Postman 向管理 API 发出多部分 POST 请求。

## <a name="postman-summary"></a>Postman 摘要

通过 REST 客户端工具（如 [Postman](https://www.getpostman.com/)）开始使用 Azure 数字孪生，以准备本地测试环境。 Postman 客户端有助于快速创建复杂的 HTTP 请求。 前往 [www.getpostman.com/apps](https://www.getpostman.com/apps) 下载 Postman 客户端的桌面版本。

[Postman](https://www.getpostman.com/) 是一种 REST 测试工具，可将关键 HTTP 请求功能定位到有用的桌面和基于插件的 GUI 中。

通过 Postman 客户端，解决方案开发人员可以指定 HTTP 请求的类型（POST、GET、UPDATE、PATCH 和 DELETE）、要调用的 API 终结点以及 SSL 的使用。 Postman 还支持添加 HTTP 请求标头、参数、表单数据和正文。

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>配置 Azure Active Directory 以使用 OAuth 2.0 隐式授权流

1. 按照[快速入门](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app)中的步骤创建和配置 Azure Active Directory 应用程序。 或者，你可以重复使用现有的应用注册。

    [![配置新 Postman 重定向 URI](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. 现在，将**重定向 URI**添加到 `https://www.getpostman.com/oauth2/callback`。

1. 选中 "**隐式授权** > **访问令牌**" 复选框，以允许使用 OAuth 2.0 隐式授权流。 选择 "**配置**"，然后单击 "**保存**"。

1. 复制 Azure Active Directory 应用的**客户端 ID** 。

## <a name="obtain-an-oauth-20-token"></a>获取 OAuth 2.0 令牌

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

设置并配置 Postman 以获取 Azure Active Directory 令牌。 之后，使用获取的令牌向 Azure 数字孪生发出经过身份验证的 HTTP 请求：

1. 验证“授权 URL”正确无误。 它应采用以下格式：

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | 名称  | 替换为 | 示例 |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | 租户或组织的名称。 使用友好名称，而不是 Azure Active Directory 应用注册的字母数字**租户 ID** 。 | `microsoft` |

1. 前往 [www.getpostman.com](https://www.getpostman.com/) 下载应用。

1. 我们想要发出 GET 请求。 选择 "**授权**" 选项卡，选择 "OAuth 2.0"，然后选择 "**获取新的访问令牌**"。

    | 字段  | 值 |
    |---------|---------|
    | 授权类型 | `Implicit` |
    | 回调 URL | `https://www.getpostman.com/oauth2/callback` |
    | 身份验证 URL | 使用**步骤 1**中的**授权 URL** |
    | 客户端 ID | 使用在上一节中创建或重新使用的 Azure Active Directory 应用的**应用程序 ID** |
    | 范围 | 留空 |
    | 状况 | 留空 |
    | 客户端身份验证 | `Send as Basic Auth header` |

1. 此时，客户端应如下所示：

    [![Postman 客户端令牌示例](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. 选择“请求令牌”。
  
1. 向下滚动并选择“使用令牌”。

## <a name="make-a-multipart-post-request"></a>发出多部分 POST 请求

完成上述步骤后，请将 Postman 配置为发出已经过身份验证的 HTTP 多部分 POST 请求：

1. 在 "**标头**" 选项卡下，添加值为 `multipart/mixed`的 HTTP 请求标头密钥**内容类型**。

   [![指定内容类型多部分/混合](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. 将非文本数据序列化为文件。 JSON 数据将另存为 JSON 文件。
1. 在 "**正文**" 选项卡下，选择 `form-data`。 
1. 通过分配**密钥**名称来添加每个文件，并选择 "`File`"。
1. 然后，通过“选择文件”按钮选择每个文件。

   [![Postman 客户端窗体正文示例](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Postman 客户端不要求多部分区块有手动分配的 **Content-Type** 或 **Content-Disposition**。
   > * 对于每个部分，不需要指定这些标头。
   > * 对于整个请求，必须选择 `multipart/mixed` 或其他相应的 **Content-Type**。

1. 最后，选择 "**发送**" 以提交多部分 HTTP POST 请求。 `200` 或 `201` 的状态代码指示请求成功。 相应的响应消息将出现在客户端界面中。

1. 通过调用 API 终结点来验证 HTTP POST 请求数据： 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>后续步骤

- 要了解有关数字孪生管理 API 以及如何使用它们的详细信息，请参阅[如何使用 Azure 数字孪生管理 API](how-to-navigate-apis.md)。

- 使用多部分请求[将 Blob 添加到 Azure 数字孪生的实体](./how-to-add-blobs.md)。

- 若要了解如何使用管理 API 进行身份验证，请阅读[使用 API 进行身份验证](./security-authenticating-apis.md)。
