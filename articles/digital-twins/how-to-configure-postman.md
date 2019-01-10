---
title: 如何为 Azure 数字孪生配置 Postman | Microsoft Docs
description: 如何为 Azure 数字孪生配置 Postman
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: adgera
ms.openlocfilehash: 92ff8cb732c7c10c525d8a8ec76180cb435bd466
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974988"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>如何为 Azure 数字孪生配置 Postman

本文介绍如何配置 Azure Active Directory (Azure AD) 应用程序以使用 OAuth 2.0 隐式授权流。 接着，还讨论了如何配置 Postman REST 客户端以向管理 API 发出令牌承载 HTTP 请求。

## <a name="postman-summary"></a>Postman 摘要

通过 REST 客户端工具（如 [Postman](https://www.getpostman.com/)）开始使用 Azure 数字孪生，以准备本地测试环境。 Postman 客户端有助于快速创建复杂的 HTTP 请求。 前往 [www.getpostman.com/apps](https://www.getpostman.com/apps) 下载 Postman 客户端的桌面版本。

[Postman](https://www.getpostman.com/) 是一种 REST 测试工具，可将关键 HTTP 请求功能定位到有用的桌面和基于插件的 GUI 中。 通过 Postman 客户端，解决方案开发人员可以指定 HTTP 请求的类型（POST、GET、UPDATE、PATCH 和 DELETE）、要调用的 API 终结点以及 SSL 的使用。 Postman 还支持添加 HTTP 请求标头、参数、表单数据和正文。

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>配置 Azure Active Directory 以使用 OAuth 2.0 隐式授权流

配置 Azure AD 应用以使用 OAuth 2.0 隐式授权流。

1. 遵循[此快速入门](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)中的步骤创建一个本机类型的 Azure AD 应用程序。 也可以重复使用现有的本机应用注册。

1. 在“所需的权限”下，选择“添加”，然后在“添加 API 访问权限”下输入“Azure 数字孪生”。 如果搜索没有找到该 API，请改为搜索“Azure 智能空间”。 然后，选择“授予权限”>“委托的权限”并选择“完成”。

    ![Azure AD 应用注册添加 api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. 单击“清单”以打开应用的应用程序清单。 将“oauth2AllowImplicitFlow”设置为 `true`。

      ![Azure AD 隐式流][1]

1. 将“回复 URL”配置为 [`https://www.getpostman.com/oauth2/callback`](https://www.getpostman.com/oauth2/callback)。

      ![Azure AD 回复 URL][2]

1. 复制并保留 Azure AD 应用的“应用程序 ID”。 下面将用到它。

## <a name="configure-the-postman-client"></a>配置 Postman 客户端

接下来，设置并配置 Postman 以获取 Azure AD 令牌。 之后，使用获取的令牌向 Azure 数字孪生发出经过身份验证的 HTTP 请求：

1. 前往 [www.getpostman.com]([https://www.getpostman.com/) 下载应用。
1. 确保“授权 URL”正确无误。 它应采用以下格式：

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | 名称  | 替换为 | 示例 |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | 租户或组织的名称 | `microsoft` |

1. 依次选择“授权”选项卡、“OAuth 2.0”、“获取新访问令牌”。

    | 字段  | 值 |
    |---------|---------|
    | 授权类型 | `Implicit` |
    | 回调 URL | `https://www.getpostman.com/oauth2/callback` |
    | 身份验证 URL | 使用上面步骤 2 中的“授权 URL” |
    | 客户端 ID | 使用上一部分创建或重新调整的 Azure AD 应用的“应用程序 ID” |
    | 范围 | 留空 |
    | 状态 | 留空 |
    | 客户端身份验证 | `Send as Basic Auth header` |

1. 客户端现在应如下所示：

   ![Postman 客户端示例][3]

1. 选择“请求令牌”。

    >[!NOTE]
    >如果收到错误消息“无法完成 OAuth 2”，请尝试以下操作：
    > * 关闭并重新打开 Postman，然后重试。
  
1. 向下滚动并选择“使用令牌”。

## <a name="next-steps"></a>后续步骤

若要了解如何使用管理 API 进行身份验证，请阅读[使用 API 进行身份验证](./security-authenticating-apis.md)。

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
