---
title: 服务到服务身份验证：使用 Azure Active Directory 将 REST API 与 Azure 数据湖存储 Gen1 |Microsoft Docs
description: 了解如何通过 REST API 使用 Azure Active Directory 进行 Azure Data Lake Storage Gen1 服务到服务身份验证
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c48f7d7608b2b70f4ae41e2af5792cff72bb0dd2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885717"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>使用 REST API 进行 Azure Data Lake Storage Gen1 服务到服务身份验证
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-service-to-service-authenticate-java.md)
> * [使用 .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-service-to-service-authenticate-python.md)
> * [使用 REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

本文介绍如何使用 REST API 执行 Azure Data Lake Storage Gen1 服务到服务身份验证。 若要了解使用 REST API 的 Data Lake Storage Gen1 最终用户身份验证，请参阅[使用 REST API 通过 Data Lake Storage Gen1 进行最终用户身份验证](data-lake-store-end-user-authenticate-rest-api.md)。

## <a name="prerequisites"></a>必备组件
* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **创建 Azure Active Directory“Web”应用程序**。 必须已完成[使用 Azure Active Directory 进行 Data Lake Storage Gen1 服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)中的步骤。

## <a name="service-to-service-authentication"></a>服务到服务身份验证
在此方案中，应用程序提供自身的凭据来执行操作。 为此，必须发出 POST 请求，如以下代码片段所示： 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

此请求的输出包含授权令牌（在以下输出中以 `access-token` 表示），随后要连同 REST API 调用传递该令牌。 在文本文件中保存身份验证令牌；对 Data Lake Storage Gen1 进行 REST 调用时，会需要此令牌。

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

本文使用 **非交互式** 方法。 有关非交互式（服务到服务的调用）的详细信息，请参阅 [Service to service calls using credentials](https://msdn.microsoft.com/library/azure/dn645543.aspx)（使用凭据执行服务到服务的调用）。 

## <a name="next-steps"></a>后续步骤
本文介绍了如何通过 REST API 使用服务到服务身份验证进行 Data Lake Storage Gen1 身份验证。 现可查看以下介绍如何使用 REST API 在 Data Lake Storage Gen1 中执行操作的文章。

* [使用 REST API 对 Data Lake Storage Gen1 进行的帐户管理操作](data-lake-store-get-started-rest-api.md)
* [使用 REST API 在 Data Lake Storage Gen1 中进行的数据操作](data-lake-store-data-operations-rest-api.md)

