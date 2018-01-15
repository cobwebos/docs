---
title: "REST API：Azure Data Lake Store 上的帐户管理选项 | Microsoft Docs"
description: "使用 Azure Data Lake Store 和 WebHDFS REST API 在 Data Lake Store 中执行帐户管理操作"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 5fafde870a01a6ceb5e86f7b00b0ca11b748c68a
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>Azure Data Lake Store 上的帐户管理操作（使用 REST API）
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

本文介绍如何使用 REST API 在 Data Lake Store 上执行帐户管理操作。 帐户管理操作包括创建 Data Lake Store 帐户、删除 Data Lake Store 帐户，等等。若要了解如何使用 REST API 在 Data Lake Store 上执行文件系统操作，请参阅[在 Data Lake Store 上使用 REST API 进行的文件系统操作](data-lake-store-data-operations-rest-api.md)。

## <a name="prerequisites"></a>系统必备
* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **[cURL](http://curl.haxx.se/)**。 本文使用 cURL 演示如何对 Data Lake Store 帐户进行 REST API 调用。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何使用 Azure Active Directory 进行身份验证？
可以通过两种方法使用 Azure Active Directory 进行身份验证。

* 有关应用程序的最终用户身份验证（交互式），请参阅[使用 .NET SDK 通过 Data Lake Store 进行最终用户身份验证](data-lake-store-end-user-authenticate-rest-api.md)。
* 有关应用程序的服务到服务身份验证（非交互式），请参阅[使用 .NET SDK 通过 Data Lake Store 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-rest-api.md)。


## <a name="create-a-data-lake-store-account"></a>创建 Data Lake Store 帐户
此操作基于 [此处](https://msdn.microsoft.com/library/mt694078.aspx)定义的 REST API 调用。

使用以下 cURL 命令。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

在上述命令中，将 \<`REDACTED`\> 替换为前面检索的授权令牌。 此命令的请求有效负载包含在提供给上述 `-d` 参数的 **input.json** 文件中。 input.json 文件的内容如下代码片段所示：

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>删除 Data Lake Store 帐户
此操作基于 [此处](https://msdn.microsoft.com/library/mt694075.aspx)定义的 REST API 调用。

使用以下 cURL 命令删除 Data Lake Store 帐户。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

应该看到输出如以下代码片段所示：

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>后续步骤
* [使用 REST API 在 Data Lake Store 上进行的文件系统操作](data-lake-store-data-operations-rest-api.md)。

## <a name="see-also"></a>另请参阅
* [Azure Data Lake Store REST API 参考](https://docs.microsoft.com/rest/api/datalakestore/)
* [Open Source Big Data applications compatible with Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)

