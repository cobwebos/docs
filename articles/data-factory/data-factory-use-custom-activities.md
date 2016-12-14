---
title: "在 Azure 数据工厂管道中使用自定义活动"
description: "了解如何创建自定义活动并在 Azure 数据工厂管道中使用。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 2a04aa8a37ca73ee5a5231e049c572b307a5a1fc
ms.openlocfilehash: defb6869fb2abab369ac5fd9cb62de810c85e57f


---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>在 Azure 数据工厂管道中使用自定义活动
> [!div class="op_single_selector"]
> [Hive](data-factory-hive-activity.md)  
> [Pig](data-factory-pig-activity.md)  
> [MapReduce](data-factory-map-reduce.md)  
> [Hadoop 流式处理](data-factory-hadoop-streaming-activity.md)
> [机器学习](data-factory-azure-ml-batch-execution-activity.md)
> [存储过程](data-factory-stored-proc-activity.md)
> [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> [.NET 自定义](data-factory-use-custom-activities.md)
>
>

在 Azure 数据工厂管道中可使用两类活动。

* [数据移动活动](data-factory-data-movement-activities.md)，用于在[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)之间移动数据。
* [数据转换活动](data-factory-data-transformation-activities.md)，用于通过计算（如 Azure HDInsight、Azure 批处理和 Azure 机器学习）转换/处理数据。 例如：HDInsight Hive 和机器学习批处理执行。  

如果需要将数据移入/移出 Azure 数据工厂不支持的数据存储，需使用自己的数据移动逻辑创建自定义 .NET 活动，然后在管道中使用该活动。

同样，如果需要以数据工厂不支持的方式转换/处理数据，需使用自己的数据处理逻辑创建自定义活动，然后在管道中使用该活动。

> [!NOTE]
> 目前，数据管理网关仅支持数据工厂中的复制活动和存储的过程活动。 不能使用自定义活动中的网关访问本地数据源。
>
>

可使用 **Azure Batch** 服务或 **Azure HDInsight** 群集配置要运行的自定义 .NET 活动。   

以下演练提供有关创建自定义 .NET 活动和在管道中使用自定义活动的分步说明。 本演练使用 **Azure Batch** 链接服务。 若要改用 Azure HDInsight 链接服务，请创建 **HDInsight** 类型（如果正在使用自己的 HDInsight 群集）或 **HDInsightOnDemand** 类型（如果想通过数据工厂按需创建 HDInsight 群集）的链接服务，并在管道 JSON (**linkedServiceName**) 的活动部分中使用。 有关使用 Azure HDInsight 运行自定义活动的详细信息，请参阅[使用 Azure HDInsight 链接服务](#use-azure-hdinsight-linked-services)部分。

> [!IMPORTANT]
> 在 Visual Studio 中，将 4.5.2 版本的 .NET Framework 设置为 .NET 自定义活动项目的目标框架。 数据工厂不支持针对低于 4.5.2 的 .NET Framework 版本编译的自定义活动。   
>
>

## <a name="walkthrough"></a>演练
### <a name="prerequisites"></a>先决条件
* Visual Studio 2012/2013/2015
* 下载并安装 [Azure .NET SDK][azure-developer-center]

### <a name="azure-batch-prerequisites"></a>Azure 批处理先决条件
在本演练中，将 Azure Batch 用作计算资源运行自定义 .NET 活动。 了解 Azure Batch 服务的概述，请参阅 [Azure Batch 基础知识][batch-technical-overview]若要快速入门 Azure Batch 服务，请参阅[用于 .NET 的 Azure Batch 库入门][batch-get-started]。

若想使用此教程，需通过 VM 池创建 Azure Batch 帐户。 下面是相关步骤：

1. 使用 [Azure 门户](http://manage.windowsazure.com)创建 **Azure Batch 帐户**。 有关说明，请参阅[创建和管理 Azure Batch 帐户][batch-create-account]一文。 记下 Azure Batch 帐户名称和帐户密钥。

    还可以使用 [New-AzureBatchAccount][new-azure-batch-account] cmdlet 创建 Azure Batch 帐户。 有关使用此 cmdlet 的详细说明，请参阅[使用 Azure PowerShell 管理 Azure Batch 帐户][azure-batch-blog]。
2. 创建 **Azure Batch 池**。

   1. 在 [Azure 门户](https://portal.azure.com)中，单击左侧菜单中的“浏览”，然后单击“Batch 帐户”。
   2. 选择 Azure Batch 帐户以打开“Batch 帐户”边栏选项卡。
   3. 单击“池”磁贴。
   4. 在“池” 边栏选项卡中，单击工具栏上的“添加”按钮以添加池。
      1. 输入池的 ID（“池 ID”）。 请记下“池的 ID”；创建数据工厂解决方案时需要使用。
      2. 为操作系统系列设置指定 **Windows Server 2012 R2**。
      3. 选择**节点定价层**。
      4. 输入 **2** 作为“目标专用”设置的值。
      5. 输入 **2** 作为“每个节点最大任务”设置的值。
   5. 单击“确定”创建池。

      还可以使用 [New-AzureBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) cmdlet 创建 Azure Batch 池。     

### <a name="high-level-steps"></a>高级步骤
1. **创建自定义活动**以使用数据工厂管道。 此示例中的自定义活动包含数据转换/处理逻辑。
   1. 在 Visual Studio 中，创建 .NET 类库项目、添加处理输入数据的代码以及编译项目。    
   2. 压缩输出文件夹中的所有二进制文件和 PDB（可选）文件。    
   3. 将 zip 文件上传到 Azure blob 存储。 “创建自定义活动”部分中提供详细步骤。
2. **创建使用自定义活动的 Azure 数据工厂**：
   1. 创建 Azure 数据工厂。
   2. 创建链接服务。
      1. AzureStorageLinkedService：提供用于访问 blob 的存储凭据。
      2. AzureBatchLinkedService：将 Azure Batch指定为计算。
   3. 创建数据集。
      1. InputDataset：指定输入 blob 的存储容器和文件夹。
      2. OuputDataset：指定输出 blob 的存储容器和文件夹。
   4. 创建使用自定义活动的管道。
   5. 运行和测试管道。
   6. 调试管道。

## <a name="create-the-custom-activity"></a>创建自定义活动
若要创建 .NET 自定义活动，请使用实现 **IDotNetActivity** 接口的类创建 **.NET 类库**项目。 此接口仅包含一个方法：[Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx)，其签名是：

    public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)


该方法采用四个参数：

* **linkedServices**。 此属性是将输入/输出数据源（例如：Azure Blob 存储）链接到数据工厂的链接服务的可枚举列表。 在此示例中，仅有一个 Azure 存储类型的链接服务可同时用于输入和输出。
* **数据集**。 此属性是数据集的可枚举列表。 可使用此参数获取输入和输出数据集定义的位置和架构。
* **活动**。 此属性表示当前的计算实体 - 在此示例中，表示 Azure Batch。
* **记录器**。 通过此对象可编写作为管道的“用户”日志的调试注释。

该方法返回一个字典，此字典可在以后将自定义活动链接在一起。 此功能尚未实现，因此该方法将返回空字典。  

### <a name="procedure"></a>过程
1. 创建 **.NET 类库**项目。
   <ol type="a">
     <li>启动 <b>Visual Studio 2015</b> 或 <b>Visual Studio 2013</b> 或 <b>Visual Studio 2012</b>。</li>
     <li>单击“文件”，指向“新建”并单击“项目”。<b></b><b></b><b></b></li>
     <li>展开“模板”，然后选择“Visual C#”。<b></b><b></b> 此演练中使用的是 C#，但也可使用任何 .NET 语言开发自定义活动。</li>
     <li>从右侧项目类型列表中选择“类库”<b></b>。</li>
     <li>对于“名称”<b></b>，输入 <b>MyDotNetActivity</b>。</li>
     <li>对于“位置”<b></b>，选择“C:\ADFGetStarted”<b></b>。</li>
     <li><b></b> 。</li>
   </ol>
2. 单击“工具”****，指向“NuGet 包管理器”****，然后单击“包管理器控制台”****。
3. 在“包管理器控制台”中，执行以下命令导入 **Microsoft.Azure.Management.DataFactories**。

     Install-Package Microsoft.Azure.Management.DataFactories
4. 将 **Azure 存储** NuGet 包导入项目。

        Install-Package WindowsAzure.Storage -Version 4.3.0

    > [!NOTE]
    > 数据工厂服务启动器需要 4.3 版本 的 WindowsAzure.Storage。 如果在自定义活动项目中添加更高版本 Azure 存储程序集的引用，将在活动执行时出现错误。 若要解决此错误，请参阅 [Appdomain 隔离](#appdomain-isolation)部分。 
5. 将以下 **using** 语句添加到项目中的源文件。

        using System.IO;
        using System.Globalization;
        using System.Diagnostics;
        using System.Linq;

        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Runtime;

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
6. 将**命名空间**名称更改为 **MyDotNetActivityNS**。

        namespace MyDotNetActivityNS
7. 将类名称更改为 **MyDotNetActivity**，并从 **IDotNetActivity** 接口派生，如以下代码片段所示：

        public class MyDotNetActivity : IDotNetActivity
8. 将 **IDotNetActivity** 接口的 **Execute** 方法实现（添加）到 **MyDotNetActivity** 类，并将以下示例代码复制到该方法。

    以下示例计算与数据切片关联的每个 blob 中搜索词（“Microsoft”）的出现次数。

        /// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement.
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
        /// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {
            // to get extended properties (for example: SliceStart)
            DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
            string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

            // to log all extended properties            
            IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
            logger.Write("Logging extended properties if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same
            // Azure Storage linked service for input and output.
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString;

            // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);

                // Calculate method returns the number of occurrences of
                // the search term (“Microsoft”) in each blob associated
                   // with the data slice. definition of the method is shown in the next step.

                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file.
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // The dictionary can be used to chain custom activities together in the future.
            // This feature is not implemented yet, so just return an empty dictionary.  

            return new Dictionary<string, string>();
        }
9. 添加以下帮助器方法。 **Execute** 方法调用这些帮助器方法。 **GetConnectionString** 方法检索 Azure 存储连接字符串，**GetFolderPath** 方法检索 blob 位置。 最重要的是，**Calculate** 方法隔离循环访问每个 blob 的代码。

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.
        /// </summary>

        private static string GetFolderPath(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
        /// and prepares the output text that is written to the output blob.
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

    GetFolderPath 方法返回数据集所指向的文件夹路径，GetFileName 方法返回数据集指向的 blob/文件名称。 如果 havefolderPath 使用变量（如 {Year}、{Month}、{Day} 等）定义，该方法将返回实际字符串，不会将它们替换为运行时值。 有关访问 SliceStart、SliceEnd 等的详细信息，请参阅[访问扩展属性](#access-extended-properties)部分。    

            "name": "InputDataset",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService",
                "typeProperties": {
                    "fileName": "file.txt",
                    "folderPath": "adftutorial/inputfolder/",

    Calculate 方法计算输入文件（文件夹中的 blob ）中关键字 Microsoft 的实例数。 搜索词（“Microsoft”）在代码中是硬编码。
10. 编译该项目。 在菜单中单击“生成”，然后单击“生成解决方案”。

    > [!IMPORTANT]
    > 将 4.5.2 版本的 .NET Framework 作为项目的目标框架：右键单击项目，然后单击“属性”设置目标框架。 数据工厂不支持针对低于 4.5.2 的 .NET Framework 版本编译的自定义活动。
    >
    >
11. 启动 **Windows 资源管理器**，并根据版本类型导航到 **bin\debug** 或 **bin\release** 文件夹。
12. 在 <project folder>\bin\Debug 文件夹内创建包含所有二进制文件的 zip 文件 **MyDotNetActivity.zip**。 可能需要包括 **MyDotNetActivity.pdb** 文件以便获取其他详细信息，例如出现故障时引发问题的源代码中的行号。 在 zip 文件中，用于自定义活动的所有文件必须在不包含任何子文件夹的**顶级**目录中。

    ![二进制输出文件](./media/data-factory-use-custom-activities/Binaries.png)
13. 将 **MyDotNetActivity.zip** 作为 blob 上传到 blob 容器：**ADFTutorialDataFactory** 中 **AzureStorageLinkedService** 链接服务使用的 Azure blob 存储中的 **customactivitycontainer**。  创建 blob 容器 **customactivitycontainer**（如果不存在）。

> [!NOTE]
> 若将此 .NET 活动项目添加到包含数据工厂项目的 Visual Studio 中的解决方案内，并从数据工厂应用程序项目添加对 .NET 活动项目的引用，则无需执行手动创建 zip 文件并将其上传到 Azure blob 存储的最后两个步骤。 使用 Visual Studio 发布数据工厂实体时，发布过程将自动完成这些步骤。 若要了解如何使用 Visual Studio 创建和发布数据工厂实体，请参阅[使用 Visual Studio 生成第一个管道](data-factory-build-your-first-pipeline-using-vs.md)和[将数据从 Azure Blob 复制到 Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md)。  
>
>

### <a name="execute-method"></a>Execute 方法
本部分提供有关 **Execute** 方法中代码的更多详细信息和说明。

1. 可在 [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) 命名空间中找到循环访问输入集合的成员。 需要使用 **BlobContinuationToken** 类循环访问 blob 集合。 从根本上而言，必须使用带有令牌的 do-while 循环作为退出循环的机制。 有关详细信息，请参阅[如何通过 .NET 使用 Blob 存储](../storage/storage-dotnet-how-to-use-blobs.md)。 基本循环如下所示：

        // Initialize the continuation token.
        BlobContinuationToken continuationToken = null;
        do
        {   
            // Get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                      true,
                                      BlobListingDetails.Metadata,
                                      null,
                                      continuationToken,
                                      null,
                                      null);
            // Return a string derived from parsing each blob.
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
        } while (continuationToken != null);

    有关详细信息，请参阅 [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) 方法的文档。
2. 以逻辑方式通过 blob 集工作的代码在 do-while 循环内运行。 在 **Execute** 方法中，do-while 循环将 blob 列表传递给名为 **Calculate** 的方法。 该方法返回名为 **output** 的字符串变量，它是循环访问段中所有 blob 的结果。

   它将返回传递给 **Calculate** 方法的 blob 中的搜索词 (**Microsoft**) 出现次数。

         output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
3. **Calculate** 方法完成该工作后，它必须写入到新 blob 中。 因此需使用结果为处理的每个 blob 集编写新的 blob。 若要写入到新的 blob，首先需找到输出数据集。

         // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
         Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

         // Convert to blob location object.
         outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;
4. 该代码还调用帮助器方法：**GetFolderPath**，以检索文件夹路径（存储容器名称）。

         folderPath = GetFolderPath(outputDataset);

   **GetFolderPath** 将 DataSet 对象转换为 AzureBlobDataSet（具有 FolderPath 属性）。

         AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

         return blobDataset.FolderPath;
5. 该代码调用 **GetFileName** 方法检索文件名称（blob 名称）。  

         AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

         return blobDataset.FileName;
6. 通过创建 URI 对象写入文件名。 URI 构造函数使用 **BlobEndpoint** 属性返回容器名称。 添加文件夹路径和文件名称以构造输出 blob URI。  

         // Write the name of the file.
         Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
7. 已编写文件名称，现在可从 Calculate 方法中将输出字符串写入新 blob：

         // Create a blob and upload the output text.
         CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
         logger.Write("Writing {0} to the output blob", output);
         outputBlob.UploadText(output);

## <a name="create-the-data-factory-using-azure-portal"></a>使用 Azure 门户创建数据工厂
在**创建自定义活动**部分中，已创建了自定义活动，并已将具有二进制的 zip 文件和 PDB 文件上传到 Azure Blob 容器。 在本部分中，将通过使用**自定义活动**的**管道**创建 Azure **数据工厂**。

自定义活动的输入数据集表示 blob 存储内输入文件夹 (adftutorial\inputfolder) 中的 blob（文件）。 活动的输出数据集表示 blob 存储内输出文件夹 (adftutorial\outputfolder) 中的输出 blob。

创建具有以下内容的 **file.txt** 文件，并将其上传到 **adftutorial\inputfolder**（adftutorial 是 Azure blob 容器名称，inputfolder 是该容器中的文件夹名称）。

    test custom activity Microsoft test custom activity Microsoft

即使输入文件夹具有两个或两个以上文件，该文件夹也对应 Azure 数据工厂中的一个切片。 管道处理每个切片时，自定义活动将循环访问该切片的输入文件夹中的所有 blob。

在 adftutorial\output 文件夹中会看到一个输出文件，该文件包含一行或多行（与输入文件夹中的 blob 数量相同）：

    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


以下是本部分中要执行的步骤：

1. 创建**数据工厂**。
2. 用于 VM（自定义活动在其中运行）的 Azure Batch 池的**链接服务**，和保存输入/输出 blob 的 Azure 存储。
3. 输入和输出**数据集**，表示自定义活动的输入和输出。
4. 使用自定义活动的**管道**。
5. **数据工厂**。 将这些实体发布到 Azure 时创建。

> [!NOTE]
> 创建 **file.txt** 并将其上传到 blob 容器（如果执行进行此操作）。 请参阅上述说明。  
>
>

### <a name="step-1-create-the-data-factory"></a>步骤 1：创建数据工厂
1. 请在登录到 Azure 门户后执行以下步骤：
   1. 单击左侧菜单上的“新建”。
   2. 单击“新建”边栏选项卡中的“数据 + 分析”。
   3. 单击“数据分析”边栏选项卡中的“数据工厂”。
2. 在“新建数据工厂”边栏选项卡中，输入 **CustomActivityFactory** 作为“名称”。 Azure 数据工厂的名称必须全局唯一。 如果收到错误：**数据工厂名称“CustomActivityFactory”不可用**，请更改数据工厂名称（例如改为 **yournameCustomActivityFactory**），并再次尝试创建。
3. 单击“资源组名称”，并选择现有资源组或创建资源组。
4. 验证是否正在使用正确**订阅**，并验证想在其中创建数据工厂的**区域**是否正确。
5. 在“新建数据工厂”边栏选项卡中单击“创建”。
6. 此时可在 Azure 门户的“仪表板”中看到正在创建数据工厂。
7. 成功创建数据工厂后，将看到“数据工厂”边栏选项卡，其中显示数据工厂的内容。

### <a name="step-2-create-linked-services"></a>步骤 2：创建链接服务
链接服务将数据存储或计算服务链接到 Azure 数据工厂。 在此步骤中，将 Azure 存储帐户和 Azure Batch 帐户链接到数据工厂。

#### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务
1. 在 **CustomActivityFactory** 的“数据工厂”边栏选项卡上，单击“作者和部署”磁贴。 随即显示“数据工厂编辑器”。
2. 单击命令栏上的“新建数据存储”，并选择“Azure 存储”。 在编辑器中，应会看到用于创建 Azure 存储链接服务的 JSON 脚本。

3. 将**帐户名**替换为 Azure 存储帐户名，将**帐户密钥**替换为 Azure 存储帐户的访问密钥。 若要了解如何获取存储访问密钥，请参阅 [View, copy and regenerate storage access keys](../storage/storage-create-storage-account.md#manage-your-storage-account)（查看、复制和重新生成存储访问密钥）。

4. 单击命令栏上的“部署”，部署链接服务。

#### <a name="create-azure-batch-linked-service"></a>创建 Azure Batch 链接服务
1. 在数据工厂编辑器中，单击命令栏的“新建计算”，并从菜单中选择“Azure Batch”。
2. 对 JSON 脚本进行以下更改：

   1. 指定 **accountName** 属性的 Azure Batch 帐户名称。 **Azure Batch 帐户边栏选项卡**中的 **URL** 采用以下格式：http://**accountname**.region.batch.azure.com。 对于 JSON 中的 **batchUri** 属性，需要在 URL 中**删除“accountname.”**， 并对 **accountName** JSON 属性使用 **accountname**。
   2. 指定 **accessKey** 属性的 Azure Batch 帐户密钥。
   3. 指定作为 **poolName** 属性先决条件一部分所创建的池的名称。 也可指定池 ID 而非池名称。
   4. 指定 **batchUri** 属性的 Azure Batch URI。 请参阅上文中对 **accountName** 属性的注释。 示例：https://westus.batch.azure.com。  
   5. 指定 **linkedServiceName** 属性的 **AzureStorageLinkedService**。

           {
             "name": "AzureBatchLinkedService",
             "properties": {
               "type": "AzureBatch",
               "typeProperties": {
                 "accountName": "myazurebatchaccount",
                 "batchUri": "https://westus.batch.azure.com",
                 "accessKey": "<yourbatchaccountkey>",
                 "poolName": "myazurebatchpool",
                 "linkedServiceName": "AzureStorageLinkedService"
               }
             }
           }

       对于 **poolName** 属性，也可指定池 ID 而非池名称。

      > [!NOTE]
      > 数据工厂服务不支持 Azure Batch 的按需选项，而对 HDInsight 支持。 在 Azure 数据工厂中只可使用自己的 Azure Batch 池。
      >
      >

### <a name="step-3-create-datasets"></a>步骤 3：创建数据集
在此步骤中，创建表示输入和输出数据的数据集。

#### <a name="create-input-dataset"></a>创建输入数据集
1. 在数据工厂的“编辑器”中，单击工具栏上的“新建数据集”按钮，然后在下拉菜单中单击“Azure Blob 存储”。
2. 将右窗格中的 JSON 替换为以下 JSON 代码片段：

         {
             "name": "InputDataset",
             "properties": {
                 "type": "AzureBlob",
                 "linkedServiceName": "AzureStorageLinkedService",
                 "typeProperties": {
                     "folderPath": "adftutorial/customactivityinput/",
                     "format": {
                         "type": "TextFormat"
                     }
                 },
                 "availability": {
                     "frequency": "Hour",
                     "interval": 1
                 },
                 "external": true,
                 "policy": {}
             }
         }

   稍后在本演练中创建管道，开始时间：2015-11-16T00:00:00Z，结束时间：2015-11-16T05:00:00Z。 计划按小时生成数据，因此存在 5 个输入/输出切片（介于 **00**: 00:00 -> **05**:00:00 之间）。

   输入数据集的**频率**和**间隔**设置为**小时**和 **1**，这意味着每小时皆可使用输入切片。 在此示例中，它是 intputfolder 中的相同文件 (file.txt)。

   以下是每个切片的开始时间，在上面的 JSON 代码段中由 SliceStart 系统变量表示。
3. 单击工具栏上的“部署”，创建并部署 **InputDataset**。 确认编辑器标题栏中显示了“已成功创建表”消息。

#### <a name="create-an-output-dataset"></a>创建输出数据集
1. 在“数据工厂编辑器”中，单击“新建数据集”，然后单击命令栏中的“Azure Blob 存储”。
2. 将右窗格中的 JSON 脚本替换为以下 JSON 脚本：

        {
            "name": "OutputDataset",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService",
                "typeProperties": {
                    "fileName": "{slice}.txt",
                    "folderPath": "adftutorial/customactivityoutput/",
                    "partitionedBy": [
                        {
                            "name": "slice",
                            "value": {
                                "type": "DateTime",
                                "date": "SliceStart",
                                "format": "yyyy-MM-dd-HH"
                            }
                        }
                    ]
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }

     输出位置是 **adftutorial/customactivityoutput/**，输出文件名称是 yyyy-MM-dd-HH.txt，其中 yyyy-MM-dd-HH 是生成切片的年、月、日和小时。 有关详细信息，请参阅[开发人员参考][adf-developer-reference]。

    将为每个输入切片生成输出 blob/文件。 下面是每个切片的输出文件命名方式。 所有输出文件在一个输出文件夹中生成：**adftutorial\customactivityoutput**。

   | 切片 | 开始时间 | 输出文件 |
   |:--- |:--- |:--- |
   | 1 |2015-11-16T00:00:00 |2015-11-16-00.txt |
   | 2 |2015-11-16T01:00:00 |2015-11-16-01.txt |
   | 3 |2015-11-16T02:00:00 |2015-11-16-02.txt |
   | 4 |2015-11-16T03:00:00 |2015-11-16-03.txt |
   | 5 |2015-11-16T04:00:00 |2015-11-16-04.txt |

    请记住，输入文件夹中的所有文件都是具有上述开始时间的切片的一部分。 处理此切片时，自定义活动将扫描每个文件，并在输出文件中生成具有搜索词（“Microsoft”）出现次数的行。 如果输入文件夹中有三个文件，则每个小时切片的输出文件中将有三行：2015-11-16-00.txt、2015-11-16:01:00:00.txt 等。
3. 单击命令栏上的“部署”来部署 **OutputDataset**。

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>创建并运行使用自定义活动的管道
1. 在数据工厂编辑器中，单击命令栏上的“新建管道”。 如果未显示命令，请单击“...（省略号）”查看。
2. 将右窗格中的 JSON 替换为以下 JSON 脚本。

        {
          "name": "ADFTutorialPipelineCustom",
          "properties": {
            "description": "Use custom activity",
            "activities": [
              {
                "Name": "MyDotNetActivity",
                "Type": "DotNetActivity",
                "Inputs": [
                  {
                    "Name": "InputDataset"
                  }
                ],
                "Outputs": [
                  {
                    "Name": "OutputDataset"
                  }
                ],
                "LinkedServiceName": "AzureBatchLinkedService",
                "typeProperties": {
                  "AssemblyName": "MyDotNetActivity.dll",
                  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                  "PackageLinkedService": "AzureStorageLinkedService",
                  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                  "extendedProperties": {
                    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
                  }
                },
                "Policy": {
                  "Concurrency": 2,
                  "ExecutionPriorityOrder": "OldestFirst",
                  "Retry": 3,
                  "Timeout": "00:30:00",
                  "Delay": "00:00:00"
                }
              }
            ],
            "start": "2015-11-16T00:00:00Z",
            "end": "2015-11-16T05:00:00Z",
            "isPaused": false
          }
        }

    请注意以下几点：

   * **Concurrency** 设置为 **2**，以便在 Azure Batch 池中通过 2 个 VM 并行处理 2 个切片。
   * 在活动部分中包含一个活动，其类型为：**DotNetActivity**。
   * **AssemblyName** 设置为 DLL 的名称：**MyDotnetActivity.dll**。
   * **EntryPoint** 设置为 **MyDotNetActivityNS.MyDotNetActivity**。
   * **PackageLinkedService** 设置为 **AzureStorageLinkedService**，它指向包含自定义活动 zip 文件的 blob 存储。 如果对输入/输出文件和自定义活动 zip 文件使用不同的 Azure 存储帐户，则需创建另一个 Azure 存储链接服务。 本文假定使用相同的 Azure 存储帐户。
   * **PackageFile** 设置为 **customactivitycontainer/MyDotNetActivity.zip**。 采用以下格式：containerforthezip/nameofthezip.zip。
   * 自定义活动采用 **InputDataset** 作为输入，采用 **OutputDataset** 作为输出。
   * 自定义活动的 linkedServiceName 属性指向 **AzureBatchLinkedService**，它告知 Azure 数据工厂自定义活动需要在 Azure Batch VM 上运行。
   * **isPaused** 属性默认设置为 **false**。 在此示例中管道会立即运行，因为切片从过去启动。 可将此属性设置为 true 以暂停管道，然后将其设置回 false 以重新启动。
   * **start** 时间和 **end** 时间相隔 **5** 小时且切片按小时生成，因此管道将生成五个切片。
3. 单击命令栏上的“部署”来部署管道。

### <a name="monitor-the-pipeline"></a>监视管道
1. 在 Azure 门户的“数据工厂”边栏选项卡中，单击“图示”。

    ![图示磁贴](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. 现在在“图示”视图中，单击“OutputDataset”。

    ![图示视图](./media/data-factory-use-custom-activities/diagram.png)
3. 如果已生成图示，则将显示 5 个输出切片处于“就绪”状态。

   ![输出切片](./media/data-factory-use-custom-activities/OutputSlices.png)
4. 验证输出文件是否在 **adftutorial** 容器的 blob 存储中生成。

   ![自定义活动的输出][image-data-factory-ouput-from-custom-activity]
5. 打开输出文件，应看到如下所示的输出：

    在文件 inputfolder/2015-11-16-00/file.txt 中发现搜索词“Microsoft”出现 2 次。
6. 使用 [Azure 门户][azure-preview-portal] 或 Azure PowerShell cmdlet 监视数据工厂、管道和数据集。 可在日志（特别是 user-0.log）中看到来自自定义活动的代码中 **ActivityLogger** 的消息，可从门户或使用 cmdlet 下载该日志。

   ![下载自定义活动的日志][image-data-factory-download-logs-from-custom-activity]

有关监视数据集和管道的详细步骤，请参阅[监视和管理管道](data-factory-monitor-manage-pipelines.md)。      

### <a name="data-factory-and-batch-integration"></a>数据工厂和 Batch 集成
数据工厂服务在 Azure Batch 中创建一项作业，名称为：**adf-poolname:job-xxx**。

![Azure 数据工厂 - Batch 作业](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

为切片的每个活动运行创建任务。 如果有 10 个切片已准备好进行处理，则此作业中将创建10 个任务。 如果在池中有多个计算节点，则可以并行运行多个切片。 如果每个计算节点的最大任务设置为 > 1，则还可在同一计算上运行多个切片。

![Azure 数据工厂 - Batch 作业任务](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

下图说明 Azure 数据工厂和 Batch 任务之间的关系。

![数据工厂和 Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="debug-the-pipeline"></a>调试管道
调试包括几种基本方法：

1. 如果出现以下错误消息，请确认 CS 文件中的类名称是否与为管道 JSON 中的 **EntryPoint** 属性指定的名称匹配。 在上面的演练中，类的名称是：MyDotNetActivity，JSON 中的 EntryPoint 名称是：MyDotNetActivityNS.**MyDotNetActivity**。

         MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly

   如果名称匹配，请确认是否所有二进制文件都在 zip 文件的**根文件夹**中。 也就是说，打开 zip 文件时，应看到所有文件均在根文件夹中，而非在任何子文件夹中。   
2. 如果输入切片未设置为 **Ready**，请确认输入文件夹结构正确，并且 **file.txt** 存在于输入文件夹中。
3. 在自定义活动的 **Execute** 方法中，使用 **IActivityLogger** 对象记录可帮助解决问题的信息。 记录的消息将显示在用户日志文件（一个或多个文件，命名为 user-0.log、user-1.log、user-2.log 等）中。

   在“OutputDataset”边栏选项卡中，单击切片以查看该切片的“数据切片”边栏选项卡。 可看到该切片的**活动运行**。 应看到该切片的一个活动运行。 如果在命令栏中单击“运行”，将启动同一切片的另一个活动运行。

   单击活动运行时，将显示具有日志文件列表的“活动运行详细信息”边栏选项卡。 在 user_0.log 文件中将显示记录的消息。 发生错误后，将显示 3 个活动运行，因为管道/活动 JSON 中的重试计数设置为 3。 单击活动运行时，将显示日志文件，可查看文件解决该错误。

   在日志文件列表中，单击“user-0.log”。 右侧面板中是使用 **IActivityLogger.Write** 方法的结果。 如果未看到所有消息，请检查是否具有多个名为：user_1.log, user_2.log 等的日志文件。否则，代码可能在上一次记录消息后出现故障。

   还应检查 **system-0.log** 是否有任何系统错误消息和异常。
4. 将 **PDB** 文件包含在 zip 文件中，这样，在发生错误时，错误详细信息中会提供**调用堆栈**等信息。
5. 在 zip 文件中，用于自定义活动的所有文件必须在不包含任何子文件夹的**顶级**目录中。
6. 确保 **assemblyName** (MyDotNetActivity.dll)、**entryPoint**(MyDotNetActivityNS.MyDotNetActivity)、**packageFile** (customactivitycontainer/MyDotNetActivity.zip) 和 **packageLinkedService**（应指向包含 zip 文件的 Azure Blob 存储）已设置为正确的值。
7. 如果你解决了错误并想要重新处理切片，请在“OutputDataset”边栏选项卡中右键单击该切片，然后单击“运行”。
8. 如果出现以下错误，则表示正在使用 4.3.0 以上版本的 Azure 存储包。 数据工厂服务启动器需要 4.3 版本 的 WindowsAzure.Storage。 如果必须使用 Azure 存储程序集的更高版本，请参阅 [Appdomain 隔离](#appdomain-isolation)部分解决此问题。 

        Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 

    如果可以使用 4.3.0 版本的 Azure 存储包，请删除对 4.3.0 以上版本的 Azure.Storage 包的现有引用，并从 Nuget 包管理器控制台运行以下命令。 

       Install-Package WindowsAzure.Storage -Version 4.3.0

    生成项目。 从 bin\Debug 文件夹中删除 4.3.0 版本以上的 Azure.Storage 程序集。 使用二进制文件和 PDB 文件创建新的 zip 文件。 使用 blob 容器 (customactivitycontainer) 中的此文件替换旧的 zip 文件。 重新运行失败的切片（右击切片，然后单击“运行”）。   
8. 自定义活动不会使用包中的 **app.config** 文件，因此，如果代码从配置文件读取任何连接字符串，则代码将无法在运行时正常运行。 使用 Azure Batch 时的最佳做法是在 **Azure KeyVault** 中保存所有机密，使用基于证书的服务主体来保护 **keyvault**，并将该证书分发到 Azure Batch 池。 然后，.NET 自定义活动可以在运行时从 KeyVault 访问机密。 该解决方案是一种通用解决方法，可以延伸到任何类型的机密，而不仅仅是连接字符串。

   有一个更容易的解决方法（但非最佳做法）：可通过连接字符串设置创建 **Azure SQL 链接服务**，再创建使用该链接服务的数据集，并将作为虚拟输入数据集的数据集链接到自定义 .NET 活动。 然后，可访问自定义活动代码中的链接服务连接字符串，并且它应在运行时正常运行。  

## <a name="update-the-custom-activity"></a>更新自定义活动
如果要更新自定义活动的代码，请生成该代码，然后将包含新二进制文件的 zip 文件上传到 blob 存储。

## <a name="copymove-data"></a>复制/移动数据

复制活动可以将数据从**源**数据存储复制到**接收器**数据存储。 有关复制活动支持的作为源和接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。

如果需要将数据移入移/出**复制活动**不支持的数据存储，可以通过自己的逻辑使用数据工厂内的**自定义活动**来复制/移动数据。 请参阅 GitHub 上的 [HTTP 数据下载程序示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample)。  

## <a name="appdomain-isolation"></a>Appdomain 隔离
请参阅[跨 AppDomain 示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample)，其中演示如何为 Azure 数据工厂创建自定义 .NET 活动，该活动不受 Azure 数据工厂启动器使用的程序集版本的限制（例如，WindowsAzure.Storage v4.3.0、Newtonsoft.Json v6.0.x 等）。

## <a name="access-extended-properties"></a>访问扩展属性
可以声明活动 JSON 中的扩展属性，如下所示：

    "typeProperties": {
      "AssemblyName": "MyDotNetActivity.dll",
      "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
      "PackageLinkedService": "AzureStorageLinkedService",
      "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
      "extendedProperties": {
        "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
        "DataFactoryName": "CustomActivityFactory"
      }
    },


代码中有两个扩展属性：**SliceStart** 和 **DataFactoryName**。 SliceStart 的值基于 SliceStart 系统变量。 有关受支持的系统变量列表，请参阅[系统变量](data-factory-scheduling-and-execution.md#data-factory-functions-and-system-variables)。 DataFactoryName 的值硬编码为“CustomActivityFactory”。

若要访问 **Execute** 方法中的这些扩展属性，请使用如下类似代码：

    // to get extended properties (for example: SliceStart)
    DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
    string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

    // to log all extended properties                               
    IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
    logger.Write("Logging extended properties if any...");
    foreach (KeyValuePair<string, string> entry in extendedProperties)
    {
        logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
    }

## <a name="auto-scaling-feature-of-azure-batch"></a>Azure Batch 的自动缩放功能
还可以使用**自动缩放**功能创建 Azure Batch 池。 例如，可以根据挂起任务的数量通过 0 专用的 VM 和自动缩放公式创建 Azure Batch 池：

一次为每个挂起的任务分配一个 VM（例如：5 个挂起任务-> 5 个 VM）：

    pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
    $TargetDedicated = max(pendingTaskSampleVector);

每次最多一个 VM，不考虑挂起的任务数：

    pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
    $TargetDedicated = (max(pendingTaskSampleVector)>0)?1:0;

有关详细信息，请参阅[自动缩放 Azure Batch 池中的计算节点](../batch/batch-automatic-scaling.md)。

如果池使用默认 [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)，则在运行自定义活动之前，Batch 服务可能需要 15-30 分钟准备 VM。  如果池使用其他 autoScaleEvaluationInterval，则 Batch 服务可能需要 autoScaleEvaluationInterval + 10 分钟。

## <a name="use-azure-hdinsight-linked-services"></a>使用 Azure HDInsight 链接服务
在本演练中，使用 Azure Batch 计算运行自定义活动。 也可以使用自己的 HDInsight 群集或使用数据工厂创建按需 HDInsight 群集，并在 HDInsight 群集上运行自定义活动。 以下是用于使用 HDInsight 群集的高级步骤。  

1. 创建 Azure HDInsight 链接服务。   
2. 使用 HDInsight 链接服务代替管道 JSON 中的 **AzureBatchLinkedService**。

用户可能想要更改管道的**开始**和**结束**时间，以便通过 Azure HDInsight 服务测试方案。

#### <a name="create-azure-hdinsight-linked-service"></a>创建 Azure HDInsight 链接服务
Azure 数据工厂服务支持创建按需群集，并使用它处理输入，以生成输出数据。 也可使用自己的群集执行相同操作。 使用按需 HDInsight 群集时，将为每个切片创建群集。 但是，如果使用自己的 HDInsight 群集，群集将准备好立即处理切片。 因此，使用按需群集时，将不会像使用自己的群集时那么快看到输出数据。

> [!NOTE]
> 在运行时，.NET 活动的实例只在 HDInsight 群集中的一个辅助角色节点上运行；无法扩展为在多个节点上运行。 .NET 活动的多个实例可以在 HDInsight 群集的不同节点上并行运行。
>
>

##### <a name="to-use-an-on-demand-hdinsight-cluster"></a>使用按需 HDInsight 群集
1. 在 **Azure 门户**中，单击数据工厂主页中的“创作和部署”。
2. 在数据工厂编辑器中，单击命令栏中的“新建计算”，并从菜单中选择“按需 HDInsight 群集”。
3. 对 JSON 脚本进行以下更改：

   1. 对于 **clusterSize** 属性，指定 HDInsight 群集的大小。
   2. 有关 **timeToLive** 属性，指定在删除客户之前其可以处于空闲状态的时长。
   3. 对于 **version** 属性，指定想要使用的 HDInsight 版本。 如果要排除此属性，则使用最新版本。  
   4. 对于 **linkedServiceName**，请指定在入门教程中创建的 **AzureStorageLinkedService**。

           {
               "name": "HDInsightOnDemandLinkedService",
               "properties": {
                   "type": "HDInsightOnDemand",
                   "typeProperties": {
                       "clusterSize": 4,
                       "timeToLive": "00:05:00",
                       "osType": "Windows",
                       "linkedServiceName": "AzureStorageLinkedService",
                   }
               }
           }
4. 单击命令栏上的“部署”，部署链接服务。

##### <a name="to-use-your-own-hdinsight-cluster"></a>使用自己的 HDInsight 群集：
1. 在 **Azure 门户**中，单击数据工厂主页中的“创作和部署”。
2. 在“数据工厂编辑器”中，单击命令栏中的“新建计算”，并从菜单中选择“HDInsight cluster”。
3. 对 JSON 脚本进行以下更改：

   1. 对于 **clusterUri** 属性，请输入 HDInsight 的 URL。 例如：https://<clustername>.azurehdinsight.net/     
   2. 对于 **UserName** 属性，请输入有权访问 HDInsight 群集的用户名。
   3. 对于 **Password** 属性，请输入用户密码。
   4. 对于 **LinkedServiceName** 属性，请输入 **AzureStorageLinkedService**。 在入门教程中已创建此链接服务。
4. 单击命令栏上的“部署”，部署链接服务。

有关详细信息，请参阅[计算链接服务](data-factory-compute-linked-services.md)。

在**管道 JSON** 中，使用（按需或自己的）HDInsight链接服务：

    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "HDInsightOnDemandLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2015-11-16T00:00:00Z",
        "end": "2015-11-16T05:00:00Z",
        "isPaused": false
      }
    }

## <a name="examples"></a>示例
| 示例 | 自定义活动的功能 |
| --- | --- |
| [HTTP 数据下载程序](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample)。 |在数据工厂中使用自定义 C# 活动将数据从 HTTP 终结点下载到 Azure Blob 存储。 |
| [Twitter 情绪分析示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |调用 Azure ML 模型和进行观点分析、评分、预测等。 |
| [运行 R 脚本](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)。 |通过在已安装 R 的 HDInsight 群集上运行 RScript.exe 来调用 R 脚本。 |
| [跨 AppDomain .NET 活动](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |使用不同于数据工厂启动器使用的程序集版本 |

## <a name="see-also"></a>另请参阅
[Azure 数据工厂更新：使用 Azure Batch 执行 ADF 自定义 .NET 活动](https://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)。

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png



<!--HONumber=Nov16_HO3-->


