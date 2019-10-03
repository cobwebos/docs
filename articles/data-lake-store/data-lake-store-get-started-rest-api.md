---
title: REST API：帐户管理操作在 Azure 数据湖存储 Gen1 |Microsoft Docs
description: 使用 Azure Data Lake Storage Gen1 和 WebHDFS REST API 在 Data Lake Storage Gen1 中执行帐户管理操作
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60877094"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>使用 REST API 在 Azure Data Lake Storage Gen1 上执行的帐户管理操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

本文介绍如何使用 REST API 在 Azure Data Lake Storage Gen1 上执行帐户管理操作。 帐户管理操作包括创建 Data Lake Storage Gen1 帐户、删除 Data Lake Storage Gen1 帐户，等等。若要了解如何使用 REST API 在 Data Lake Storage Gen1 上执行文件系统操作，请参阅[在 Data Lake Storage Gen1 上使用 REST API 执行的文件系统操作](data-lake-store-data-operations-rest-api.md)。

## <a name="prerequisites"></a>必备组件
* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **[cURL](https://curl.haxx.se/)** 。 本文使用 cURL 演示如何对 Data Lake Storage Gen1 帐户进行 REST API 调用。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何使用 Azure Active Directory 进行身份验证？
可以通过两种方法使用 Azure Active Directory 进行身份验证。

* 若要了解应用程序的最终用户身份验证（交互式），请参阅[使用 .NET SDK 通过 Data Lake Storage Gen1 进行最终用户身份验证](data-lake-store-end-user-authenticate-rest-api.md)。
* 若要了解应用程序的服务到服务身份验证（非交互式），请参阅[使用 .NET SDK 通过 Data Lake Storage Gen1 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-rest-api.md)。


## <a name="create-a-data-lake-storage-gen1-account"></a>创建 Data Lake Storage Gen1 帐户
此操作基于 [此处](https://docs.microsoft.com/rest/api/datalakestore/accounts/create)定义的 REST API 调用。

使用以下 cURL 命令。 将 **\<yourstoragegen1name>** 替换为自己的 Data Lake Storage Gen1 帐户名。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

在上述命令中，将 \<`REDACTED`\> 替换为前面检索的授权令牌。 此命令的请求有效负载包含在提供给上述 `-d` 参数的 **input.json** 文件中。 input.json 文件的内容如下代码片段所示：

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>删除 Data Lake Storage Gen1 帐户
此操作基于 [此处](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete)定义的 REST API 调用。

使用以下 cURL 命令删除 Data Lake Storage Gen1 帐户。 将 **\<yourstoragegen1name>** 替换为自己的 Data Lake Storage Gen1 帐户名称。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

应该看到输出如以下代码片段所示：

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>后续步骤
* [使用 REST API 对 Data Lake Storage Gen1 执行的文件系统操作](data-lake-store-data-operations-rest-api.md)。

## <a name="see-also"></a>另请参阅
* [Azure Data Lake Storage Gen1 REST API 参考](https://docs.microsoft.com/rest/api/datalakestore/)
* [与 Azure Data Lake Storage Gen1 兼容的开源大数据应用程序](data-lake-store-compatible-oss-other-applications.md)

