---
title: 向/从 Azure Data Lake Store 复制数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂向/从 Data Lake Store 复制数据
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8f86f43b4d8c474f338285abffb3c444f5ebc2d7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054732"
---
# <a name="copy-data-to-and-from-data-lake-store-by-using-data-factory"></a>使用数据工厂向/从 Data Lake Store 复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](data-factory-azure-datalake-connector.md)
> * [版本 2（当前版本）](../connector-azure-data-lake-store.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用数据工厂服务的当前版本，请参阅 [V2 中的 Azure Data Lake Store 连接器](../connector-azure-data-lake-store.md)。

本文介绍如何使用 Azure 数据工厂中的复制活动将数据移入/移出 Azure Data Lake Store。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，后者概述了如何使用复制活动移动数据。

## <a name="supported-scenarios"></a>支持的方案
可以将数据**从 Azure Data Lake Store** 复制到以下数据存储：

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

可以将数据从以下数据存储复制**到 Azure Data Lake Store**：

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> 在创建包含复制活动的管道之前，先创建一个 Data Lake Store 帐户。 有关详细信息，请参阅 [Azure Data Lake Store 入门](../../data-lake-store/data-lake-store-get-started-portal.md)。

## <a name="supported-authentication-types"></a>支持的身份验证类型
Data Lake Store 连接器支持以下身份验证类型：
* 服务主体身份验证
* 用户凭据 (OAuth) 身份验证 

建议使用服务主体身份验证，特别是在进行已计划的数据复制时。 使用用户凭据身份验证时可能会出现令牌过期行为。 有关配置详细信息，请参阅[链接服务属性](#linked-service-properties)部分。

## <a name="get-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以将数据移入/移出 Azure Data Lake Store。

通过创建管道来复制数据的最简单方法是使用**复制向导**。 有关使用复制向导创建管道的教程，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure 资源管理器模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建**数据工厂**。 数据工厂可以包含一个或多个管道。 
2. 创建链接服务可将输入和输出数据存储链接到数据工厂。 例如，如果要将数据从 Azure Blob 存储复制到 Azure Data Lake Store，可创建两个链接服务，将 Azure 存储帐户和 Azure Data Lake Store 链接到数据工厂。 有关特定于 Azure Data Lake Store 的链接服务属性，请参阅[链接服务属性](#linked-service-properties)部分。 
2. 创建数据集以表示复制操作的输入和输出数据。 在上一个步骤所述的示例中，创建了一个数据集来指定 Blob 容器和包含输入数据的文件夹。 创建了另一个数据集来指定 Data Lake Store 中用于保存从 Blob 存储复制的数据的文件夹和文件路径。 有关特定于 Azure Data Lake Store 的数据集属性，请参阅[数据集属性](#dataset-properties)部分。
3. 创建包含复制活动的管道，该活动将一个数据集作为输入，将一个数据集作为输出。 在前面所述的示例中，在复制活动中使用 BlobSource 作为源，AzureDataLakeStoreSink 作为接收器。 同样，如果从 Azure Data Lake Store 复制到 Azure Blob 存储，则在复制活动中使用 AzureDataLakeStoreSource 和 BlobSink。 有关特定于 Azure Data Lake Store 的复制活动属性，请参阅[复制活动属性](#copy-activity-properties)部分。 有关如何将数据存储用作源或接收器的详细信息，请单击前面章节中的相应数据存储链接。  

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于向/从 Azure Data Lake Store 复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例](#json-examples-for-copying-data-to-and-from-data-lake-store)部分。

对于特定于 Data Lake Store 的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性
链接服务可将数据存储链接到数据工厂。 创建 **AzureDataLakeStore** 类型的链接服务，以便将 Data Lake Store 数据链接到数据工厂。 下表描述了特定于 Data Lake Store 链接服务的 JSON 元素。 可以选择服务主体身份验证或用户凭据身份验证。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureDataLakeStore**。 | 是 |
| **dataLakeStoreUri** | Azure Data Lake Store 帐户相关信息。 此信息采用以下格式之一：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| **subscriptionId** | Data Lake Store 帐户所属的 Azure 订阅 ID。 | 接收器所需 |
| **resourceGroupName** | Data Lake Store 帐户所属的 Azure 资源组名称。 | 接收器所需 |

### <a name="service-principal-authentication-recommended"></a>服务主体身份验证（推荐）
若要使用服务主体身份验证，请在 Azure Active Directory (Azure AD) 中注册一个应用程序实体并授予其访问 Data Lake Store 的权限。 有关详细步骤，请参阅[服务到服务身份验证](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：
* 应用程序 ID
* 应用程序密钥 
* 租户 ID

> [!IMPORTANT]
> 请确保在 Azure Data Lake Store 中授予服务主体适当的权限：
>- 若要将 Data Lake Store 用作源，请至少授予“读取 + 执行”数据访问权限才能列出和复制文件夹的内容，或者授予“读取”权限以复制单个文件。 对帐户级别访问控制没有要求。
>- 若要将 Data Lake Store 用作接收器，请至少授予“写入 + 执行”数据访问权限才能在文件夹中创建子项目。 如果使用 Azure IR 来增强复制功能（源和接收器都在云中），为了让数据工厂检测到 Data Lake Store区域，请至少授予帐户访问控制 (IAM) 中的“读者”角色。 如果想要避免使用此 IAM 角色，请使用 Data Lake Store 在复制活动中的位置[指定 executionLocation](data-factory-data-movement-activities.md#global)。
>- 如果使用复制向导创作管道，请至少授予帐户访问控制 (IAM) 中的“读者”角色。 此外，至少授予对 Data Lake Store 根目录 ("/") 及其子级的“读取 + 执行”权限。 否则，可能会显示“提供的凭据无效”消息。

通过指定以下属性使用服务主体身份验证：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| **servicePrincipalId** | 指定应用程序的客户端 ID。 | 是 |
| **servicePrincipalKey** | 指定应用程序的密钥。 | 是 |
| **tenant** | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |

**示例：服务主体身份验证**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>用户凭据身份验证
或者，通过指定以下属性，可使用用户凭据身份验证向/从 Data Lake Store 进行复制：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| **authorization** | 单击数据工厂编辑器中的“授权”按钮，并输入凭据以会自动生成的授权 URL 分配给此属性。 | 是 |
| **sessionId** | OAuth 授权会话中的 OAuth 会话 ID。 每个会话 ID 都是唯一的，并且只能使用一次。 使用数据工厂编辑器时会自动生成此设置。 | 是 |

> [!IMPORTANT]
> 请确保在 Azure Data Lake Store 中授予用户适当的权限：
>- 若要将 Data Lake Store 用作源，请至少授予“读取 + 执行”数据访问权限才能列出和复制文件夹的内容，或者授予“读取”权限以复制单个文件。 对帐户级别访问控制没有要求。
>- 若要将 Data Lake Store 用作接收器，请至少授予“写入 + 执行”数据访问权限才能在文件夹中创建子项目。 如果使用 Azure IR 来增强复制功能（源和接收器都在云中），为了让数据工厂检测到 Data Lake Store区域，请至少授予帐户访问控制 (IAM) 中的“读者”角色。 如果想要避免使用此 IAM 角色，请使用 Data Lake Store 在复制活动中的位置[指定 executionLocation](data-factory-data-movement-activities.md#global)。
>- 如果使用复制向导创作管道，请至少授予帐户访问控制 (IAM) 中的“读者”角色。 此外，至少授予对 Data Lake Store 根目录 ("/") 及其子级的“读取 + 执行”权限。 否则，可能会显示“提供的凭据无效”消息。

**示例：用户凭据身份验证**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>令牌过期
使用“授权”按钮生成的授权代码在一段时间后便会过期。 以下消息意味着身份验证令牌已过期：

凭据操作错误: invalid_grant - AADSTS70002: 验证凭据时出错。 AADSTS70008：提供的访问权限已过期或已被吊销。 跟踪 ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相关 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 时间戳: 2015-12-15 21-09-31Z。

下表介绍不同类型用户帐户的过期时间：

| 用户类型 | 过期时间 |
|:--- |:--- |
| *不*由 Azure Active Directory 管理的用户帐户（例如 @hotmail.com 或 @live.com） |12 小时 |
| 由 Azure Active Directory 管理的用户帐户 |最后一次运行切片后的 14 天 <br/><br/>如果以基于 OAuth 的链接服务为基础的切片每 14 天至少运行一次，则为 90 天 |

如果在此令牌过期之前更改密码，则该令牌立即过期。 系统会显示此部分前面提到的错误。

令牌过期后，可以使用“授权”按钮为帐户重新授权，以便重新部署链接服务。 还可使用以下代码以编程方式生成 **sessionId** 和 **authorization** 属性的值：


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
有关代码中使用的数据工厂类的详细信息，请参阅以下主题：[AzureDataLakeStoreLinkedService 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、[AzureDataLakeAnalyticsLinkedService 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)和 [AuthorizationSessionGetResponse 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)。 为代码中使用的 `WindowsFormsWebAuthenticationDialog` 类添加对 `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` 版本 `2.9.10826.1824` 的引用。

## <a name="troubleshooting-tips"></a>故障排除提示

症状：在将数据复制到 Azure Data Lake Store 时，如果复制活动失败，出现以下错误：

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

根本原因：可能有两个原因：

1. 在 Azure Data Lake Store 已链接的服务中指定的 `resourceGroupName` 和/或 `subscriptionId` 不正确；
2. 用户或服务主体没有所需的权限。

**解决方法：**

1. 请确保你的 data lake 帐户确实属于链接的服务 `typeProperties` 中指定的 `subscriptionId` 和 `resourceGroupName`。

2. 请确保至少向用户或 data lake 帐户上的服务主体授予“读者”的角色。 以下为具体操作：

    1. 请转到 Azure 门户 -> 你的 Data Lake Store 帐户
    2. 在 Data Lake Store 的边栏选项卡上单击“访问控制 (IAM)”
    3. 在“访问控制 (IAM)”的边栏选项卡上单击“添加”
    4. 将“角色”设置为“读者”，并选择用于复制的用户或服务主体以授予访问权限

3. 如果不想为用户或服务主体授予“读者”角色，可选择在复制活动中使用 Data Lake Store 的位置[显式指定执行位置](data-factory-data-movement-activities.md#global)。 示例：

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>数据集属性
要指定一个数据集来表示 Data Lake Store 中的输入数据，可以将该数据集的 **type** 属性设置为 **AzureDataLakeStore**。 将该数据集的 **linkedServiceName** 属性设置为 Data Lake Store 链接服务的名称。 有关可用于定义数据集的 JSON 节和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 在 JSON 中，所有数据集类型（例如 Azure SQL 数据库、Azure Blob 和 Azure 表）的 **structure**、**availability** 和 **policy** 等节均类似。 每种数据集的 **typeProperties** 节均有所不同，该节提供数据在数据存储中的位置、格式等信息。 

**AzureDataLakeStore** 类型的数据集的 **typeProperties** 节包含以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| **folderPath** |Data Lake Store 中容器和文件夹的路径。 |是 |
| **fileName** |Azure Data Lake Store 中文件的名称。 **fileName** 属性可选，并且区分大小写。 <br/><br/>如果指定 **fileName**，则活动（包括复制）将对特定文件起作用。<br/><br/>如果未指定 **fileName**，则复制将包括输入数据集的 **folderPath** 中的所有文件。<br/><br/>如果没有为输出数据集指定 **fileName**，并且没有在活动接收器中指定 **preserveHierarchy**，所生成文件的名称会采用格式 Data._Guid_.txt`。 例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt。 |否 |
| **partitionedBy** |**partitionedBy** 属性可选。 它可用于指定时序数据的动态路径和文件名。 例如，**folderPath** 可针对每小时的数据参数化。 有关详细信息和示例，请参阅 [partitionedBy 属性](#using-partitionedby-property)。 |否 |
| **format** | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅 [Azure 数据工厂支持的文件和压缩格式](data-factory-supported-file-and-compression-formats.md)一文中的[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[JSON 格式](data-factory-supported-file-and-compression-formats.md#json-format)、 [Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[ORC 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)等部分。 <br><br> 如果想要在基于文件的存储之间“按原样”复制文件（二进制副本），可以在输入和输出数据集定义中跳过 `format` 节。 |否 |
| **compression** | 指定数据的压缩类型和级别。 支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为“最佳”和“最快”。 有关详细信息，请参阅 [Azure 数据工厂支持的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

### <a name="the-partitionedby-property"></a>partitionedBy 属性
可以使用 **partitionedBy** 属性、数据工厂函数和系统变量指定时序数据的动态 **folderPath** 和 **fileName** 属性。 有关详细信息，请参阅 [Azure 数据工厂 — 函数和系统变量](data-factory-functions-variables.md)一文。


在下面的示例中，`{Slice}` 已替换为数据工厂系统变量 `SliceStart` 的值（使用指定格式 `yyyyMMddHH`）。 名称 `SliceStart` 指切片开始时间。 每个切片（比如在 `wikidatagateway/wikisampledataout/2014100103` 或 `wikidatagateway/wikisampledataout/2014100104` 中）的 `folderPath` 属性均不同。

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

在下面的示例中，`SliceStart` 的年、月、日和时间已提取到 `folderPath` 和 `fileName` 属性使用的各个变量中：
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
有关时序数据集、计划和切片的更多详细信息，请参阅 [Azure 数据工厂中的数据集](data-factory-create-datasets.md)和[数据工厂计划和执行](data-factory-scheduling-and-execution.md)这两篇文章。 


## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

活动的 **typeProperties** 节中的可用属性因每种活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

**AzureDataLakeStoreSource** 支持 **typeProperties** 节中的以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| **recursive** |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True（默认值）、False |否 |


**AzureDataLakeStoreSink** 支持 **typeProperties** 节中的以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| **copyBehavior** |指定复制行为。 |<b>PreserveHierarchy</b>：保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><br/><b>FlattenHierarchy</b>：源文件夹中的所有文件在目标文件夹的第一个级别中创建。 创建目标文件时，自动生成名称。<br/><br/><b>MergeFiles</b>：将源文件夹的所有文件合并到一个文件中。 如果指定了文件名或 Blob 名称，则合并文件的名称为指定名称。 否则，会自动生成文件名。 |否 |

### <a name="recursive-and-copybehavior-examples"></a>recursive 和 copyBehavior 示例
本节介绍了将 recursive 和 copyBehavior 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 产生的行为 |
| --- | --- | --- |
| 是 |preserveHierarchy |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用与源相同的结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| 是 |flattenHierarchy |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成名称 |
| 是 |mergeFiles |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的内容将合并到一个文件中，且自动生成文件名 |
| false |preserveHierarchy |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy |对于具有以下结构的源文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/><br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles |对于具有以下结构的源文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成名称<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |

## <a name="supported-file-and-compression-formats"></a>支持的文件和压缩格式
有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md)一文。

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>向/从 Data Lake Store 复制数据的 JSON 示例
以下示例提供 JSON 示例定义。 可以使用这些示例定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 以下示例演示如何向/从 Data Lake Store 和 Azure Blob 存储复制数据。 但是，可以从任何源将数据_直接_复制到任何受支持的接收器。 有关详细信息，请参阅[使用复制活动移动数据](data-factory-data-movement-activities.md)一文中的“支持的数据存储和格式”部分。  

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>示例：将数据从 Azure Blob 存储复制到 Azure Data Lake Store
本部分中的示例代码演示：

* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
* [AzureDataLakeStore](#linked-service-properties) 类型的链接服务。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureDataLakeStore](#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 和 [AzureDataLakeStoreSink](#copy-activity-properties)。

以下示例每隔一小时将时序数据从 Azure Blob 存储复制到 Data Lake Store。 

**Azure 存储链接服务**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Azure Data Lake Store 链接服务**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> 有关配置详细信息，请参阅[链接服务属性](#linked-service-properties)部分。
>

**Azure Blob 输入数据集**

在下面的示例中，每隔一小时从新 Blob 获取一次数据 (`"frequency": "Hour", "interval": 1`)。 根据正在处理的切片的开始时间，将对 blob 的文件夹路径和文件名进行动态计算。 文件夹路径使用开始时间的年、月和日部分。 文件名使用开始时间的小时部分。 `"external": true` 设置将告知数据工厂服务：表位于数据工厂外且不由数据工厂中的活动生成。

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Data Lake Store 输出数据集**

下面的示例将数据复制到 Data Lake Store。 每隔一小时将新的数据复制到 Data Lake Store。

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**管道中使用 Blob 源和 Data Lake Store 接收器的复制活动**

在下面的示例中，管道包含配置为使用输入和输出数据集的复制活动。 该复制活动计划每小时运行一次。 在管道 JSON 定义中，`source` 类型设置为 `BlobSource`，`sink` 类型设置为 `AzureDataLakeStoreSink`。

```json
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>示例：将数据从 Azure Data Lake Store 复制到 Azure Blob
本部分中的示例代码演示：

* [AzureDataLakeStore](#linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
* [AzureDataLakeStore](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [AzureDataLakeStoreSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此代码每隔一小时将时序数据从 Data Lake Store 复制到 Azure Blob。 

**Azure Data Lake Store 链接服务**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> 有关配置详细信息，请参阅[链接服务属性](#linked-service-properties)部分。
>

**Azure 存储链接服务**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Data Lake 输入数据集**

在此示例中，将 `"external"` 设置为 `true` 会告知数据工厂服务：表位于数据工厂外且不由数据工厂中的活动生成。

```json
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
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
        "external": true,
        "availability": {
            "frequency": "Hour",
              "interval": 1
        },
        "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
        }
      }
}
```
**Azure Blob 输出数据集**

在下面的示例中，数据写入到新 Blob，每隔一小时进行一次 (`"frequency": "Hour", "interval": 1`)。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**管道中使用 Azure Data Lake Store 源和 Blob 接收器的复制活动**

在下面的示例中，管道包含配置为使用输入和输出数据集的复制活动。 该复制活动计划每小时运行一次。 在管道 JSON 定义中，`source` 类型设置为 `AzureDataLakeStoreSource`，`sink` 类型设置为 `BlobSink`。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

在复制活动定义中，还可以将源数据集中的列映射到接收器数据集中的列。 有关详细信息，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响复制活动性能的因素以及优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)一文。
