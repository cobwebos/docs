---
title: 身份验证-Data Lake Storage Gen1 与 Azure AD
description: 了解如何使用 Azure Active Directory 对 Azure Data Lake Storage Gen1 进行身份验证。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 49e6df417190071e06582be400575e1880f2543a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692290"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>使用 Azure Active Directory 进行 Azure Data Lake Storage Gen1 身份验证

Azure Data Lake Storage Gen1 使用 Azure Active Directory 进行身份验证。 编写用于 Data Lake Storage Gen1 的应用程序之前，必须先决定使用 Azure Active Directory (Azure AD) 对应用程序进行身份验证的方式。

## <a name="authentication-options"></a>身份验证选项

* **最终用户身份验证** - 使用最终用户的 Azure 凭据进行 Data Lake Storage Gen1 身份验证。 你创建的与 Data Lake Storage Gen1 一起使用的应用程序会提示输入这些用户凭据。 因此，此身份验证机制是“交互式的”，并且应用程序在已登录用户的上下文中运行。** 有关详细信息和说明，请参阅 [Data Lake Storage Gen1 的最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)。

* **服务到服务身份验证** - 如果希望应用程序向 Data Lake Storage Gen1 证明自己的身份，请使用此选项。 在这种情况下，你创建 Azure Active Directory (AD) 应用程序，并使用来自 Azure AD 应用程序的密钥进行 Data Lake Storage Gen1 身份验证。 因此，此身份验证机制是“非交互式的”。** 有关详细信息和说明，请参阅 [Data Lake Storage Gen1 的服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)。

下表说明了 Data Lake Storage Gen1 对最终用户身份验证机制和服务到服务身份验证机制的支持情况。 下面说明了如何解读此表。

* ✔* 符号表示此身份验证选项受支持并且链接到演示如何使用此身份验证选项的文章。 
* ✔ 符号表示此身份验证选项受支持。 
* 空单元格表示此身份验证选项不受支持。


|将此身份验证选项用于...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|最终用户（不使用 MFA\*\*）                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**（不推荐使用）     |    **[✔ *](data-lake-store-end-user-authenticate-rest-api.md)**    |
|最终用户（使用 MFA）                           |    **[✔ *](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔ *](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔ *](data-lake-store-get-started-cli-2.0.md)**      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|服务到服务（使用客户端密钥）         |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔ *](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔ *](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔ *](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|服务到服务（使用客户端证书） |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* 单击<b>✔\* </b>符号。这是一个链接。</i><br>
<i>** MFA 代表多重身份验证</i>

有关如何使用 Azure Active Directory 进行身份验证的详细信息，请参阅 [Authentication Scenarios for Azure Active Directory](../active-directory/develop/authentication-scenarios.md)（Azure Active Directory 的身份验证方案）。

## <a name="next-steps"></a>后续步骤

* [最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)
* [服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)


