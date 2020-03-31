---
title: 如何配置邮递员 - Azure 数字孪生 |微软文档
description: 了解如何配置和使用 Postman 来测试 Azure 数字孪生 API。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297160"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>如何为 Azure 数字孪生配置 Postman

本文介绍如何配置 Postman REST 客户端以与 Azure 数字孪生管理 API 进行交互并对其进行测试。 具体而言，本文介绍以下内容：

* 如何将 Azure Active Directory 应用程序配置为使用 OAuth 2.0 隐式授权流。
* 如何使用 Postman REST 客户端向管理 API 发出令牌承载 HTTP 请求。
* 如何使用 Postman 向管理 API 发出多部分 POST 请求。

## <a name="postman-summary"></a>Postman 摘要

通过 REST 客户端工具（如 [Postman](https://www.getpostman.com/)）开始使用 Azure 数字孪生，以准备本地测试环境。 Postman 客户端有助于快速创建复杂的 HTTP 请求。 前往 [www.getpostman.com/apps](https://www.getpostman.com/apps) 下载 Postman 客户端的桌面版本。

[Postman](https://www.getpostman.com/) 是一种 REST 测试工具，可将关键 HTTP 请求功能定位到有用的桌面和基于插件的 GUI 中。

通过 Postman 客户端，解决方案开发人员可以指定 HTTP 请求 *（POST、GET、**更新**、PATCH*和*GET**DELETE）、* 要调用的 API 终结点以及 TLS 的使用类型。 Postman 还支持添加 HTTP 请求标头、参数、表单数据和正文。

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>配置 Azure Active Directory 以使用 OAuth 2.0 隐式授权流

1. 按照["快速入门"](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app)中的步骤创建和配置 Azure 活动目录应用程序。 或者，您可以重用现有的应用注册。

    [![配置新的邮递员重定向 URI](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. 现在，将**重定向 URI**添加到`https://www.getpostman.com/oauth2/callback`。

1. 选择 **"隐式授予** > **访问令牌"** 复选框以允许使用 OAuth 2.0 隐式授予流。 选择 **"配置**"，然后**保存**。

1. 复制 Azure 活动目录应用的**客户端 ID。**

## <a name="obtain-an-oauth-20-token"></a>获取 OAuth 2.0 令牌

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

设置和配置 Postman 以获取 Azure 活动目录令牌。 之后，使用获取的令牌向 Azure 数字孪生发出经过身份验证的 HTTP 请求：

1. 验证“授权 URL”正确无误****。 它应采用以下格式：

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | “属性”  | 替换为 | 示例 |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | 租户或组织的名称。 使用人性化名称，而不是 Azure 活动目录应用注册的字母数字**租户 ID。** | `microsoft` |

1. 前往 [www.getpostman.com](https://www.getpostman.com/) 下载应用。

1. 我们想提出GET请求。 选择 **"授权"** 选项卡，选择 OAuth 2.0，然后选择 **"获取新访问令牌**"。

    | 字段  | “值” |
    |---------|---------|
    | 授权类型 | `Implicit` |
    | 回调 URL | `https://www.getpostman.com/oauth2/callback` |
    | 身份验证 URL | 使用**步骤 1**中的**授权 URL** |
    | 客户端 ID | 使用从上一节创建或重用的 Azure 活动目录应用**的应用程序 ID** |
    | 范围 | 留空 |
    | 状态 | 留空 |
    | 客户端身份验证 | `Send as Basic Auth header` |

1. 此时，客户端应如下所示：

    [![邮递员客户端令牌示例](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. 选择“请求令牌”。****
  
1. 向下滚动并选择“使用令牌”****。

## <a name="make-a-multipart-post-request"></a>发出多部分 POST 请求

完成上述步骤后，请将 Postman 配置为发出已经过身份验证的 HTTP 多部分 POST 请求：

1. 在 **"标题"** 选项卡下，添加具有值`multipart/mixed`的 HTTP 请求标头键**内容类型**。

   [![指定内容类型多部分/混合](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. 将非文本数据序列化为文件。 JSON 数据将另存为 JSON 文件。
1. 在"**正文"** 选项卡下`form-data`，选择 。 
1. 通过分配**密钥**名称来添加每个文件，选择`File`。
1. 然后，通过“选择文件”**** 按钮选择每个文件。

   [![邮递员客户端表单正文示例](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Postman 客户端不要求多部分区块有手动分配的 **Content-Type** 或 **Content-Disposition**。
   > * 对于每个部分，不需要指定这些标头。
   > * 对于整个请求，必须选择 `multipart/mixed` 或其他相应的 **Content-Type**。

1. 最后，选择 **"发送**"以提交多部分 HTTP POST 请求。 或`200``201`指示成功请求的状态代码。 相应的响应消息将显示在客户端接口中。

1. 通过调用 API 终结点验证 HTTP POST 请求数据： 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>后续步骤

- 要了解有关数字孪生管理 API 以及如何使用它们的详细信息，请参阅[如何使用 Azure 数字孪生管理 API](how-to-navigate-apis.md)。

- 使用多部分请求[将 Blob 添加到 Azure 数字孪生的实体](./how-to-add-blobs.md)。

- 若要了解如何使用管理 API 进行身份验证，请阅读[使用 API 进行身份验证](./security-authenticating-apis.md)。
