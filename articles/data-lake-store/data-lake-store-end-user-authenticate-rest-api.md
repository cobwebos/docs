---
title: 最终用户身份验证-REST 与 Data Lake Storage Gen1-Azure
description: 了解如何通过 REST API 使用 Azure Active Directory 进行 Azure Data Lake Storage Gen1 最终用户身份验证
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: ac06c9ef355eeba489d2006c435a48b7efcfd7f0
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82688067"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>通过 REST API 进行 Azure Data Lake Storage Gen1 最终用户身份验证
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [使用 .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-end-user-authenticate-python.md)
> * [使用 REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

本文介绍如何使用 REST API 执行 Azure Data Lake Storage Gen1 最终用户身份验证。 有关使用 REST API 的 Data Lake Storage Gen1 服务到服务身份验证，请参阅[使用 REST API 进行 Data Lake Storage Gen1 的服务到服务身份验证](data-lake-store-service-to-service-authenticate-rest-api.md)。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **创建 Azure Active Directory“本机”应用程序**。 必须已完成[使用 Azure Active Directory 进行 Data Lake Storage Gen1 最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)中的步骤。

* **[弯曲](https://curl.haxx.se/)**。 本文使用 cURL 演示如何对 Data Lake Storage Gen1 帐户进行 REST API 调用。

## <a name="end-user-authentication"></a>最终用户身份验证
如果希望用户使用 Azure AD 登录到应用程序，则最终用户身份验证是建议的方法。 应用程序可访问 Azure 资源，其访问权限级别与已登录用户相同。 用户需要定期提供凭据，以使应用程序可继续访问。

让最终用户登录的结果是会向应用程序授予访问令牌和刷新令牌。 访问令牌会附加到对 Data Lake Storage Gen1 或 Data Lake Analytics 作出的每个请求，默认情况下一小时内有效。 刷新令牌可用于获取新的访问令牌，并且如果定期使用，默认情况下两周内有效。 可使用两种不同的最终用户登录方式。

在此方案中，应用程序提示用户登录，所有操作在用户的上下文中执行。 执行以下步骤：

1. 通过应用程序将用户重定向至以下 URL：

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<REDIRECT-URI> 需要经过编码才能在 URL 中使用。 因此，对于 https://localhost，请使用 `https%3A%2F%2Flocalhost`）

    在本教程中，可以替换上述 URL 中的占位符值，并将此值粘贴到 Web 浏览器地址栏中。 随后用户将重定向，以便使用 Azure 登录名进行身份验证。 成功登录后，响应会显示在浏览器地址栏中。 响应格式如下：

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. 捕获响应中的授权代码。 在本教程中，可以从 Web 浏览器的地址栏复制授权代码，然后在 POST 请求中将其传递给令牌终结点，如以下代码片段所示：

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>

   > [!NOTE]
   > 在此情况下，\<REDIRECT-URI> 不需要编码。
   > 
   > 

3. 响应是一个 JSON 对象，包含访问令牌（例如 `"access_token": "<ACCESS_TOKEN>"`）和刷新令牌（例如 `"refresh_token": "<REFRESH_TOKEN>"`）。 应用程序在访问 Azure Data Lake Storage Gen1 时使用访问令牌，在访问令牌过期时使用刷新令牌获取另一个访问令牌。

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. 访问令牌过期时，可以使用刷新令牌请求新的访问令牌，如以下代码片段所示：

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

有关交互式用户身份验证的详细信息，请参阅 [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx)（授权代码授予流）。

## <a name="next-steps"></a>后续步骤
本文介绍了如何通过 REST API 使用服务到服务身份验证进行 Azure Data Lake Storage Gen1 身份验证。 现可查看以下介绍如何使用 REST API 在 Azure Data Lake Storage Gen1 中执行操作的文章。

* [使用 REST API Data Lake Storage Gen1 上的帐户管理操作](data-lake-store-get-started-rest-api.md)
* [使用 REST API 在 Data Lake Storage Gen1 中进行的数据操作](data-lake-store-data-operations-rest-api.md)

