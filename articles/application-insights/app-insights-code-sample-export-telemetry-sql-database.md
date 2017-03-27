---
title: "示例：分析从 Azure Application Insights 导出的数据 | Microsoft Docs"
description: "在 Application Insights 中使用连续导出功能编写遥测数据的分析代码。 将数据保存到 SQL。"
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 3ffb62b6-3fe9-455d-a260-b2a0201b5ecd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: fedd078402bbd220bce9b71cd035508d46f92f82


---
# <a name="code-sample-parse-data-exported-from-application-insights"></a>代码示例：分析从 Application Insights 导出的数据
本文说明如何编写代码来处理使用[连续导出][export]从 [Azure Application Insights][start] 导出的数据。 连续导出以 JSON 格式将遥测数据移入 Azure 存储，因此，我们将编写一些代码来分析 JSON 对象，并在数据库表中创建一些行。

作为示例，我们将编写代码将遥测数据从 Application Insights 移入 Azure SQL 数据库。

开始之前，请注意：

* 将导出的数据转移到数据库的更有效方法是[使用流分析](app-insights-code-sample-export-sql-stream-analytics.md)，但本文的目的是演示如何编写一些代码来处理导出的数据。 可以改写此代码示例，对导出的遥测数据执行其他操作。
* 本示例通过在 Azure 辅助角色中运行代码，将数据移入 Azure 数据库。 但可以将此代码改写为在本地服务器中运行，以便将数据提取到本地 SQL 服务器中。
* 可以[编写代码来直接访问 Application Insights 中的遥测数据](http://dev.applicationinsights.io/)，而无需将其导出。

如果尚未开始使用 Application Insights 监视 Web 应用程序，请[立即执行该操作][start]。



## <a name="create-storage-in-azure"></a>在 Azure 中创建存储
来自 Application Insights 的数据始终以 JSON 格式导出到 Azure 存储帐户。 代码从此存储读取数据。

1. 在 [Azure 门户][portal]内的订阅中创建一个“经典”存储帐户。
   
    ![在 Azure 门户中，依次选择“添加”、“数据”、“存储”](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)
2. 创建容器
   
    ![在新存储中选择“容器”，单击“容器”磁贴，然后单击“添加”](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)

## <a name="start-continuous-export-to-azure-storage"></a>开始向 Azure 存储连续导出
1. 在 Azure 门户中，浏览到为应用程序创建的 Application Insights 资源。
   
    ![依次选择“浏览”、“Application Insights”、你的应用程序](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)
2. 创建连续导出。
   
    ![依次选择“设置”、“连续导出”、“添加”](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)

    选择前面创建的存储帐户：

    ![设置导出目标](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)

    设置想要查看的事件类型：

    ![选择事件类型](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

1. 让我们累积一些数据。 请休息一下，让其他人先使用该应用程序一段时间。 应用程序中会逐渐传入遥测数据，[指标资源管理器](app-insights-metrics-explorer.md)中会显示统计图表，[诊断搜索](app-insights-diagnostic-search.md)中会显示各个事件。 
   
    此外，数据将导出到存储。 
2. 检查导出的数据 在 Visual Studio 中，请选择“查看”>“Cloud Explorer”，然后打开“Azure”>“存储”。 （如果未看到此菜单选项，则需要安装 Azure SDK：打开“新建项目”对话框，然后打开“Visual C#”>“云”>“获取用于 .NET 的 Microsoft Azure SDK”。）
   
    ![在 Visual Studio 中，依次打开“Server Browser”、“Azure”、“存储”](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)
   
    记下派生自应用程序名称和检测密钥的路径名称的共同部分。 

事件将以 JSON 格式写入 Blob 文件。 每个文件可能包含一个或多个事件。 因此我们想要读取事件数据，并筛选出所需的字段。 可以针对数据执行各种操作，但我们目前的计划是编写一些代码，将数据移到 SQL 数据库。 这样做可以轻松运行许多微妙的查询。

## <a name="create-an-azure-sql-database"></a>创建 Azure SQL 数据库
在本示例中，我们将编写代码以将数据推送到数据库。

再次在 [Azure 门户][portal]中打开订阅，创建要在其中写入数据的数据库（除非已有新服务器，否则还要创建新服务器）。

![依次选择“新建”、“数据”、“SQL”](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)

确保数据库服务器允许访问 Azure 服务：

![依次选择“浏览”、“服务器”、你的服务器、“设置”、“防火墙”、“允许访问 Azure”](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)

## <a name="create-a-worker-role"></a>创建辅助角色
现在，我们终于可以编写[一些代码](https://sesitai.codeplex.com/)来分析导出的 Blob 的 JSON，并在数据库中创建记录。 由于导出存储和数据库都在 Azure 中，因此我们将在 Azure 辅助角色中运行代码。

此代码自动提取 JSON 中的任何属性。 有关属性的说明，请参阅[导出数据模型](app-insights-export-data-model.md)。

#### <a name="create-worker-role-project"></a>创建辅助角色项目
在 Visual Studio 中，创建新的辅助角色项目：

![“新建项目”、“Visual C#”、“云”、“Azure 云服务”](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![在“新建云服务”对话框中，依次选择“Visual C#”、“辅助角色”](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)

#### <a name="connect-to-the-storage-account"></a>连接到存储帐户
在 Azure 中，从存储帐户获取连接字符串：

![在“存储帐户”中选择“密钥”，然后复制“主连接字符串”](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

在 Visual Studio 中，使用存储帐户连接字符串配置辅助角色设置：

![在解决方案资源管理器中的“云服务”项目下面，展开“角色”，然后打开你的辅助角色。 打开“设置”选项卡，选择“添加设置”，设置 name=StorageConnectionString，type=connection string，然后单击以设置值。 手动设置该值，并粘贴连接字符串。](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)

#### <a name="packages"></a>包
在解决方案资源管理器中，右键单击辅助角色项目并选择“管理 NuGet 包”。
搜索并安装以下包： 

* EntityFramework 6.1.2 或更高版本 - 我们将根据 Blob 中的 JSON 内容，使用此包即时生成数据库表架构。
* JsonFx - 使用此包将 JSON 平整为 C# 类属性。

使用此工具从单个 JSON 文档生成 C# 类。 此外，还需要做一些小的更改，例如在数据库表（如 urlData_port）中将 JSON 数组平整为单个 C# 属性，再平整为 单个列） 

* [JSON C# 类生成器](http://jsonclassgenerator.codeplex.com/)

## <a name="code"></a>代码
可以将此代码放入 `WorkerRole.cs`。

#### <a name="imports"></a>导入
    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### <a name="retrieve-the-storage-connection-string"></a>检索存储连接字符串
    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### <a name="run-the-worker-at-regular-intervals"></a>定期运行辅助角色
替换现有的运行方法，选择所需的间隔。 由于导出功能完成一个 JSON 对象需要一小时，因此间隔时间至少应设置为一小时。

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000

        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### <a name="insert-each-json-object-as-a-table-row"></a>插入每个 JSON 对象作为表行
    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
             {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
        //handle exception
      }
    }

#### <a name="parse-each-blob"></a>分析每个 Blob
    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());

        string json;

        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());

          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));

          recCount = entities.Count();
          failureCount = 0; //resetting failure count

          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);

            Dictionary<string, object> dict = new Dictionary<string, object>();

            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");

            switch (FilterType)
            {
              case "PageViewPerformance":

              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
                }

              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }

            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);

            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;

            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### <a name="prepare-a-dictionary-for-each-json-document"></a>为每个 JSON 文档准备字典
    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }
        }

#### <a name="cast-the-json-document-into-c-class-telemetry-object-properties"></a>将 JSON 文档强制转换为 C# 类遥测对象属性
     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }

            return res;
        }

#### <a name="pageviewperformance-class-file-generated-out-of-json-document"></a>从 JSON 文档中生成的 PageViewPerformance 类文件
    public class PageViewPerformance
    {
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### <a name="dbcontext-for-sql-interaction-by-entity-framework"></a>实体框架的 SQL 交互的 DBcontext
    public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

使用 `app.config` 中的名称 `TelemetryContext` 添加数据库连接字符串。

## <a name="schema-information-only"></a>架构（仅供参考）
这是要为 PageView 生成的表的架构。

> [!NOTE]
> 无需运行此脚本。 JSON 中的属性确定了表中的列。
> 
> 

    CREATE TABLE [dbo].[PageViewPerformances](
    [Id] [uniqueidentifier] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlData_port] [int] NOT NULL,
    [urlData_protocol] [nvarchar](max) NULL,
    [urlData_host] [nvarchar](max) NULL,
    [urlData_base] [nvarchar](max) NULL,
    [urlData_hashTag] [nvarchar](max) NULL,
    [total_value] [float] NOT NULL,
    [networkConnection_value] [float] NOT NULL,
    [sendRequest_value] [float] NOT NULL,
    [receiveRequest_value] [float] NOT NULL,
    [clientProcess_value] [float] NOT NULL,
    [name] [nvarchar](max) NULL,
    [User] [nvarchar](max) NULL,
    [internal_data_id] [nvarchar](max) NULL,
    [internal_data_documentVersion] [nvarchar](max) NULL,
    [context_data_eventTime] [datetime] NULL,
    [context_device_id] [nvarchar](max) NULL,
    [context_device_type] [nvarchar](max) NULL,
    [context_device_os] [nvarchar](max) NULL,
    [context_device_osVersion] [nvarchar](max) NULL,
    [context_device_locale] [nvarchar](max) NULL,
    [context_device_userAgent] [nvarchar](max) NULL,
    [context_device_browser] [nvarchar](max) NULL,
    [context_device_browserVersion] [nvarchar](max) NULL,
    [context_device_screenResolution_value] [nvarchar](max) NULL,
    [context_user_anonId] [nvarchar](max) NULL,
    [context_user_anonAcquisitionDate] [nvarchar](max) NULL,
    [context_user_authAcquisitionDate] [nvarchar](max) NULL,
    [context_user_accountAcquisitionDate] [nvarchar](max) NULL,
    [context_session_id] [nvarchar](max) NULL,
    [context_session_isFirst] [bit] NOT NULL,
    [context_operation_id] [nvarchar](max) NULL,
    [context_location_point_lat] [float] NOT NULL,
    [context_location_point_lon] [float] NOT NULL,
    [context_location_clientip] [nvarchar](max) NULL,
    [context_location_continent] [nvarchar](max) NULL,
    [context_location_country] [nvarchar](max) NULL,
    [context_location_province] [nvarchar](max) NULL,
    [context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


若要查看本示例的运行情况，请[下载](https://sesitai.codeplex.com/)完整的工作代码，更改 `app.config` 设置，并将辅助角色发布到 Azure。

## <a name="next-steps"></a>后续步骤

* [编写代码来直接访问遥测数据](http://dev.applicationinsights.io/)
* [使用辅助角色导出到 SQL](app-insights-code-sample-export-telemetry-sql-database.md)
* [Application Insights 中的连续导出](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)
* [导出数据模型](app-insights-export-data-model.md)
* [更多示例和演练](app-insights-code-samples.md)


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md





<!--HONumber=Jan17_HO4-->


