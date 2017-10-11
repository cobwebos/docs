---
title: "数据湖分析使用 REST API 入门 |Microsoft 文档"
description: "使用 WebHDFS REST Api 执行对数据湖分析操作"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 5e133d92-baaa-44c9-890c-ab2d85c91122
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: jgao
ms.openlocfilehash: 332d7af2539eea8890745005104ac5b0921c2b7f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>要开始使用 Azure 数据湖分析使用 REST Api
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 WebHDFS REST Api 和数据湖分析 REST Api 来管理数据湖分析帐户、 作业和目录。 

## <a name="prerequisites"></a>必要條件
* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **创建 Azure Active Directory 应用程序**。 使用 Azure AD 应用程序进行身份验证数据湖分析应用程序与 Azure AD。 有不同的方法来使用 Azure AD 进行身份验证作为**最终用户身份验证**或**服务到服务身份验证**。 有关说明以及如何进行身份验证的详细信息，请参阅[与数据湖分析使用 Azure Active Directory 进行身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。
* [cURL](http://curl.haxx.se/)。 本文章将使用 cURL 演示如何执行针对数据湖分析帐户的 REST API 调用。

## <a name="authenticate-with-azure-active-directory"></a>使用 Azure Active Directory 进行身份验证
有两种方法来使用 Azure Active Directory 进行身份验证。

### <a name="end-user-authentication-interactive"></a>最终用户身份验证 （交互）
使用此方法，应用程序将提示用户登录和用户的上下文中执行所有的操作。 

请按照下列步骤进行交互式身份验证：

1. 通过应用程序，将用户重定向到以下 URL:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<重定向 URI > 需要 url 编码。 因此，对于 https://localhost，使用`https%3A%2F%2Flocalhost`)
   > 
   > 
   
    就本教程来说，你可以替换上述 URL 中的占位符值，并将其粘贴在 web 浏览器的地址栏中。 你将重定向以使用你的 Azure 登录名进行身份验证。 你成功登录后，响应将显示在浏览器的地址栏中。 响应将采用以下格式：
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. 捕获响应中的授权代码。 对于本教程，你可以从 web 浏览器的地址栏复制授权代码，并将其传递为 POST 请求中到令牌终结点，如下所示：
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > 在这种情况下，\<重定向 URI > 需要对其进行编码。
   > 
   > 
3. 响应是一个包含访问令牌的 JSON 对象 (例如， `"access_token": "<ACCESS_TOKEN>"`) 和刷新令牌 (例如， `"refresh_token": "<REFRESH_TOKEN>"`)。 你的应用程序使用访问令牌访问 Azure 数据湖存储和刷新令牌时获取另一个访问令牌时访问令牌过期。
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. 当访问令牌到期时，你可以请求新的访问令牌使用刷新令牌，如下所示：
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

交互式用户身份验证的详细信息，请参阅[授权代码授予流](https://msdn.microsoft.com/library/azure/dn645542.aspx)。

### <a name="service-to-service-authentication-non-interactive"></a>服务到服务身份验证 （非交互）
使用此方法，应用程序提供其自己的凭据来执行操作。 为此，你必须发出 POST 请求类似如下所示： 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

此请求的输出将包含的授权令牌 (由表示`access-token`在下面的输出)，随后将会传递与你的 REST API 调用。 保存在文本文件中; 此身份验证令牌您将需要它在本文后面。

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

本文章将使用**非交互式**方法。 非交互式 （服务间调用） 的详细信息，请参阅[到服务的服务调用使用凭据](https://msdn.microsoft.com/library/azure/dn645543.aspx)。

## <a name="create-a-data-lake-analytics-account"></a>创建数据湖分析帐户
你可以创建数据湖分析帐户之前，必须创建 Azure 资源组和 Data Lake 存储帐户。  请参阅[创建 Data Lake 存储帐户](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account)。

以下 Curl 命令演示如何创建一个帐户：

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

替换\< `REDACTED` \>与授权令牌， \< `AzureSubscriptionID` \>替换为订阅 ID， \< `AzureResourceGroupName` \>与一个现有的 Azure 资源组名称，并\< `NewAzureDataLakeAnalyticsAccountName` \>具有新的数据湖分析帐户名称。 中包含此命令请求负载**CreateDatalakeAnalyticsAccountRequest.json**为提供的文件`-d`上面的参数。 Input.json 文件的内容如下所示：

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>列表数据湖分析帐户订阅中
以下 Curl 命令显示如何列出订阅中的帐户：

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

替换\< `REDACTED` \>与授权令牌， \< `AzureSubscriptionID` \>替换为订阅 id。 输出为类似于：

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>获取有关数据湖分析帐户的信息
以下 Curl 命令演示如何获取帐户信息：

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

替换\< `REDACTED` \>与授权令牌， \< `AzureSubscriptionID` \>替换为订阅 ID， \< `AzureResourceGroupName` \>与一个现有的 Azure 资源组名称，并\< `DataLakeAnalyticsAccountName` \>替换现有数据湖分析帐户的名称。 输出为类似于：

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>列表数据湖存储的数据湖分析帐户
以下 Curl 命令显示如何列出的帐户的数据湖存储区：

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

替换\< `REDACTED` \>与授权令牌， \< `AzureSubscriptionID` \>替换为订阅 ID， \< `AzureResourceGroupName` \>与一个现有的 Azure 资源组名称，并\< `DataLakeAnalyticsAccountName` \>替换现有数据湖分析帐户的名称。 输出为类似于：

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>提交 U SQL 作业
下面的 Curl 命令演示如何将提交 U SQL 作业：

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

替换\< `REDACTED` \>与授权令牌， \< `DataLakeAnalyticsAccountName` \>替换现有数据湖分析帐户的名称。 中包含此命令请求负载**SubmitADLAJob.json**为提供的文件`-d`上面的参数。 Input.json 文件的内容如下所示：

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

输出为类似于：

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>列出 U SQL 作业
以下 Curl 命令显示如何列出 U SQL 作业：

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

替换\< `REDACTED` \>与授权令牌，和\< `DataLakeAnalyticsAccountName` \>替换现有数据湖分析帐户的名称。 

输出为类似于：

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>获取目录项
以下 Curl 命令演示如何从目录中获取的数据库：

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

输出为类似于：

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>另请参阅
* 若要查看更复杂的查询，请参阅[使用 Azure 数据湖分析分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要开始开发 U SQL 应用程序，请参阅[使用 Data Lake Tools for Visual Studio 的开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，请参阅[开始使用 Azure 数据湖分析 U-SQL 语言](data-lake-analytics-u-sql-get-started.md)。
* 有关管理任务，请参阅[管理 Azure 数据湖分析使用 Azure 门户](data-lake-analytics-manage-use-portal.md)。
* 若要获取的数据湖分析概述，请参阅[Azure 数据湖分析概述](data-lake-analytics-overview.md)。
* 若要查看使用其他工具的同一教程，请单击页面顶部的选项卡上选择器。

