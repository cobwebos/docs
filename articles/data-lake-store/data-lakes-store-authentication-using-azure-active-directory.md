---
title: 使用 Azure Active Directory 在 Data Lake Store 中进行身份验证 | Microsoft Docs
description: 了解如何使用 Azure Active Directory 进行 Data Lake Store 身份验证
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: bee65fbdc65807ac33ae425ed9d87dbf0c246d9d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625281"
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>使用 Azure Active Directory 进行 Data Lake Store 身份验证

Azure Data Lake Store 使用 Azure Active Directory 进行身份验证。 编写用于 Azure Data Lake Store 的应用程序之前，必须首先决定使用 Azure Active Directory (Azure AD) 对应用程序进行身份验证的方式。

## <a name="authentication-options"></a>身份验证选项

* **最终用户身份验证** - 使用最终用户的 Azure 凭据进行 Data Lake Store 身份验证。 你创建的与 Data Lake Store 一起使用的应用程序会提示输入这些用户凭据。 因此，此身份验证机制是“交互式的”，并且应用程序在已登录用户的上下文中运行。 有关详细信息和说明，请参阅 [Data Lake Store 的最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)。

* **服务到服务身份验证** - 如果希望应用程序向 Data Lake Store 证明自己的身份，请使用此选项。 在这种情况下，你创建 Azure Active Directory (AD) 应用程序，并使用来自 Azure AD 应用程序的密钥进行 Data Lake Store 身份验证。 因此，此身份验证机制是“非交互式的”。 有关详细信息和说明，请参阅 [Data Lake Store 的服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)。

下表说明了 Data Lake Store 对最终用户身份验证机制和服务到服务身份验证机制的支持情况。 下面说明了如何解读此表。

* ✔* 符号表示此身份验证选项受支持并且链接到演示如何使用此身份验证选项的文章。 
* ✔ 符号表示此身份验证选项受支持。 
* 空单元格表示此身份验证选项不受支持。


|将此身份验证选项用于...                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|最终用户（不使用 MFA**）                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**（已弃用）     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|最终用户（使用 MFA）                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|服务到服务（使用客户端密钥）         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|服务到服务（使用客户端证书） |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* 单击 <b>✔\*</b> 符号。它是一个链接。</i><br>
<i>** MFA 代表多重身份验证</i>

有关如何使用 Azure Active Directory 进行身份验证的详细信息，请参阅 [Authentication Scenarios for Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md)（Azure Active Directory 的身份验证方案）。

## <a name="next-steps"></a>后续步骤

* [最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)
* [服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)


