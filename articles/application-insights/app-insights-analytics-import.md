---
title: "将数据导入到 Azure Application Insights 中的 Analytics | Microsoft 文档"
description: "导入要与应用遥测数据联接的静态数据，或者导入要使用 Analytics 查询的单独数据流。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 47c3491b067d5e112db589672b68e7cfc7cbe921
ms.openlocfilehash: eb89c6f485f2321f729dcfe650af4355de84a9ac


---
# <a name="import-data-into-analytics"></a>将数据导入到 Analytics 中

将任何表格数据导入到 [Analytics](app-insights-analytics.md) 中，以便将这些数据与来自应用的 [Application Insights](app-insights-overview.md) 遥测数据联接，或者将其作为单独的流进行分析。 Analytics 是一种功能强大的查询语言，非常适合于分析大量加盖时间戳的遥测数据流。

可以使用自己的架构将数据导入到 Analytics 中。 它不一定使用标准 Application Insights 架构（如请求或跟踪）。

目前，可以导入 CSV（逗号分隔值）文件或采用了 Tab 或分号分隔符的类似格式的文件。

在三种情况下，导入到 Analytics 非常有用：

* **与应用遥测数据联接。** 例如，可以导入一个表，该表将网站 URL 映射到更具可读性的页标题。 在 Analytics 中，可以创建显示网站中十个最常用页面的仪表板图表报表。 现在，它可以显示页面标题而不是 URL。
* **将应用程序遥测数据与网络流量、服务器数据或 CDN 日志文件等其他源关联**。
* **将 Analytics 应用于单独的数据流。** Application Insights Analytics 是一种强大的工具，非常适用于加盖时间戳的稀疏流，在许多情况下大大优于 SQL。 如果有来自其他某个源的此类流，可以使用 Analytics 进行分析。

将数据发送到数据源很容易。 

1. （一次）在“数据源”中定义数据的架构。
2. （定期）将数据上载到 Azure 存储，并调用 REST API 向我们通知新数据正在等待引入。 几分钟内，数据即可用于在 Analytics中查询。

上载的频率由用户以及用户希望数据可用于查询的速度决定。 将数据以较大块（但不要超过 1GB）的形式上载更高效。

> [!NOTE]
> *有大量要分析的数据源？* [*请考虑使用 *logstash* 将数据传送到 Application Insights。*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>开始之前

需要：

1. Microsoft Azure 中的 Application Insights 资源。

 * 如果要独立于任何其他遥测数据分析数据，请[创建新的 Application Insights 资源](app-insights-create-new-resource.md)。
 * 如果要将数据与已使用 Application Insights 设置的应用中的遥测数据进行联接或比较，可以对该应用使用此资源。
 * 对该资源具有参与者或所有者访问权限。
 
2. Azure 存储。 用户上载到 Azure 存储，Analytics 将从那里获取数据。 

 * 我们建议为 Blob 创建一个专用的存储帐户。 如果与其他进程共享 Blob，那么我们的进程读取 Blob 将花费更长时间。

2. 此功能处于预览状态时，必须请求获得访问权限。

 * 从 [Azure 门户](https://portal.azure.com)的 Application Insights 资源中，打开 Analytics。 
 * 在架构窗格的底部，单击“其他数据源”下的“联系我们”链接。 
 * 如果看到“添加数据源”，则已具有访问权限。


## <a name="define-your-schema"></a>定义架构

导入数据之前，必须定义*数据源*，它指定数据的架构。

1. 启动数据源向导

    ![添加新数据源](./media/app-insights-analytics-import/add-new-data-source.png)

2. 按照说明来上载示例数据文件。

 * 此示例的第一行可以是列标题。 （可以在下一步中更改字段名称。）
 * 该示例应至少包含 10 行的数据。

3. 查看向导已从示例推断出来的架构。 如有必要，可以调整已推断出来的列类型。

4. 选择时间戳。 Analytics 中的所有数据都必须有时间戳字段。 它必须具有类型 `datetime`，但它无需命名为“timestamp”。 如果数据的一列包含 ISO 格式的日期和时间，请选择此列作为时间戳列。 否则，请选择“按到达的数据”，然后导入进程将添加时间戳字段。

    ![查看架构](./media/app-insights-analytics-import/data-source-review-schema.png)

5. 创建数据源。


## <a name="import-data"></a>导入数据

若要导入数据，请将其上载到 Azure 存储、为其创建访问密钥，然后执行 REST API 调用。

![添加新数据源](./media/app-insights-analytics-import/analytics-upload-process.png)

可以手动执行以下过程，或者设置自动系统以定期执行此过程。 需要对每个要导入的数据块执行这些步骤。

1. 将数据上载到 [Azure Blob 存储](../storage/storage-dotnet-how-to-use-blobs.md)。 

 * Blob 可以是任何小于等于 1 GB 的大小（未压缩）。 从性能角度来看，数百 MB 大小的大型 Blob 十分理想。
 * 可以使用 Gzip 进行压缩，缩短上载时间和数据可供查询的延迟。 使用 `.gz` 文件扩展名。
 
2. [为 Blob 创建共享访问签名密钥](../storage/storage-dotnet-shared-access-signature-part-2.md)。 密钥应具有一天的有效期并提供读取访问权限。
3. 执行 REST 调用以向 Application Insights 通知数据正在等待。

 * 终结点：`https://dc.services.visualstudio.com/v2/track`
 * HTTP 方法：POST
 * 有效负载：

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

占位符为：

* `Blob URI with Shared Access Key`：从此过程中获取该项以用于创建密钥。 它特定于 Blob。
* `Schema ID`：针对已定义架构生成的架构 ID。 此 blob 中的数据应符合该架构。
* `DateTime`：提交该请求的时间 (UTC)。 我们接受以下格式：ISO8601（如 "2016-01-01 13:45:01"）；RFC822 ("Wed, 14 Dec 16 14:57:01 +0000")；RFC850 ("Wednesday, 14-Dec-16 14:57:00 UTC")；RFC1123 ("Wed, 14 Dec 2016 14:57:00 +0000")。
* Application Insights 资源的 `Instrumentation key`。

几分钟后，数据将在 Analytics 中可用。

## <a name="error-responses"></a>错误响应

* **400 错误请求**：指示请求有效负载无效。 请检查：
 * 检测密钥是否正确。
 * 时间值是否有效。 它应该是 UTC 格式的当前时间。
 * 数据是否符合架构。
* **403 禁止访问**：已发送的 Blob 不可访问。 请确保共享访问密钥有效并且未过期。
* **404 未找到**：
 * Blob 不存在。
 * 数据源名称错误。

响应错误消息中提供了更多详细信息。

## <a name="sample-code"></a>代码示例

此代码使用 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) NuGet 包。

### <a name="classes"></a>类

```C#


namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            HttpWebResponse response; 
            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 

```

### <a name="ingest-data"></a>引入数据

将此代码用于每个 Blob。 

```C#


   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "tableId/sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);

```

## <a name="next-steps"></a>后续步骤

* [Analytics 查询语言教程](app-insights-analytics-tour.md)
* [使用 *Logstash* 将数据发送到 Application Insights](https://github.com/Microsoft/logstash-output-application-insights)



<!--HONumber=Jan17_HO3-->


