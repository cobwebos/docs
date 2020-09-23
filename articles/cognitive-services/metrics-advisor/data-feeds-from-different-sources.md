---
title: 如何将来自不同源的数据馈送添加到指标顾问
titleSuffix: Azure Cognitive Services
description: 将不同的数据馈送添加到指标顾问
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: aahi
ms.openlocfilehash: f9ab340e73ce8d58da63a0089073ac4770bf2d52
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973375"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>将来自不同数据源的数据馈送添加到指标顾问

使用本文查找将不同类型的数据源连接到指标顾问的设置和要求。 请确保阅读如何载入 [数据](how-tos/onboard-your-data.md) ，以了解有关将数据用于指标顾问的关键概念。 

## <a name="supported-authentication-types"></a>支持的身份验证类型

| 身份验证类型 | 说明 |
| ---------------------|-------------|
|**基本** | 你将需要提供用于访问数据源的基本参数。 例如，连接字符串或键。 数据馈送管理器能够查看这些凭据。 |
| **AzureManagedIdentity** | Azure 资源的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)是 Azure Active Directory 的一项功能。 它通过 Azure AD 中的自动托管标识提供 Azure 服务。 你可以使用该标识向支持 Azure AD 身份验证的任何服务进行身份验证。|
| **AzureSQLConnectionString**| 将 AzureSQL 连接字符串存储为指标顾问中的 **凭据实体** ，并在每次载入度量值数据时直接使用该字符串。 只有凭据实体的管理员能够查看这些凭据，但允许授权的查看者创建数据馈送，而无需了解凭据的详细信息。 |
| **DataLakeGen2SharedKey**| 在指标顾问中将 data lake 帐户密钥存储为 **凭据实体** ，并在每次载入度量值数据时直接使用。 只有凭据实体的管理员能够查看这些凭据，但允许授权的查看者创建数据馈送，无需知道凭据详细信息。|
| **服务主体**| 在指标顾问中将服务主体存储为 **凭据实体** ，并在每次载入度量值数据时直接使用。 只有凭据实体的管理员才能查看凭据，但允许授权的查看者创建数据馈送，无需知道凭据详细信息。|
| **Key vault 中的服务主体**|将服务主体作为指标顾问存储在密钥保管库中作为 **凭据实体** ，并在每次载入度量值数据时直接使用。 只有 **凭据实体** 的管理员能够查看凭据，但也会使查看者无需知道详细凭据即可创建数据馈送。 |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>支持的数据源和相应的身份验证类型


| 数据源 | 身份验证类型 |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  基本 |
|[**Azure Blob 存储 (JSON) **](#blob) | 基本<br>对 microsoft.managedidentity|
|[**Azure Cosmos DB (SQL) **](#cosmosdb) | 基本 |
|[**Azure 数据资源管理器 (Kusto) **](#kusto) | 基本<br>对 microsoft.managedidentity|
|[**Azure Data Lake Storage Gen2**](#adl) | 基本<br>DataLakeGen2SharedKey<br>服务主体<br>Key vault 中的服务主体<br> |
|[**Azure SQL Database/SQL Server**](#sql) | 基本<br>对 microsoft.managedidentity<br>服务主体<br>Key vault 中的服务主体<br>AzureSQLConnectionString
|[**Azure 表存储**](#table) | 基本 | 
|[**ElasticSearch**](#es) | 基本 |
|[**Http 请求**](#http) | 基本 | 
|[**InfluxDB (InfluxQL) **](#influxdb) | 基本 |
|[**MongoDB**](#mongodb) | 基本 |
|[**MySQL**](#mysql) | 基本 |
|[**PostgreSQL**](#pgsql)| 基本|

创建 **凭据实体** 并将其用于对数据源进行身份验证。 以下各节指定了 *基本* 身份验证所需的参数。 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **应用程序 ID**：用于在使用 Application Insights API 时识别此应用程序。 若要获取应用程序 ID，请执行以下操作：

    1. 在 Application Insights 资源中，单击“API 访问权限”  。

    2. 将生成的应用程序 ID 复制到指标顾问中的 " **应用程序 id** " 字段。 
    
    有关详细信息，请参阅 [Azure 机器人服务文档](https://docs.microsoft.com/azure/bot-service/bot-service-resources-app-insights-keys#application-id) 。

* **Api 密钥**：浏览器外的应用程序使用 api 密钥来访问此资源。 若要获取 API 密钥，请执行以下操作：

    1. 在 Application Insights 资源中，单击“API 访问权限”  。

    2. 单击“创建 API 密钥”  。

    3. 输入简短说明，选中 "读取遥测数据" 选项，然后单击 "生成密钥" 按钮。

    4. 将 API 密钥复制到指标顾问中的 **api 密钥** 字段。

* **查询**： Azure 应用程序 Insights 日志在 Azure 数据资源管理器上构建，Azure Monitor 日志查询使用相同 Kusto 查询语言的版本。 [Kusto 查询语言文档](https://docs.microsoft.com/azure/data-explorer/kusto/query/)包含了该语言的所有详细信息，应该是编写针对 Application Insights 的查询的主要资源。 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob 存储 (JSON) </span>

* **连接字符串**：有关检索此字符串的信息，请参阅 Azure Blob 存储 [连接字符串](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string#view-and-copy-a-connection-string) 。

* **容器**：度量顾问需要将时序数据存储为 blob 文件， (每个时间戳的 blob) 单个容器下。 这是容器名称字段。

* **Blob 模板**：这是 Blob 文件名的模板。 例如：`/%Y/%m/X_%Y-%m-%d-%h-%M.json`。 支持以下参数：
  * `%Y` 年份的格式设置为 `yyyy`
  * `%m` 月份的格式设置为 `MM`
  * `%d` 格式为的日期 `dd`
  * `%h` 格式为的小时 `HH`
  * `%M` 格式为的分钟 `mm`

* **Json 格式版本**：定义 json 文件中的数据架构。 当前指标顾问支持两个版本：
  
  * v1 (默认值) 

      仅接受指标 *名称* 和 *值* 。 例如：
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      还接受度量值 *维度* 和 *时间戳* 。 例如：
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

每个 JSON 文件只能有一个时间戳。 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL) </span>

* **连接字符串**：用于访问 Azure Cosmos DB 的连接字符串。 可在 Cosmos DB 资源的 "密钥" 中找到此 **项**。 
* **数据库**：要对其进行查询的数据库。 此项可在 "**容器**" 部分下的**浏览**页中找到。
* **集合 id**：要对其进行查询的集合 id。 此项可在 "**容器**" 部分下的**浏览**页中找到。
* **Sql 查询**：用于获取数据并将数据构建为多维时序数据的 sql 查询。 您可以 `@StartTime` `@EndTime` 在查询中使用和变量。 应设置其格式： `yyyy-MM-dd HH:mm:ss` 。

    示例查询：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    来自2019/12/12 的数据切片的示例查询：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure 数据资源管理器 (Kusto) </span>

* **连接字符串**：有关如何从 Azure 数据资源管理器 (Kusto) 检索连接字符串的信息，请参阅 [查看和复制连接字符串](https://docs.microsoft.com/azure/data-explorer/kusto/api/connection-strings/kusto) 。

* **查询**：若要获取数据并将数据构建为多维时序数据，请参阅 [Kusto 查询语言](https://docs.microsoft.com/azure/data-explorer/kusto/query) 。 您可以 `@StartTime` `@EndTime` 在查询中使用和变量。 应设置其格式： `yyyy-MM-dd HH:mm:ss` 。

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **帐户名称**： Azure Data Lake Storage Gen2 的帐户名称。 可在 Azure 存储帐户中找到此项 (Azure Data Lake Storage Gen2 **访问密钥**中) 资源。

* **帐户密钥**：请指定用于访问 Azure Data Lake Storage Gen2 的帐户名称。 可在 Azure 存储帐户 (中找到此项 Azure Data Lake Storage Gen2) **访问密钥** "设置中的资源。

* **文件系统名称 (容器) **：指标顾问会预计将时序数据存储为 blob 文件，在单个容器下 (每个时间) 戳一个 blob。 这是容器名称字段。 可在 Azure 存储帐户 (Azure Data Lake Storage Gen2) 实例中找到此项，并单击 "Blob 服务" 部分中的 "容器"。

* **目录模板**：这是 Blob 文件的目录模板。 例如： */%Y/%m/%d*。 支持以下参数：
  * `%Y` 年份的格式设置为 `yyyy`
  * `%m` 月份的格式设置为 `MM`
  * `%d` 格式为的日期 `dd`
  * `%h` 格式为的小时 `HH`
  * `%M` 格式为的分钟 `mm`

* **文件模板**：这是 Blob 文件的文件模板。 例如： *X_% Y-% m-% d-% h-% M.json*。 支持以下参数：
  * `%Y` 年份的格式设置为 `yyyy`
  * `%m` 月份的格式设置为 `MM`
  * `%d` 格式为的日期 `dd`
  * `%h` 格式为的小时 `HH`
  * `%M` 格式为的分钟 `mm`

目前，指标顾问支持 JSON 文件中的数据架构，如下所示。 例如：

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL 数据库 |SQL Server</span>

* **连接字符串**：度量顾问接受 sql server 数据源的 [ADO.NET 样式连接字符串](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax) 。

    示例连接字符串：

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **查询**：用于获取数据并将数据构建为多维时序数据的 SQL 查询。 您可以 `@StartTime` 在查询中使用变量来帮助获取预期指标值。

  * `@StartTime`：格式为的日期时间 `yyyy-MM-dd HH:mm:ss`

    示例查询：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    为2019/12/12 的数据切片执行的实际查询：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure 表存储</span>

* **连接字符串**：有关如何从 Azure 表存储中检索连接字符串的信息，请参阅 [查看和复制连接字符串](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage?toc=%2Fazure%2Fstorage%2Ftables%2Ftoc.json&tabs=azure-portal#view-account-access-keys) 。

* **表名**：指定要查询的表。 可在 Azure 存储帐户实例中找到此项。 单击 "**表服务**" 部分中的 "**表**"。

* **查询** 您可以 `@StartTime` 在查询中使用。 `@StartTime` 替换为脚本中的 Yyyy-mm-ddthh： MM： ss 格式字符串。

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **Host**：指定 Elasticsearch 群集的主主机。
* **端口**：指定 Elasticsearch 群集的主端口。
* **Authorization 标头**：指定 Elasticsearch 群集的授权标头值。
* **Query**：指定查询以获取数据。 @StartTime支持占位符。 (例如，当数据为 2020-06-21T00：00：00Z 为引入， @StartTime = 2020-06-21T00：00： 00) 

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP 请求</span>

* **请求 URL**：可返回 JSON 的 HTTP URL。 占位符% Y，% m，% d，% h，% M 受支持：% Y = 年份，格式为 yyyy，% m = 月，格式为 MM，% d = 日，格式为 dd，% h = 小时，格式为 HH，% M = 分钟（格式为 MM）。 例如：`http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`。
* **请求 HTTP 方法**：使用 GET 或 POST。
* **请求标头**：可以添加基本身份验证。 
* **请求负载**：仅支持 JSON 有效负载。 @StartTime有效负载中支持占位符。 响应应为以下 JSON 格式： [{"timestamp"： "2018-01-01T00：00： 00Z"、"marketplace"： "en-us"、"count"：11、"收入"： 1.23}、{"timestamp"： "2018-01-01T00：00： 00Z"、"marketplace"： "zh-chs-cn"、"count"：22、"年收入"： 4.56}]。 (例如，当数据为 2020-06-21T00：00： 00Z is 引入， @StartTime = 2020-06-21T00：00： 00.0000000 + 00： 00) 

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL) </span>

* **连接字符串**：用于访问 InfluxDB 的连接字符串。
* **数据库**：要对其进行查询的数据库。
* **查询**：一个查询，用于获取数据并将数据构建为引入的多维时序数据。
* **用户名**：对于身份验证，这是可选的。 
* **密码**：对于身份验证，这是可选的。 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **连接字符串**：用于访问 MongoDB 的连接字符串。
* **数据库**：要对其进行查询的数据库。
* **命令**：一个命令，用于获取数据并将数据构建为引入的多维时序数据。

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **连接字符串**：用于访问 MySQL 数据库的连接字符串。
* **查询**：一个查询，用于获取数据并将数据构建为引入的多维时序数据。

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **连接字符串**：用于访问 PostgreSQL DB 的连接字符串。
* **查询**：一个查询，用于获取数据并将数据构建为引入的多维时序数据。

## <a name="next-steps"></a>后续步骤

* 在等待度量值数据引入到系统中时，请阅读有关 [如何管理数据馈送配置](how-tos/manage-data-feeds.md)的信息。
* 如果度量值数据为引入，则可以 [配置度量值和微调检测配置](how-tos/configure-metrics.md)。