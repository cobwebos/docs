---
title: "使用数据工厂和 Batch 来处理大规模数据集 | Microsoft Docs"
description: "描述如何使用 Azure Batch 的并行处理功能在 Azure 数据工厂中处理大量数据。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: bb4188bed4839aea6d19c49a8f0e6d154a343ec1
ms.openlocfilehash: e0f77f88ee91b263c49a148197e418fdf64cca0b


---
# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>使用数据工厂和批处理来处理大规模数据集
本文介绍示例解决方案的体系结构，该解决方案通过计划的自动方式移动并处理大规模数据集。 它还提供使用 Azure 数据工厂和 Azure Batch 来实现此解决方案的端到端演练。

本文比一般文章更长，因为其中包含了整个示例解决方案的演练。 如果不熟悉 Batch 和数据工厂，可以了解这些服务以及如何将其结合使用。 如果对这些服务有所了解，并打算设计/构建解决方案，可只关注本文的[体系结构部分](#architecture-of-sample-solution)；如果打算开发原型或解决方案，那么可能也需要尝试[演练](#implementation-of-sample-solution)中的分步说明。 欢迎你对此内容进行评论以及说明你的使用情况。

首先来了解一下数据工厂和 Batch 服务如何帮助用户处理云中的大型数据集。     

## <a name="why-azure-batch"></a>为什么使用 Azure Batch？
Azure Batch 可帮助用户在云中有效运行大规模并行的高性能计算 (HPC) 应用程序。 它是一个平台服务，可以计划要在托管的虚拟机集合上运行的计算密集型工作，并且可以缩放计算资源以符合作业的需求。

在使用 Batch 服务时，可以定义用于大规模并行执行应用程序的 Azure 计算资源。 你可以根据需要或按计划运行作业，而不需要手动创建、配置和管理 HPC 群集、各个虚拟机、虚拟网络或复杂的作业和任务计划基础结构。

如果不熟悉 Azure Batch，请参阅以下文章，因为这有助于了解本文所述的解决方案体系结构/实现。   

* [Azure Batch 基础知识](../batch/batch-technical-overview.md)
* [批处理功能概述](../batch/batch-api-basics.md)

（可选）若要了解有关 Azure Batch 的详细信息，请参阅 [Azure Batch 的学习路径](https://azure.microsoft.com/documentation/learning-paths/batch/)。

## <a name="why-azure-data-factory"></a>为什么使用 Azure 数据工厂？
数据工厂是一项基于云的数据集成服务，可对数据移动和转换进行安排并使其实现自动化。 通过数据工厂服务，可创建托管的数据管道，将数据从本地和云数据存储移动到集中式数据存储（例如 Azure Blob 存储），并使用 Azure HDInsight 和 Azure 机器学习等服务处理/转换数据。 还可以安排数据管道以预定的方式（每小时、每天、每周等）运行，并可快速监视和管理管道，找出问题并采取行动。

如果不熟悉 Azure 数据工厂，请参阅以下文章，因为这有助于了解本文所述的解决方案体系结构/实现。  

* [Azure 数据工厂简介](data-factory-introduction.md)
* [生成第一个数据管道](data-factory-build-your-first-pipeline.md)   

（可选）若要了解有关 Azure 数据工厂的详细信息，请参阅 [Azure 数据工厂的学习路径](https://azure.microsoft.com/documentation/learning-paths/data-factory/)。

## <a name="data-factory-and-batch-together"></a>配合使用数据工厂和 Batch
数据工厂包含一些内置活动，例如“复制活动”（将数据从源数据存储复制/移动到目标数据存储）和“Hive 活动”（使用 Azure 上的 Hadoop 群集 (HDInsight) 处理数据）。 有关支持的转换活动列表，请参阅[数据转换活动](data-factory-data-transformation-activities.md)。

它还允许创建自定义 .NET 活动，用户可使用自己的逻辑移动或处理数据，并在 Azure HDInsight 群集或 VM 的 Azure Batch 池上运行这些活动。 使用 Azure Batch 时，可以根据你提供的公式，将池配置为自动缩放（基于工作负荷添加或删除 VM）。     

## <a name="architecture-of-sample-solution"></a>示例解决方案的体系结构
虽然本文介绍的体系结构针对简单解决方案，但它与复杂方案相关，例如金融服务的风险建模、图像处理和绘制以及染色体分析。

该图说明：1) 数据工厂如何安排数据移动和处理 2) Azure Batch 如何以并行方式处理数据。 下载并打印该图以便参考（11 x 17 英寸 或 A3 大小）：[HPC and data orchestration using Azure Batch and Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686)（使用 Azure Batch 和数据工厂的 HPC 和数据业务流程）。

[![大规模数据处理关系图](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

以下列表提供了该过程的基本步骤。 解决方案包含构建端到端解决方案的代码和说明。

1. **为 Azure Batch 配置计算节点池 (VM)**。 可指定节点数和每个节点的大小。
2. **创建 Azure 数据工厂实例** 该实例配置有代表 Azure Blob 存储、Azure Batch 计算服务、输入/输出数据和含有移动和转换数据的活动的工作流/管道的实体。
3. **在数据工厂管道中创建自定义 .NET 活动**。 此活动是在 Azure Batch 池上运行的用户代码。
4. **将大量输入数据存储为 Azure 存储中的 blob**。 数据被划分为逻辑扇区（通常按时间划分）。
5. **数据工厂将并行处理的数据复制**到第二个位置。
6. **数据工厂使用 Batch 分配的池运行自定义活动**。 数据工厂可同时运行多个活动。 每个活动处理一个数据切片。 结果存储在 Azure 存储中。
7. **数据工厂将最终结果移动至第三个位置**，通过应用将其分发或使用其他工具进行进一步处理。

## <a name="implementation-of-sample-solution"></a>示例解决方案的实现
示例解决方案进行了刻意地简化，以演示如何配合使用数据工厂和 Batch 来处理数据集。 该解决方案对某个搜索词（“Microsoft”）在按时序排列的输入文件中出现的次数计数。 其将计数输出到输出文件。

**时间**：如果熟悉 Azure、数据工厂和 Batch 的基础知识，且已完成以下列出的先决条件，完成此解决方案估计需要 1-2 小时。

### <a name="prerequisites"></a>先决条件
#### <a name="azure-subscription"></a>Azure 订阅
如果没有 Azure 订阅，只需几分钟即可创建一个免费试用帐户。 请参阅[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

#### <a name="azure-storage-account"></a>Azure 存储帐户
在本教程中，将使用 Azure 存储帐户存储数据。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)。 示例解决方案使用 Blob 存储。

#### <a name="azure-batch-account"></a>Azure Batch 帐户
使用 [Azure 门户](http://manage.windowsazure.com/)创建 Azure Batch 帐户。 请参阅[创建和管理 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 请注意 Azure Batch 帐户名称和帐户密钥。 还可使用 [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) cmdlet 创建 Azure Batch 帐户。 请参阅 [Azure Batch PowerShell cmdlet 入门](../batch/batch-powershell-cmdlets-get-started.md)，详细了解如何使用此 cmdlet。

该示例解决方案使用 Azure Batch（通过 Azure 数据工厂管道间接使用）以并行方式在计算节点（托管的虚拟机集合）上处理数据。

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>虚拟机 (VM) 的 Azure Batch 池
创建至少有 2 个计算节点的 **Azure Batch 池**。

1. 在 [Azure 门户](https://portal.azure.com)中，单击左侧菜单中的“浏览”，然后单击“Batch 帐户”。
2. 选择 Azure Batch 帐户，打开“Batch 帐户”边栏选项卡。
3. 单击“池”磁贴。
4. 在“池”边栏选项卡中，单击工具栏上的“添加”按钮以添加池。
   1. 输入池的 ID（“池 ID”）。 请记下“池的 ID”；创建数据工厂解决方案时需要使用。
   2. 为操作系统系列设置指定 **Windows Server 2012 R2**。
   3. 选择**节点定价层**。
   4. 输入 **2** 作为“目标专用”设置的值。
   5. 输入 **2** 作为“每个节点最大任务”设置的值。
   6. 单击“确定”创建池。

#### <a name="azure-storage-explorer"></a>Azure 存储空间资源管理器
[Azure 存储资源管理器 6（工具）](https://azurestorageexplorer.codeplex.com/)或 [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)（来自 ClumsyLeaf 软件）。 可使用这些工具检查和更改 Azure 存储项目中的数据，包括云托管应用程序的日志。

1. 创建名为 **mycontainer** 的容器，此容器具有专用访问权限（无匿名访问权限）
2. 如果正在使用 **CloudXplorer**，按以下结构创建文件夹和子文件夹：

   ![](./media/data-factory-data-processing-using-batch/image3.png)

   **Inputfolder** 和 **outputfolder** 是 **mycontainer** 中的顶级文件夹，且 **inputfolder** 含有具有日期时间戳 (YYYY-MM-DD-HH) 的子文件夹。

   如果正在使用 **Azure 存储资源管理器**，则下一步需要上传名称为 inputfolder/2015-11-16-00/file.txt、inputfolder/2015-11-16-01/file.txt 等的文件。 此步骤会自动创建文件夹。
3. 在计算机上创建一个文本文件 **file.txt**，其内容含有关键字 **Microsoft**。 例如：“test custom activity Microsoft test custom activity Microsoft”。
4. 将该文件上传到 Azure Blob 存储中的以下输入文件夹。

   ![](./media/data-factory-data-processing-using-batch/image4.png)

   如果正在使用 **Azure 存储资源管理器**，请将 **file.txt** 文件上传到 **mycontainer**。 单击工具栏上的“复制”，创建 blob 副本。 在“复制 Blob”对话框中，将“目标 blob 名”更改为“inputfolder/2015-11-16-00/file.txt”**.**。 重复此步骤，创建 inputfolder/2015-11-16-01/file.txt、inputfolder/2015-11-16-02/file.txt、inputfolder/2015-11-16-03/file.txt、inputfolder/2015-11-16-04/file.txt 等。 此操作会自动创建文件夹。
5. 创建名为 **customactivitycontainer** 的另一容器。 将自定义活动 zip 文件上传至此容器。

#### <a name="visual-studio"></a>Visual Studio
安装 Microsoft Visual Studio 2012 或更高版本，创建用于数据工厂解决方案的自定义 Batch 活动。

### <a name="high-level-steps-to-create-the-solution"></a>创建解决方案的高级步骤
1. 创建包含数据处理逻辑的自定义活动。
2. 创建使用该自定义活动的 Azure 数据工厂：

### <a name="create-the-custom-activity"></a>创建自定义活动
数据工厂自定义活动是此示例解决方案的核心。 该示例解决方案使用 Azure Batch 运行自定义活动。 请参阅 [Use custom activities in an Azure Data Factory pipeline](data-factory-use-custom-activities.md)（在 Azure 数据工厂管道中使用自定义活动），获取开发自定义活动并在 Azure 数据工厂管道中使用活动的基本信息。

若要创建可用于 Azure 数据工厂管道的 .NET 自定义活动，需创建 **.NET 类库**项目，其中具有实现 **IDotNetActivity** 接口的类。 此接口只包含一个方法：**Execute**。 以下是该方法的签名：

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

需要了解此方法的几个关键部分。

* 该方法采用四个参数：

  1. **linkedServices**。 将输入/输出数据源（例如：Azure Blob 存储）链接到数据工厂的链接服务的可枚举列表。 在此示例中，仅有一个 Azure 存储类型的链接服务可同时用于输入和输出。
  2. **数据集**。 这是数据集的可枚举列表。 可使用此参数获取输入和输出数据集定义的位置和架构。
  3. **活动**。 此参数表示当前计算实体，在此示例中表示 Azure Batch 服务。
  4. **记录器**。 通过记录器，可编写作为管道的“用户”日志的调试注释。
* 该方法返回一个字典，此字典可在以后将自定义活动链接在一起。 此功能尚未实现，因此该方法将返回空字典。

#### <a name="procedure-create-the-custom-activity"></a>过程：创建自定义活动
1. 在 Visual Studio 中创建 .NET 类库项目。

   1. 启动 **Visual Studio 2012**/**2013/2015**。
   2. 单击“文件”，指向“新建”并单击“项目”。
   3. 展开“模板”，然后选择“Visual C\#”。在此演练中使用的是 C\#，但也可使用任意 .NET 语言开发自定义活动。
   4. 从右侧项目类型列表中选择“类库”。
   5. 对于“名称”，输入 **MyDotNetActivity**。
   6. 对于“位置”，选择“C:\\ADFGetStarted”。 如果不存在，则创建 **ADF** 文件夹。
   7.  。
2. 单击“工具”，指向“NuGet 包管理器”，然后单击“包管理器控制台”。
3. 在“包管理器控制台”中，执行以下命令，导入 **Microsoft.Azure.Management.DataFactories**。

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. 将 **Azure 存储** NuGet 包导入项目。 因为此示例中使用了 Blob 存储 API，因此需要此包。

    ```powershell
    Install-Package Azure.Storage
    ```
5. 将以下 **using** 指令添加到项目中的源文件。

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. 将**命名空间**名称更改为 **MyDotNetActivityNS**。

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. 将类名称更改为 **MyDotNetActivity**，并从 **IDotNetActivity** 接口派生，如下所示。

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. 将 **IDotNetActivity** 接口的 **Execute** 方法实现（添加）到 **MyDotNetActivity** 类，并将以下示例代码复制到该方法。 请参阅 [Execute 方法](#execute-method)部分，了解此方法中使用的逻辑。

    ```csharp
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
    
       // declare types for input and output data stores
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // using First method instead of Single since we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
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
           // with the data slice.
           //
           // definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
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
    ```
9. 将以下帮助器方法添加到类。 通过 **Execute** 方法调用这些方法。 最重要的是，**Calculate** 方法隔离循环访问每个 blob 的代码。

    ```csharp
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
    ```
    **GetFolderPath** 方法返回数据集指向的文件夹路径，**GetFileName** 方法返回数据集指向的 blob/文件名称。

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    **Calculate** 方法计算输入文件（文件夹中的 blob）中关键字 **Microsoft** 的实例数。 搜索词（“Microsoft”）在代码中是硬编码。

1. 编译该项目。 在菜单中单击“生成”，然后单击“生成解决方案”。
2. 启动 **Windows 资源管理器**，并根据生成类型导航到 **bin\\debug** 或 **bin\\release** 文件夹。
3. 在 **\\bin\\Debug** 文件夹内创建包含所有二进制文件的 zip 文件 **MyDotNetActivity.zip**。 可能需要包括 MyDotNetActivity.**pdb** 文件以便获取其他详细信息，例如出现故障时引发问题的源代码中的行号。

   ![](./media/data-factory-data-processing-using-batch/image5.png)
4. 将 **MyDotNetActivity.zip** 作为 blob 上传到 blob 容器：**ADFTutorialDataFactory** 中 **StorageLinkedService** 链接服务使用的 Azure Blob 存储中的 **customactivitycontainer**。 创建 blob 容器 **customactivitycontainer**（如果不存在）。

#### <a name="execute-method"></a>Execute 方法
本部分提供有关 Excute 方法中代码的更多详细信息和说明。

1. 可在 [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) 命名空间中找到循环访问输入集合的成员。 需要使用 **BlobContinuationToken** 类循环访问 blob 集合。 实际上必须使用将标记作为退出循环机制的 do-while 循环。 有关详细信息，请参阅[如何通过 .NET 使用 Blob 存储](../storage/storage-dotnet-how-to-use-blobs.md)。 基本循环如下所示：

    ```csharp
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

    ```
   有关详细信息，请参阅 [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) 方法的文档。
2. 以逻辑方式通过 blob 集工作的代码在 do-while 循环内运行。 在 **Execute** 方法中，do-while 循环将 blob 列表传递给名为 **Calculate** 的方法。 该方法返回名为 **output** 的字符串变量，它是循环访问段中所有 blob 的结果。

   它将返回传递给 **Calculate** 方法的 blob 中的搜索词 (**Microsoft**) 出现次数。

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. **Calculate** 方法完成该工作后，它必须写入到新 blob 中。 因此需使用结果为处理的每个 blob 集编写新的 blob。 若要写入到新的 blob，首先需找到输出数据集。

    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. 该代码还调用帮助器方法：**GetFolderPath**，以检索文件夹路径（存储容器名称）。

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   **GetFolderPath** 将 DataSet 对象转换为 AzureBlobDataSet（具有 FolderPath 属性）。

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. 该代码调用 **GetFileName** 方法检索文件名称（blob 名称）。 此代码与获取文件夹路径的上述代码类似。

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. 通过创建 URI 对象写入文件名。 URI 构造函数使用 **BlobEndpoint** 属性返回容器名称。 添加文件夹路径和文件名称以构造输出 blob URI。  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. 已经写入文件名，现在可以将输出字符串从 **Calculate** 方法写入到新 blob：

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>创建数据工厂
在[创建自定义活动](#create-the-custom-activity)部分中，已创建了自定义活动，并已将具有二进制的 zip 文件和 PDB 文件上传到 Azure Blob 容器。 在本部分中，将通过使用**自定义活动**的**管道**创建 Azure **数据工厂**。

自定义活动的输入数据集表示 blob 存储内输入文件夹 (mycontainer\\inputfolder) 中的 blob（文件）。 自定义活动的输出数据集表示 blob 存储内输出文件夹 (mycontainer\\outputfolder) 中的输出 blob。

将一个或多个文件拖至输入文件夹：

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

例如，将含有以下内容的一个文件 (file.txt) 拖至每个文件夹。

```
test custom activity Microsoft test custom activity Microsoft
```

即使输入文件夹具有两个或两个以上文件，每个输入文件夹只对应于 Azure 数据工厂中的一个切片。 管道处理每个切片时，自定义活动将循环访问该切片的输入文件夹中的所有 blob。

会出现内容相同的五个输出文件。 例如，处理 2015-11-16-00 文件夹中文件后的输出文件含有如下内容：

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

如果将含有相同内容的多个文件（file.txt、file2.txt、file3.txt）拖至输入文件夹，输出文件夹中会出现如下内容。 即使文件夹有多个输入文件，每个文件夹（2015-11-16-00 等）只对应此示例中的一个切片。

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

此时输出文件有三行，每行对应于文件夹中与切片 (2015-11-16-00) 关联的输入文件 (blob)。

为每次活动运行创建任务。 在此示例中，管道中只有一个活动。 管道处理切片时，会在 Azure Batch 上运行自定义活动以处理该切片。 由于存在五个切片（每个切片可拥有多个 blob 或文件），所以在 Azure Batch 中创建了五个任务。 任务在 Batch 上运行时，实际上运行的是自定义活动。

以下演练提供了更多详细信息。

#### <a name="step-1-create-the-data-factory"></a>步骤 1：创建数据工厂
1. 登录到 [Azure 门户](https://portal.azure.com/)后，执行以下步骤：

   1. 单击左侧菜单上的“新建”。
   2. 单击“新建”边栏选项卡中的“数据 + 分析”。
   3. 单击“数据分析”边栏选项卡中的“数据工厂”。
2. 在“新建数据工厂”边栏选项卡中，输入 **CustomActivityFactory** 作为“名称”。 Azure 数据工厂的名称必须全局唯一。 如果收到错误：**数据工厂名称“CustomActivityFactory”不可用**，请更改数据工厂名称（例如改为 **yournameCustomActivityFactory**），并再次尝试创建。
3. 单击“资源组名称”，并选择现有资源组或创建资源组。
4. 验证正在使用的订阅，以及想要在其中创建数据工厂的区域是否正确。
5. 在“新建数据工厂”边栏选项卡中单击“创建”。
6. 此时可在 Azure 门户的“仪表板”中看到正在创建数据工厂。
7. 成功创建数据工厂后，将看到数据工厂页，其中显示了数据工厂的内容。

   ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>步骤 2：创建链接服务
链接服务将数据存储或计算服务链接到 Azure 数据工厂。 在此步骤中，将 **Azure 存储**帐户和 **Azure Batch** 帐户链接到数据工厂。

#### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务
1. 在 **CustomActivityFactory** 的“数据工厂”边栏选项卡上，单击“作者和部署”磁贴。 随即显示“数据工厂编辑器”。
2. 在命令栏上单击“新建数据存储”并选择“Azure 存储”。 在编辑器中，应会看到用于创建 Azure 存储链接服务的 JSON 脚本。

   ![](./media/data-factory-data-processing-using-batch/image7.png)

3. 将**帐户名**替换为 Azure 存储帐户名，将**帐户密钥**替换为 Azure 存储帐户的访问密钥。 若要了解如何获取存储访问密钥，请参阅 [View, copy and regenerate storage access keys](../storage/storage-create-storage-account.md#manage-your-storage-account)（查看、复制和重新生成存储访问密钥）。

4. 单击命令栏上的“部署”，部署链接服务。

   ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>创建 Azure Batch 链接服务
在此步骤中，为 **Azure Batch** 帐户创建链接服务，该帐户用于运行数据工厂自定义活动。

1. 在命令栏上单击“新建计算”并选择“Azure Batch”。 在编辑器中，应会出现用于创建 Azure Batch 链接服务的 JSON 脚本。
2. 在 JSON 脚本中：

   1. 将“帐户名称”替换为 Azure Batch 帐户的名称。
   2. 将“访问密钥”替换为 Azure Batch 帐户的访问密钥。
   3. 对于 **poolName** 属性，输入池 ID**。** 对于此属性，可指定池名称或池 ID。
   4. 对于 **batchUri** JSON 属性，输入 batch URI。

      > [!IMPORTANT]
      > “Azure Batch 帐户边栏选项卡”中的 **URL** 采用以下格式：\<accountname\>.\<region\>.batch.azure.com。 对于 JSON 中的 **batchUri** 属性，需要在 URL 中**删除“accountname.”** 。 示例：`"batchUri": "https://eastus.batch.azure.com"`。
      >
      >

      ![](./media/data-factory-data-processing-using-batch/image9.png)

      对于 **poolName** 属性，也可指定池 ID 而非池名称。

      > [!NOTE]
      > 数据工厂服务不支持 Azure Batch 的按需选项，而对 HDInsight 支持。 在 Azure 数据工厂中只可使用自己的 Azure Batch 池。
      >
      >
   5. 对于 **linkedServiceName** 属性，指定 **StorageLinkedService**。 已在之前的步骤中创建此链接服务。 此存储用作文件和日志的临时区域。
3. 单击命令栏上的“部署”，部署链接服务。

#### <a name="step-3-create-datasets"></a>步骤 3：创建数据集
在此步骤中，创建表示输入和输出数据的数据集。

#### <a name="create-input-dataset"></a>创建输入数据集
1. 在数据工厂的“编辑器”中，单击工具栏上的“新建数据集”按钮，然后在下拉菜单中单击“Azure Blob 存储”。
2. 将右窗格中的 JSON 替换为以下 JSON 代码片段：

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
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
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    稍后在本演练中创建管道，开始时间：2015-11-16T00:00:00Z，结束时间：2015-11-16T05:00:00Z。 计划**按小时**生成数据，因此存在 5 个输入/输出切片（介于 **00**:00:00-\> **05**:00:00 之间）。

    输入数据集的**频率**和**间隔**设置为**小时**和 **1**，这意味着每小时皆可使用输入切片。

    以下是每个切片的开始时间，由上方 JSON 代码段中的 **SliceStart** 系统变量表示。

    | **切片** | **开始时间**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    使用切片开始时间 (**SliceStart**) 的年、月、日和小时部分计算 **folderPath**。 因此，输入文件夹按以下方式映射到切片。

    | **切片** | **开始时间**          | **输入文件夹**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. 单击工具栏上的“部署”，创建并部署 **InputDataset** 表。

#### <a name="create-output-dataset"></a>创建输出数据集
在此步骤中，创建 AzureBlob 类型的另一数据集，以表示输出数据。

1. 在数据工厂的“编辑器”中，单击工具栏上的“新建数据集”按钮，然后在下拉菜单中单击“Azure Blob 存储”。
2. 将右窗格中的 JSON 替换为以下 JSON 代码片段：

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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
    ```

    将为每个输入切片生成输出 blob/文件。 下面是每个切片的输出文件命名方式。 所有输出文件在一个输出文件夹中生成：**mycontainer\\outputfolder**。

    | **切片** | **开始时间**          | **输出文件**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    请记住输入文件夹（例如 2015-11-16-00）中的所有文件都是含有开始时间（2015-11-16-00）的切片的一部分。 处理此切片时，自定义活动将扫描每个文件，并在输出文件中生成具有搜索词（“Microsoft”）出现次数的行。 如果 2015-11-16-00 文件夹有三个文件，则 2015-11-16-00.txt 输出文件中存在三行。

1. 单击工具栏上的“部署”，创建并部署 **OutputDataset**。

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>步骤 4：创建和运行带自定义活动的管道
在此步骤中，创建含一个活动（之前创建的自定义活动）的管道。

> [!IMPORTANT]
> 如果尚未将 **file.txt** 上传到 blob 容器中的输入文件夹，请在创建管道之前上传。 在管道 JSON 中将 **IsPaused** 属性设置为 false，以便在超过**开始**日期后立即运行管道。
>
>

1. 在数据工厂编辑器中，单击命令栏上的“新建管道”。 如果未显示命令，请单击“...（省略号）”查看。
2. 将右窗格中的 JSON 替换为以下 JSON 脚本：

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   请注意以下几点：

   * 管道中仅包含一个活动，其类型为：**DotNetActivity**。
   * **AssemblyName** 设置为 DLL 的名称：**MyDotnetActivity.dll**。
   * **EntryPoint** 设置为 **MyDotNetActivityNS.MyDotNetActivity**。 在代码中其基本为 \<namespace\>.\<classname\>。
   * **PackageLinkedService** 设置为 **StorageLinkedService**，它指向包含自定义活动 zip 文件的 blob 存储。 如果对输入/输出文件和自定义活动 zip 文件使用不同的 Azure 存储帐户，则必须创建另一个 Azure 存储链接服务。 本文假定使用相同的 Azure 存储帐户。
   * **PackageFile** 设置为 **customactivitycontainer/MyDotNetActivity.zip**。 采用以下格式：\<containerforthezip\>/\<nameofthezip.zip\>。
   * 自定义活动采用 **InputDataset** 作为输入，采用 **OutputDataset** 作为输出。
   * 自定义活动的 **linkedServiceName** 属性指向 **AzureBatchLinkedService**它将告诉 Azure 数据工厂自定义活动需要在 Azure Batch 上运行。
   * **并发**设置十分重要。 如果使用默认值 1，即使 Azure Batch 池中存在 2 个或 2 个以上的计算节点，也只能逐个处理切片。 因此，无法利用 Azure Batch 的并行处理功能。 如果将**并发**设置为更高的值，比如说 2，这表示可同时处理 2 个切片（对应 Azure Batch 中的两个任务），此时 Azure Batch 池中的两个 VM 都得以利用。 因此，请将并发属性设置为合适的值。
   * 默认情况下，无论何时，一台 VM 上只执行一个任务（切片）。 原因在于，Azure Batch 池的**每台 VM 最大任务数**默认设置为 1。 一个先决条件是创建一个将此属性设置为 2 的池，这样可同时在一台 VM 上运行两个数据工厂切片。

    -   **isPaused** 属性默认设置为 false。 在此示例中管道会立即运行，因为切片从过去启动。 可将此属性设置为 true 以暂停管道，然后将其设置回 false 以重新启动。

    -   **开始**时间和**结束**时间相隔五小时，切片按小时生成，因此管道将生成五个切片。

1. 单击命令栏上的“部署”来部署管道。

#### <a name="step-5-test-the-pipeline"></a>步骤 5：测试管道
在此步骤中，将文件拖放到输入文件夹以测试管道。 首先，通过一个输入文件夹对应一个文件的方式来测试管道。

1. 在 Azure 门户的“数据工厂”边栏选项卡中，单击“图示”。

   ![](./media/data-factory-data-processing-using-batch/image10.png)
2. 在关系图视图中，双击输入数据集“InputDataset”。

   ![](./media/data-factory-data-processing-using-batch/image11.png)
3. 会出现已准备好五个切片的“InputDataset”边栏选项卡。 请注意每个切片的**切片开始时间**和**切片结束时间**。

   ![](./media/data-factory-data-processing-using-batch/image12.png)
4. 现在在“关系图视图”中，单击“OutputDataset”。
5. 如果已生成图示，则将显示&5; 个输出切片处于“就绪”状态。

   ![](./media/data-factory-data-processing-using-batch/image13.png)
6. 使用 Azure 门户查看与**切片**关联的**任务**，并查看每个切片在哪些 VM 上运行。 请参阅[数据工厂和 Batch 集成](#data-factory-and-batch-integration)部分了解详细信息。
7. 在 Azure Blob 存储中，**mycontainer** 的 **outputfolder** 中应有输出文件。

   ![](./media/data-factory-data-processing-using-batch/image15.png)

   应会看到五个输出文件，每个文件对应一个输入切片。 每个输出文件应包含与下方输出类似的内容：

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   下图说明了数据工厂切片如何映射到 Azure Batch 中的任务。 在此示例中，一个切片只运行一次。

   ![](./media/data-factory-data-processing-using-batch/image16.png)
8. 现在来看看文件夹中存在多个文件的情况。 创建文件：**file2.txt**、**file3.txt**、**file4.txt** 和 **file5.txt**，这些文件与 **2015-11-06-01** 文件夹中 file.txt 文件的内容相同。
9. 在输出文件夹中，**删除**输出文件 **2015-11-16-01.txt**。
10. 现在，在 **OutputDataset** 边栏选项卡中，右键单击将**切片开始时间**设置为 **2015/11/16 01:00:00 AM** 的切片，然后单击“运行”，重新运行/重新处理切片。 现在，该切片有五个文件而非一个文件。

    ![](./media/data-factory-data-processing-using-batch/image17.png)
11. 切片运行且其状态为**就绪**后，验证 blob 存储中 **mycontainer** 的 **outputfolder** 中的此切片的输出文件 (**2015-11-16-01.txt**) 中的内容。 该切片的每个文件都应有一个行。

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> 如果尝试使用五个输入文件前未删除输出文件 2015-11-16-01.txt，则会看到以前切片的一个行运行和当前切片的五个行运行。 默认情况下，内容追加到输出文件，如果内容已存在。
>
>

#### <a name="data-factory-and-batch-integration"></a>数据工厂和 Batch 集成
数据工厂服务在 Azure Batch 中创建一项作业，名称为：**adf-poolname:job-xxx**。

![Azure 数据工厂 - Batch 作业](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

为切片的每个活动运行创建作业中的任务。 如果有 10 个切片准备就绪待处理，在该作业中将创建10 个任务。 如果在池中有多个计算节点，则可以并行运行多个切片。 如果每个计算节点的最大任务设置为 > 1，则可以在同一计算上运行多个切片。

此示例中有五个切片，因此 Azure Batch 中有五个任务。 在 Azure 数据工厂内的管道 JSON 中将**并发**设置为 **5**，并在含 **2** 个 VM 的 Azure Batch 池中将**每个 VM 的最大任务数**设置为 **2**，则可快速运行任务（检查任务的开始和结束时间）。

使用 Azure 门户查看 Batch 作业及其与**切片**关联的任务，并查看每个切片在哪些 VM 上运行。

![Azure 数据工厂 - Batch 作业任务](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>调试管道
调试包括几种基本方法：

1. 如果输入切片未设置为**就绪**，请确认输入文件夹结构是否正确，file.txt 是否存在于输入文件夹中。

   ![](./media/data-factory-data-processing-using-batch/image3.png)
2. 在自定义活动的 **Execute** 方法中，使用 **IActivityLogger** 对象记录可帮助解决问题的信息。 记录的消息在 user\_0.log 文件中显示。

   在“OutputDataset”边栏选项卡中，单击切片以查看该切片的“数据切片”边栏选项卡。 可看到该切片的**活动运行**。 应看到该切片的一个活动运行。 如果在命令栏中单击“运行”，则可为相同切片启动另一个活动运行。

   单击活动运行时，将显示具有日志文件列表的“活动运行详细信息”边栏选项卡。 在 **user\_0.log** 文件中将显示记录的消息。 发生错误后，将显示 3 个活动运行，因为管道/活动 JSON 中的重试计数设置为 3。 单击活动运行时，将显示日志文件，可查看文件解决该错误。

   ![](./media/data-factory-data-processing-using-batch/image18.png)

   在日志文件列表中，单击“user-0.log”。 右侧面板中是使用 **IActivityLogger.Write** 方法的结果。

   ![](./media/data-factory-data-processing-using-batch/image19.png)

   检查 system-0.log 是否有任何系统错误消息和异常。

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. 将 **PDB** 文件包含在 zip 文件中，这样，在发生错误时，错误详细信息中会提供**调用堆栈**等信息。
4. 在 zip 文件中，自定义活动的所有文件必须在不包含任何子文件夹的**顶级**目录中。

   ![](./media/data-factory-data-processing-using-batch/image20.png)
5. 确保 **assemblyName** (MyDotNetActivity.dll)、**entryPoint**(MyDotNetActivityNS.MyDotNetActivity)、**packageFile** (customactivitycontainer/MyDotNetActivity.zip) 和 **packageLinkedService**（应指向包含 zip 文件的 Azure Blob 存储）已设置为正确的值。
6. 如果你解决了错误并想要重新处理切片，请在“OutputDataset”边栏选项卡中右键单击该切片，然后单击“运行”。

   ![](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Azure Blob 存储中会出现一个**容器**，名为：**adfjobs**。 不会自动删除此容器，但解决方案测试完成后，可安全删除该容器。 同样，数据工厂解决方案会创建一个 Azure Batch **作业**，其名为：**adf-\<pool ID/name\>:job-0000000001**。 测试完解决方案后，可按需删除此作业。
   >
   >
7. 自定义活动不使用包中的 **app.config** 文件。 因此，如果代码从配置文件读取任何连接字符串，其在运行时无效。 使用 Azure Batch 时的最佳做法是在 **Azure KeyVault** 中保存所有密钥，使用基于证书的服务主体来保护 keyvault，并将该证书分发到 Azure Batch 池。 然后，.NET 自定义活动可以在运行时从 KeyVault 访问机密。 此解决方案是一种通用解决方法，可以延伸到任何类型的密钥，而不仅仅是连接字符串。

    有一个更容易的解决方法（但非最佳做法）：可通过连接字符串设置创建 **Azure SQL 链接服务**，再创建使用该链接服务的数据集，并将作为虚拟输入数据集的数据集链接到自定义 .NET 活动。 然后，可访问自定义活动代码中的链接服务连接字符串，并且它应在运行时正常运行。  

#### <a name="extend-the-sample"></a>扩展该示例
可扩展此示例，深入了解 Azure 数据工厂和 Azure Batch 功能。 例如，若要在不同时间范围处理切片，请执行以下步骤：

1. 添加 **inputfolder** 中的以下子文件夹：2015-11-16-05、2015-11-16-06、201-11-16-07、2011-11-16-08、2015-11-16-09，并将输入文件置于这些文件夹中。 将管道结束时间从 `2015-11-16T05:00:00Z` 更改为 `2015-11-16T10:00:00Z`。 在“关系图视图”中，双击“InputDataset”，并确认输入切片是否就绪。 双击“OuptutDataset”，查看输出切片的状态。 如果其处于“就绪”状态，请检查输出文件的输出文件夹。
2. 增加或减少**并发**设置，了解其对解决方案性能的影响，尤其是对 Azure Batch 上进行的处理的影响。 （请参阅步骤 4：创建和运行管道，详细了解**并发**设置。）
3. 创建含较高/较低的**每个 VM 的最大任务数**的池。 若要使用创建的新池，更新数据工厂解决方案中的 Azure Batch 链接服务。 （请参阅步骤 4：创建和运行管道，详细了解**每个 VM 的最大任务数**设置。）
4. 创建带有**自动缩放**功能的 Azure Batch 池。 自动缩放 Azure 批处理池中的计算节点是指动态调整应用程序使用的处理能力。 例如，可以根据挂起任务的数量通过 0 专用的 VM 和自动缩放公式创建 Azure Batch 池：

   一次为每个挂起的任务分配一个 VM（例如：5 个挂起任务->&5; 个 VM）：
    
    ```
    pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
    $TargetDedicated = max(pendingTaskSampleVector);
    ```

   每次最多一个 VM，不考虑挂起的任务数：

    ```
    pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
    $TargetDedicated = (max(pendingTaskSampleVector)>0)?1:0;
    ```

   有关详细信息，请参阅 [Automatically scale compute nodes in an Azure Batch pool](../batch/batch-automatic-scaling.md)（自动缩放 Azure Batch 池中的计算节点）。

   如果池使用默认 [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)，则在运行自定义活动之前，Batch 服务可能需要 15-30 分钟准备 VM。  如果池使用其他 autoScaleEvaluationInterval，则 Batch 服务可能需要 autoScaleEvaluationInterval + 10 分钟。
5. 在示例解决方案中，**Excute** 方法调用 **Calculate** 方法，后者可处理输入数据切片，产生输出数据切片。 可编写自己的方法来处理输入数据，并将 Excute 方法中的 Calculate 方法调用替换为对你的方法的调用。

### <a name="next-steps-consume-the-data"></a>后续步骤：使用数据
处理数据后，可通过 **Microsoft Power BI** 等联机工具使用数据。 以下是有助于了解 Power BI 以及如何在 Azure 中使用 Power BI 的链接：

* [在 Power BI 中浏览数据集](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-data/)
* [Power BI Desktop 入门](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/)
* [Power BI 中的数据刷新](https://powerbi.microsoft.com/en-us/documentation/powerbi-refresh-data/)
* [Azure 和 Power BI - 基本概述](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>参考
* [Azure 数据工厂](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Azure 数据工厂服务简介](data-factory-introduction.md)
  * [Azure 数据工厂入门](data-factory-build-your-first-pipeline.md)
  * [在 Azure 数据工厂管道中使用自定义活动](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Azure Batch 基础知识](../batch/batch-technical-overview.md)
  * [Azure Batch 功能概述](../batch/batch-api-basics.md)
  * [在 Azure 门户中创建和管理 Azure Batch 帐户](../batch/batch-account-create-portal.md)
  * [Azure Batch Library .NET 入门](../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx



<!--HONumber=Jan17_HO3-->


