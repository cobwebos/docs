---
title: 使用 U-SQL 脚本转换数据 - Azure | Microsoft Docs
description: 了解如何通过在 Azure Data Lake Analytics 计算服务上运行 U-SQL 脚本来处理或转换数据。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: abnarain
ms.openlocfilehash: b82353418931c872f8ec90f381b27bbb5d5781e9
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046949"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>通过在 Azure Data Lake Analytics 上运行 U-SQL 脚本来转换数据 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](v1/data-factory-usql-activity.md)
> * [当前版本](transform-data-using-data-lake-analytics.md)

Azure 数据工厂中的管道通过使用链接计算服务来处理链接存储服务中的数据。 它包含一系列活动，其中每个活动执行特定的处理操作。 本文介绍在 **Azure Data Lake Analytics** 计算链接服务上运行 **U-SQL** 脚本的 **Data Lake Analytics U-SQL 活动**。 

在使用 Data Lake Analytics U-SQL 活动创建管道之前，先创建 Azure Data Lake Analytics 帐户。 若要了解 Azure Data Lake Analytics，请参阅 [Azure Data Lake Analytics 入门](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 链接服务
创建 **Azure Data Lake Analytics** 链接服务将 Azure Data Lake Analytics 计算服务链接到 Azure 数据工厂。 管道中的 Data Lake Analytics U-SQL 活动是指此链接服务。 

下表介绍了 JSON 定义中使用的一般属性。 

| 属性                 | 说明                              | 必选                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | 类型属性应设置为 **AzureDataLakeAnalytics**。 | 是                                      |
| **accountName**          | Azure Data Lake Analytics 帐户名。  | 是                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI。           | 否                                       |
| **subscriptionId**       | Azure 订阅 ID                    | 否（如果未指定，则使用数据工厂的订阅）。 |
| **resourceGroupName**    | Azure 资源组名称                | 否（如果未指定，则使用数据工厂的资源组）。 |

### <a name="service-principal-authentication"></a>服务主体身份验证
Azure Data Lake Analytics 链接服务需要进行服务主体身份验证，才能连接到 Azure Data Lake Analytics 服务。 若要使用服务主体身份验证，请在 Azure Active Directory (Azure AD) 中注册一个应用程序实体并授予其访问 Data Lake Analytics 和它使用的 Data Lake Store 的权限。 有关详细步骤，请参阅[服务到服务身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：

* 应用程序 ID
* 应用程序密钥 
* 租户 ID

使用[添加用户向导](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user)向 Azure Data Lake Anatlyics 授予服务主体权限。

通过指定以下属性使用服务主体身份验证：

| 属性                | 说明                              | 必选 |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | 指定应用程序的客户端 ID。     | 是      |
| **servicePrincipalKey** | 指定应用程序的密钥。           | 是      |
| **tenant**              | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是      |

**示例：服务主体身份验证**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant ID>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

若要了解有关链接服务的详细信息，请参阅[计算链接服务](compute-linked-services.md)。

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 活动
以下 JSON 代码段定义了具有 Data Lake Analytics U-SQL 活动的管道。 活动定义具有对之前创建的 Azure Data Lake Analytics 链接服务的引用。 若要执行 Data Lake Analytics U-SQL 脚本，数据工厂会将你指定的脚本提交到 Data Lake Analytics，Data Lake Analytics 的脚本中将定义所需的输入和输出，以便提取和输出。 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

下表描述了此活动特有的属性的名称和描述。 

| 属性            | 说明                              | 必选 |
| :------------------ | :--------------------------------------- | :------- |
| 名称                | 管道中活动的名称     | 是      |
| description         | 描述活动用途的文本。  | 否       |
| type                | 对于 Data Lake Analytics U-SQL 活动，活动类型是 **DataLakeAnalyticsU-SQL**。 | 是      |
| linkedServiceName   | Azure Data Lake Analytics 的链接服务。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。  |是       |
| scriptPath          | 包含 U-SQL 脚本的文件夹路径。 文件的名称区分大小写。 | 是      |
| scriptLinkedService | 将包含脚本的 **Azure Data Lake Store** 或 **Azure 存储**链接到数据工厂的链接服务 | 是      |
| degreeOfParallelism | 同时用于运行作业的最大节点数。 | 否       |
| priority            | 确定应在所有排队的作业中选择哪些作业首先运行。 编号越低，优先级越高。 | 否       |
| parameters          | 要传入 U-SQL 脚本的参数。    | 否       |
| runtimeVersion      | 要使用的 U-SQL 引擎的运行时版本。 | 否       |
| compilationMode     | <p>U-SQL 编译模式。 必须是以下值之一：**Semantic：** 只执行语义检查和必要的完整性检查；**Full：** 执行完整的编译，包括语法检查、优化、代码生成，等等；**SingleBox：** 通过将 TargetType 设置为 SingleBox，可执行完整编译。 如果该属性未指定值，则服务器将确定最佳编译模式。 | 否 |

数据工厂将提交脚本，请参阅 [SearchLogProcessing.txt 脚本定义](#sample-u-sql-script)了解有关脚本定义的信息。 

## <a name="sample-u-sql-script"></a>示例 U-SQL 脚本

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

在上面的脚本示例中，脚本的输入和输出分别在 **@in** 和 **@out** 参数中定义。 数据工厂使用“parameters”部分动态传递 U-SQL 脚本中 **@in** 和 **@out** 参数的值。 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

在这种情况下，输入文件仍从 /datalake/input 文件夹中获取，输出文件仍在 /datalake/output 文件夹中生成。 文件名是基于触发管道时传入的窗口开始时间动态生成的。  

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据： 

* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [机器学习“批处理执行”活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
