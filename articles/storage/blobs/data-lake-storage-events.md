---
title: 教程：实现数据湖捕获模式以更新 Azure Databricks Delta 表 | Microsoft Docs
description: 本教程介绍如何使用事件网格订阅、Azure 函数和 Azure Databricks 作业将数据行插入到存储在 Azure DataLake Storage Gen2 中的表。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.openlocfilehash: 03a07e70c967f92fe5dcc7c951aeea299b050405
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "71326990"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>教程：实现数据湖捕获模式以更新 Databricks Delta 表

本教程介绍如何处理使用分层命名空间的存储帐户中的事件。

你将生成一个小型解决方案，它可以让用户通过上传描述销售订单的逗号分隔值 (csv) 文件，来填充 Databricks Delta 表。 为了生成此解决方案，你要将事件网格订阅、Azure 函数和 Azure Databricks 中的[作业](https://docs.azuredatabricks.net/user-guide/jobs.html)连接到一起。

在本教程中，将：

> [!div class="checklist"]
> * 创建一个事件网格订阅用于调用 Azure 函数。
> * 创建一个 Azure 函数，用于接收事件中的通知，然后运行 Azure Databricks 中的作业。
> * 创建一个 Databricks 作业，用于将客户订单插入到存储帐户中的 Databricks Delta 表。

我们将从 Azure Databricks 工作区开始，按相反的顺序生成此解决方案。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 创建一个采用分层命名空间的存储帐户 (Azure Data Lake Storage Gen2)。 本教程使用名为 `contosoorders` 的存储帐户。 请确保你的用户帐户分配有[存储 Blob 数据参与者角色](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)。

  请参阅[创建 Azure Data Lake Storage Gen2 帐户](data-lake-storage-quickstart-create-account.md)。

* 创建服务主体。 请参阅[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

  在执行该文中的步骤时，需要完成一些特定的事项。

  :heavy_check_mark:执行该文中[将应用程序分配给角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)部分中的步骤时，请确保将“存储 Blob 数据参与者”  角色分配给服务主体。

  > [!IMPORTANT]
  > 请确保在 Data Lake Storage Gen2 存储帐户的范围内分配角色。 可以将角色分配给父资源组或订阅，但在这些角色分配传播到存储帐户之前，你将收到与权限相关的错误。

  :heavy_check_mark:执行本文中[获取用于登录的值](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)部分中的步骤时，请将租户 ID、应用 ID 和密码值粘贴到文本文件中。 稍后需要这些值。

## <a name="create-a-sales-order"></a>创建销售订单

首先创建一个描述销售订单的 csv 文件，然后将该文件上传到存储帐户。 稍后将使用此文件中的数据来填充 Databricks Delta 表中的第一行。

1. 打开 Azure 存储资源管理器。 然后，导航到你的存储帐户，并在“Blob 容器”部分创建一个名为 **data** 的新容器。 

   ![数据文件夹](./media/data-lake-storage-events/data-container.png "数据文件夹")

   有关如何使用存储资源管理器的详细信息，请参阅[使用 Azure 存储资源管理器管理 Azure Data Lake Storage Gen2 帐户中的数据](data-lake-storage-explorer.md)。

2. 在 **data** 容器中创建名为 **input** 的文件夹。

3. 在文本编辑器中粘贴以下文本：

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. 将此文件保存到本地计算机，并将其命名为 **data.csv**。

5. 在存储资源管理器中，将此文件上传到 **input** 文件夹。  

## <a name="create-a-job-in-azure-databricks"></a>在 Azure Databricks 中创建作业

在本部分，你将执行以下任务：

* 创建 Azure Databricks 工作区。
* 创建笔记本。
* 创建并填充 Databricks Delta 表。
* 添加用于将行插入 Databricks Delta 表的代码。
* 创建一个作业。

### <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

在本部分，使用 Azure 门户创建 Azure Databricks 工作区。

1. 在 Azure 门户中，选择“创建资源”   >   “分析” >   “Azure Databricks”。

    ![Azure 门户上的 Databricks](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Azure 门户上的 Databricks")

2. 在“Azure Databricks 服务”  下，提供所需的值以创建 Databricks 工作区。

    ![创建 Azure Databricks 工作区](./media/data-lake-storage-events/new-databricks-service.png "创建 Azure Databricks 工作区")

    创建工作区需要几分钟时间。 若要监视操作状态，请查看顶部的进度栏。

### <a name="create-a-spark-cluster-in-databricks"></a>在 Databricks 中创建 Spark 群集

1. 在 [Azure 门户](https://portal.azure.com)中转到所创建的 Azure Databricks 工作区，然后选择“启动工作区”。 

2. 随后将会重定向到 Azure Databricks 门户。 从门户中，选择“新建” > “群集”。  

    ![Azure 上的 Databricks](./media/data-lake-storage-events/databricks-on-azure.png "Azure 上的 Databricks")

3. 在“新建群集”页中，提供用于创建群集的值。 

    ![在 Azure 上创建 Databricks Spark 群集](./media/data-lake-storage-events/create-databricks-spark-cluster.png "在 Azure 上创建 Databricks Spark 群集")

    除以下值外，接受其他所有默认值：

    * 输入群集的名称。
    * 请务必选中“在不活动超过 120 分钟后终止”  复选框。 提供一个持续时间（以分钟为单位），如果群集在这段时间内一直未被使用，则会将其终止。

4. 选择“创建群集”。  群集运行后，可将笔记本附加到该群集，并运行 Spark 作业。

有关创建群集的详细信息，请参阅[在 Azure Databricks 中创建 Spark 群集](https://docs.azuredatabricks.net/user-guide/clusters/create.html)。

### <a name="create-a-notebook"></a>创建笔记本

1. 在左窗格中选择“工作区”  。 在**工作区**下拉列表中，选择**创建** > **笔记本**。

    ![在 Databricks 中创建笔记本](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "在 Databricks 中创建笔记本")

2. 在“创建 Notebook”对话框中，输入 Notebook 的名称。  选择“Python”作为语言，然后选择前面创建的 Spark 群集。 

    ![在 Databricks 中创建笔记本](./media/data-lake-storage-events/new-databricks-notebook.png "在 Databricks 中创建笔记本")

    选择“创建”  。

### <a name="create-and-populate-a-databricks-delta-table"></a>创建并填充 Databricks Delta 表

1. 在创建的笔记本中，将以下代码块复制并粘贴到第一个单元中，但暂时不要运行此代码。  

   请将此代码块中的 `appId`、`password` 和 `tenant` 占位符值替换为在完成本教程的先决条件时收集的值。

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    此代码将创建名为 **source_file** 的小组件。 稍后你将创建一个 Azure 函数，用于调用此代码并将文件路径传递到该小组件。  此代码还使用存储帐户对你的服务主体进行身份验证，并创建要在其他单元中使用的一些变量。

    > [!NOTE]
    > 在生产设置中，请考虑将身份验证密钥存储在 Azure Databricks 中。 然后，将查找密钥（而不是身份验证密钥）添加到代码块。 <br><br>例如，不使用这行代码：`spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")`，而是使用以下代码行：`spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`。 <br><br>完成了本教程之后，请参阅 Azure Databricks 网站上的 [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) 一文以查看此方法的示例。

2. 按 **SHIFT + ENTER** 键，运行此块中的代码。

3. 将以下代码块复制并粘贴到另一个单元中，然后按 **SHIFT + ENTER** 键运行此代码块中的代码。

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   此代码将在存储帐户中创建 Databricks Delta 表，然后从前面上传的 csv 文件中加载一些初始数据。

4. 成功运行此代码块后，请从笔记本中删除此代码块。

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>添加用于将行插入 Databricks Delta 表的代码

1. 将以下代码块复制并粘贴到另一个单元中，但不要运行此单元。

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   此代码使用 csv 文件中的数据将数据插入到临时表视图。 该 csv 文件的路径源自在前一步骤中创建的输入小组件。

2. 添加以下代码，以将临时表视图的内容与 Databricks Delta 表合并。

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>创建作业

创建一个作业来运行前面所创建的笔记本。 稍后你将创建一个 Azure 函数，用于在引发事件时运行此作业。

1. 单击“作业”。 

2. 在“作业”页中单击“创建作业”。  

3. 为作业命名，然后选择 `upsert-order-data` 工作簿。

   ![创建作业](./media/data-lake-storage-events/create-spark-job.png "创建作业")

## <a name="create-an-azure-function"></a>创建 Azure 函数

创建用于运行作业的 Azure 函数。

1. 在 Databricks 工作区的上角选择人像图标，然后选择“用户设置”。 

   ![管理帐户](./media/data-lake-storage-events/generate-token.png "用户设置")

2. 依次单击“生成新令牌”按钮、“生成”按钮。  

   确保将令牌复制到安全位置。 Azure 函数需要使用此令牌对 Databricks 进行身份验证，以便能够运行该作业。
  
3. 选择 Azure 门户左上角的“创建资源”按钮，然后选择“计算”>“函数应用”。  

   ![创建 Azure 函数](./media/data-lake-storage-events/function-app-create-flow.png "创建 Azure 函数")

4. 在函数应用的“创建”页中，请确保为运行时堆栈选择“.NET Core”，并确保配置 Application Insights 实例。  

   ![配置函数应用](./media/data-lake-storage-events/new-function-app.png "配置函数应用")

5. 在函数应用的“概述”页中，单击“配置”。  

   ![配置函数应用](./media/data-lake-storage-events/configure-function-app.png "配置函数应用")

6. 在“应用程序设置”页中，选择“新建应用程序设置”按钮以添加每个设置。  

   ![添加配置设置](./media/data-lake-storage-events/add-application-setting.png "添加配置设置")

   添加以下设置：

   |设置名称 | 值 |
   |----|----|
   |**DBX_INSTANCE**| Databricks 工作区的区域。 例如： `westus2.azuredatabricks.net`|
   |**DBX_PAT**| 前面生成的个人访问令牌。 |
   |**DBX_JOB_ID**|正在运行的作业的标识符。 在本例中，此值为 `1`。|
7. 在函数应用的概述页中，单击“新建函数”按钮。 

   ![新建函数](./media/data-lake-storage-events/new-function.png "新建函数")

8. 选择“Azure 事件网格触发器”。 

   根据提示安装 **Microsoft.Azure.WebJobs.Extensions.EventGrid** 扩展。 如果必须安装该扩展，则必须再次选择“Azure 事件网格触发器”来创建函数。 

   此时将显示“新建函数”窗格。 

9. 在“新建函数”窗格中，将函数命名为 **UpsertOrder**，然后单击“创建”按钮。  

10. 将代码文件的内容替换为此代码，然后单击“保存”按钮： 

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   此代码将分析有关引发的存储事件的信息，然后创建一条请求消息，其中包含触发了该事件的文件的 URL。 在该消息中，该函数会将一个值传递给前面创建的 **source_file** 小组件。 函数代码将该消息发送到 Databricks 作业，并使用前面获取的用于身份验证的令牌。

## <a name="create-an-event-grid-subscription"></a>创建事件网格订阅

在本部分，你将创建一个事件网格订阅，用于在将文件上传到存储帐户时调用 Azure 函数。

1. 在函数代码页中，单击“添加事件网格订阅”按钮。 

   ![新建事件订阅](./media/data-lake-storage-events/new-event-subscription.png "新建事件订阅")

2. 在“创建事件订阅”页中为订阅命名，然后使用页中的字段选择你的存储帐户。 

   ![新建事件订阅](./media/data-lake-storage-events/new-event-subscription-2.png "新建事件订阅")

3. 在“筛选事件类型”下拉列表中，选择“已创建 Blob”和“已删除 Blob”事件，然后单击“创建”按钮。    

## <a name="test-the-event-grid-subscription"></a>测试事件网格订阅

1. 创建名为 `customer-order.csv` 的文件，将以下信息粘贴到该文件中，并将其保存到本地计算机。

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. 在存储资源管理器中，将此文件上传到存储帐户的 **input** 文件夹。

   上传文件会引发 **Microsoft.Storage.BlobCreated** 事件。 事件网格会通知所有订阅者已发生该事件。 在本例中，Azure 函数是唯一的订阅者。 Azure 函数将分析事件参数以确定发生了哪个事件。 然后，它将该文件的 URL 传递给 Databricks 作业。 Databricks 作业将读取该文件，并将某个行添加到存储帐户中的 Databricks Delta 表。

3. 若要检查作业是否成功，请打开 Databricks 工作区，单击“作业”按钮，然后打开该作业。 

4. 选择作业以打开作业页。

   ![Spark 作业](./media/data-lake-storage-events/spark-job.png "Spark 作业")

   作业完成后，你会看到完成状态。

   ![已成功完成作业](./media/data-lake-storage-events/spark-job-completed.png "已成功完成作业")

5. 在新的工作簿单元中运行此查询，以查看更新的 Delta 表。

   ```
   %sql select * from customer_data
   ```

   返回的表将显示最新记录。

   ![最新记录显示在表中](./media/data-lake-storage-events/final_query.png "最新记录显示在表中")

6. 若要更新此记录，请创建名为 `customer-order-update.csv` 的文件，将以下信息粘贴到该文件中，然后将其保存到本地计算机。

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   除了订单数量从 `228` 更改为 `22` 外，此 CSV 文件几乎与前一文件相同。

7. 在存储资源管理器中，将此文件上传到存储帐户的 **input** 文件夹。

8. 再次运行 `select` 查询以查看更新后的增量表。

   ```
   %sql select * from customer_data
   ```

   返回的表将显示更新后的记录。

   ![更新后的记录显示在表中](./media/data-lake-storage-events/final_query-2.png "更新后的记录显示在表中")

## <a name="clean-up-resources"></a>清理资源

如果不再需要本文中创建的资源，可以删除资源组和所有相关资源。 为此，请选择存储帐户所在的资源组，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [响应 Blob 存储事件](storage-blob-event-overview.md)
