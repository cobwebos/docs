---
title: 使用 U-SQL 脚本转换数据 - Azure | Microsoft Docs
description: 了解如何通过在 Azure Data Lake Analytics 计算服务上运行 U-SQL 脚本来处理或转换数据。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 534fbeaa8ba3c27c8d3f3bbcc59717d8bdb5c654
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050312"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>通过在 Azure Data Lake Analytics 上运行 U-SQL 脚本来转换数据 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](data-factory-usql-activity.md)
> * [版本 2（当前版本）](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本数据工厂服务，请参阅[V2 中的 U-SQL 活动](../transform-data-using-data-lake-analytics.md)。

Azure 数据工厂中的管道通过使用链接计算服务来处理链接存储服务中的数据。 它包含一系列活动，其中每个活动执行特定的处理操作。 本文介绍在 **Azure Data Lake Analytics** 计算链接服务上运行 **U-SQL** 脚本的 **Data Lake Analytics U-SQL 活动**。 

在使用 Data Lake Analytics U-SQL 活动创建管道之前，先创建 Azure Data Lake Analytics 帐户。 若要了解 Azure Data Lake Analytics，请参阅 [Azure Data Lake Analytics 入门](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)。

查看[生成首个管道教程](data-factory-build-your-first-pipeline.md)获取详细步骤，以便创建数据工厂、链接的服务、数据集和管道。 通过数据工厂编辑器或 Visual Studio 或 Azure PowerShell 使用 JSON 代码段创建数据工厂实体。

## <a name="supported-authentication-types"></a>支持的身份验证类型
U-SQL 活动支持对 Data Lake Analytics 进行以下类型的身份验证：
* 服务主体身份验证
* 用户凭据 (OAuth) 身份验证 

建议使用服务主体身份验证，特别是对于已计划的 U-SQL 执行。 使用用户凭据身份验证时可能会出现令牌过期行为。 有关配置详细信息，请参阅[链接服务属性](#azure-data-lake-analytics-linked-service)部分。

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 链接服务
创建 **Azure Data Lake Analytics** 链接服务将 Azure Data Lake Analytics 计算服务链接到 Azure 数据工厂。 管道中的 Data Lake Analytics U-SQL 活动是指此链接服务。 

下表介绍了 JSON 定义中使用的一般属性。 可以进一步选择服务主体身份验证，还是用户凭据身份验证。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为 **AzureDataLakeAnalytics**。 |是 |
| **accountName** |Azure Data Lake Analytics 帐户名。 |是 |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics URI。 |否 |
| **subscriptionId** |Azure 订阅 ID |否（如果未指定，则使用数据工厂的订阅）。 |
| **resourceGroupName** |Azure 资源组名称 |否（如果未指定，则使用数据工厂的资源组）。 |

### <a name="service-principal-authentication-recommended"></a>服务主体身份验证（推荐）
若要使用服务主体身份验证，请在 Azure Active Directory (Azure AD) 中注册一个应用程序实体并授予其访问 Data Lake Store 的权限。 有关详细步骤，请参阅[服务到服务身份验证](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：
* 应用程序 ID
* 应用程序密钥 
* 租户 ID

通过指定以下属性使用服务主体身份验证：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| **servicePrincipalId** | 指定应用程序的客户端 ID。 | 是 |
| **servicePrincipalKey** | 指定应用程序的密钥。 | 是 |
| **tenant** | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |

**示例：服务主体身份验证**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>用户凭据身份验证
也可以指定下列属性，对 Data Lake Analytics 使用用户凭据身份验证：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| **authorization** | 单击数据工厂编辑器中的“授权”按钮，并输入凭据以会自动生成的授权 URL 分配给此属性。 | 是 |
| **sessionId** | OAuth 授权会话中的 OAuth 会话 ID。 每个会话 ID 都是唯一的，并且只能使用一次。 使用数据工厂编辑器时会自动生成此设置。 | 是 |

**示例：用户凭据身份验证**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>令牌过期
使用“授权”按钮生成的授权代码在一段时间后便会过期。 请参阅下表，了解不同类型用户帐户的过期时间。 身份验证**令牌过期**时可能会看到以下错误消息：凭据操作错误: invalid_grant-AADSTS70002: 验证凭据时出错。 AADSTS70008：提供的访问权限已过期或已被吊销。 跟踪 ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相关 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 时间戳: 2015-12-15 21:09:31Z

| 用户类型 | 过期时间 |
|:--- |:--- |
| 不由 Azure Active Directory 管理的用户帐户（@hotmail.com、@live.com，等等） |12 小时 |
| 由 Azure Active Directory (AAD) 管理的用户帐户 |最后一次运行切片后的 14 天。 <br/><br/>如果以基于 OAuth 的链接服务为基础的切片每 14 天至少运行一次，则为 90 天。 |

若要避免/解决此错误，**令牌过期**时，使用“授权”按钮重新授权，并重新部署链接服务。 还可以使用下列代码，以编程方式生成 sessionId 和 authorization 属性值：

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

请参阅 [AzureDataLakeStoreLinkedService 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、[AzureDataLakeAnalyticsLinkedService 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)和 [AuthorizationSessionGetResponse 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)主题，了解关于代码中使用的数据工厂类的详细信息。 针对 WindowsFormsWebAuthenticationDialog 类向 Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll 添加引用。 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 活动
以下 JSON 代码段定义了具有 Data Lake Analytics U-SQL 活动的管道。 活动定义具有对之前创建的 Azure Data Lake Analytics 链接服务的引用。   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

下表描述了此活动特有的属性的名称和描述。 

| 属性            | 说明                              | 必选                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | type 属性必须设置为 **DataLakeAnalyticsU SQL**。 | 是                                      |
| linkedServiceName   | 引用在数据工厂中注册为链接服务的 Azure Data Lake Analytics | 是                                      |
| scriptPath          | 包含 U-SQL 脚本的文件夹路径。 文件的名称区分大小写。 | 否（如果使用脚本）                   |
| scriptLinkedService | 将包含脚本的存储链接到数据工厂的链接服务 | 否（如果使用脚本）                   |
| 脚本              | 指定内联脚本，而不是指定 scriptPath 和 scriptLinkedService。 例如： `"script": "CREATE DATABASE test"`。 | 否（如果使用 scriptPath 和 scriptLinkedService） |
| degreeOfParallelism | 同时用于运行作业的最大节点数。 | 否                                       |
| priority            | 确定应在所有排队的作业中选择哪些作业首先运行。 编号越低，优先级越高。 | 否                                       |
| parameters          | U-SQL 脚本的参数          | 否                                       |
| runtimeVersion      | 要使用的 U-SQL 引擎的运行时版本 | 否                                       |
| compilationMode     | <p>U-SQL 编译模式。 必须是这些值之一：</p> <ul><li>Semantic：只执行语义检查和必要的健全性检查。</li><li>Full：执行完整编译，包括语法检查、优化、代码生成等。</li><li>SingleBox：执行完整编译，且 TargetType 设置为 SingleBox。</li></ul><p>如果该属性未指定值，则服务器将确定最佳编译模式。 </p> | 否                                       |

请参阅 [SearchLogProcessing.txt 脚本定义](#sample-u-sql-script)了解有关脚本定义的信息。 

## <a name="sample-input-and-output-datasets"></a>输入和输出数据集示例
### <a name="input-dataset"></a>输入数据集
在此示例中，输入数据驻留在 Azure Data Lake Store 中（datalake/input 文件夹中的 SearchLog.tsv 文件）。 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>输出数据集
在此示例中，U-SQL 脚本生成的输出数据存储在 Azure Data Lake Store 中（datalake/output 文件夹）。 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Data Lake Store 链接服务示例
下面是输入/输出数据集使用的示例 Azure Data Lake Store 链接服务的定义。 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

请参阅[将数据从 Azure Data Lake Store 移入和移出](data-factory-azure-datalake-connector.md)一文了解 JSON 属性的说明。 

## <a name="sample-u-sql-script"></a>示例 U-SQL 脚本

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

ADF 使用“parameters”部分动态传递 U-SQL 脚本中 **@in** 和 **@out** 参数的值。 请参阅管道定义中的“parameters”部分。

也可在 Azure Data Lake Analytics 服务上运行的作业的管道定义中指定其他属性，如 degreeOfParallelism 和 priority。

## <a name="dynamic-parameters"></a>动态参数
在示例管道定义中，in 和 out 参数都分配有硬编码值。 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

可改为使用动态参数。 例如： 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

在这种情况下，输入文件仍从 /datalake/input 文件夹中获取，输出文件仍在 /datalake/output 文件夹中生成。 文件名则根据切片开始时间动态产生。  


