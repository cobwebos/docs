---
title: "以编程方式重新训练机器学习模型 | Microsoft Docs"
description: "了解如何以编程方式重新训练模型并更新 Web 服务以使用 Azure 机器学习中的最新训练模型。"
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: raymondl;garye;v-donglo
translationtype: Human Translation
ms.sourcegitcommit: cd32cb7d2247676795f345448960d547eafba084
ms.openlocfilehash: 9290258998f6756dce55a764e020fb3278c2f8a6


---
# <a name="retrain-machine-learning-models-programmatically"></a>以编程方式重新训练机器学习模型
在本演练中，你将学习如何使用 C# 和机器学习批处理执行服务以编程方式重新训练 Azure 机器学习 Web 服务。

重新训练模型之后，下面的演练显示了如何更新预测 Web 服务中的模型：

* 如果在机器学习 Web 服务门户中部署了经典 Web 服务，请参阅[重新训练经典 Web 服务](machine-learning-retrain-a-classic-web-service.md)。 
* 如果部署了新的 Web 服务，请参阅[使用机器学习管理 cmdlet 重新训练新 Web 服务](machine-learning-retrain-new-web-service-using-powershell.md)。

有关重新训练过程的概述，请参阅[重新训练机器学习模型](machine-learning-retrain-machine-learning-model.md)。

如果想从基于现有的新 Azure Resource Manager 的 Web 服务开始，请参阅[重新训练现有预测 Web 服务](machine-learning-retrain-existing-resource-manager-based-web-service.md)。

## <a name="create-a-training-experiment"></a>创建训练实验
对于此示例，你将使用来自 Microsoft Azure 机器学习示例中的“示例 5：训练、测试、评估二元分类：成人数据集”。 

若要创建此实验：

1. 登录 Microsoft Azure 机器学习工作室。 
2. 在仪表板的右下角底部，单击“**新建**”。
3. 从 Microsoft 示例中，选择示例 5。
4. 若要重命名此实验，在实验画布顶部选择实验名称“示例 5：训练，测试、评估二元分类：成人数据集”。
5. 键入“人口普查模型”。
6. 在实验画布的底部，单击“**运行**”。
7. 单击“设置 Web 服务”，然后选择“重新训练 Web 服务”。 

下图显示初始实验。
   
   ![初始实验。][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>创建预测实验并将其发布为 Web 服务
接下来创建预测实验。

1. 在实验画布的底部，单击“**设置 Web 服务**”，然后选择“**预测 Web 服务**”。 这会将该模型保存为训练模型，并将添加 Web 服务输入和输出模块。 
2. 单击“**运行**”。 
3. 实验运行完毕后，单击“部署 Web 服务 [经典]”或“部署 Web 服务 [全新] ”。

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>将训练实验部署为训练 Web 服务
若要重新训练已经训练的模型，必须部署作为重新训练 Web 服务而创建的训练实验。 此 Web 服务需要一个与*[训练模型][train-model]* 模块连接的 Web 服务输出模块，以便生成新的训练模型。

1. 若要返回到训练实验，单击左侧窗格中的“实验”图标，然后单击名为“人口普查模型”的实验。  
2. 在“搜索实验项目”的搜索框中，键入 Web 服务。 
3. 将 *Web 服务输入*模块拖到实验画布，然后将其输出连接到*清理缺失数据*模块。  这可确保重新训练数据的处理方式与原始训练数据相同。
4. 将两个 *Web 服务输出*模块拖到实验画布。 将*训练模型*模块的输出连接到一个画布，将*评估模型*模块的输出连接到另一个画布。 **训练模型**的 Web 服务输出为我们提供了新的训练模型。 附加到**评估模型**的输出将返回该模块的输出，这是性能结果。
5. 单击“**运行**”。 

接下来，必须将训练实验部署为 Web 服务，该服务将生成训练模型和模型评估结果 。 若要实现此目的，接下来的操作将依赖于是要使用经典 Web 服务还是新的 Web 服务。  

**经典 Web 服务**

在实验画布的底部，单击“**设置 Web 服务**”，然后选择“**部署 Web 服务 [经典]**”。 Web 服务**仪表板**将显示，其中显示了 API 密钥和用于 Batch 执行的 API 帮助页。 只有 Batch 执行方法才能用于创建训练模型。

**新 Web 服务**

在实验画布的底部，单击“**设置 Web 服务”**，然后选择“**部署 Web 服务 [新]”**。 Web 服务 Azure 机器学习 Web 服务门户可打开“部署 Web 服务”页。 为 Web 服务键入名称，并选择付款计划，然后单击“部署”。 只有 Batch 执行方法才能用于创建训练模型

在任一情况下，实验完成运行后，生成的工作流应如下所示：

![运行后生成的工作流。][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>使用 BES 通过新数据重新训练模型
对于此示例，正在使用 C# 创建重新培训的应用程序。 还可以使用 Python 或 R 示例代码来完成此任务。

若要调用重新训练 API：

1. 在 Visual Studio 中创建 C# 控制台应用程序（“新建”->“项目”->“Windows 桌面”->“控制台应用程序”）。
2. 登录到“机器学习 Web 服务”门户。
3. 如果正在使用经典 Web 服务，单击“经典 Web 服务”。
   1. 单击正在使用的 Web 服务。
   2. 单击默认终结点。
   3. 单击“**使用**”。
   4. 在“**使用**”页底部的“**示例代码**”部分中，单击“**Batch**”。
   5. 继续此过程的步骤 5。
4. 如果正在使用新的 Web 服务，单击“Web 服务”。
   1. 单击正在使用的 Web 服务。
   2. 单击“**使用**”。
   3. 在“使用”页底部的“**示例代码**”部分中，单击“**Batch**”。
5. 复制 Batch 执行的示例 C# 代码，并将其粘贴到 Program.cs 文件中，请确保命名空间保持不变。

按照注释中指定的方式添加 Nuget 包 Microsoft.AspNet.WebApi.Client。 若要将引用添加到 Microsoft.WindowsAzure.Storage.dll，可能首先需要为 Microsoft Azure 存储服务安装客户端库。 有关详细信息，请参阅 [Windows存储服务](https://www.nuget.org/packages/WindowsAzure.Storage)。

### <a name="update-the-apikey-declaration"></a>更新 apiKey 声明
定位到 **apikey** 声明。

    const string apiKey = "abc123"; // Replace this with the API key for the web service

在“**使用**”页的“**基本使用信息**”部分中，定位到主键，然后将其复制到 **apikey** 声明。

### <a name="update-the-azure-storage-information"></a>更新 Azure 存储信息
BES 示例代码将本地驱动器（例如，“C:\temp\CensusIpnput.csv”）中的文件上传到 Azure 存储，对其进行处理，并将结果写回 Azure 存储。  

若要完成此任务，必须从经典 Azure 门户和代码的更新对应值中，检索用于你的存储帐户的存储帐户名称、密钥和容器信息。 

1. 登录到经典 Azure 门户。
2. 在左侧导航列中，单击“**存储**”。
3. 从存储帐户列表中，选择一个来存储重新训练模型。
4. 在该页的底部，单击“**管理访问密匙**”。
5. 复制并保存“**主访问密匙**”，然后关闭对话框。 
6. 在该页顶部，单击“**容器**”。
7. 选择现有容器或创建新的容器并保存名称。

定位到“*StorageAccountName*”、“*StorageAccountKey*”和“*StorageContainerName*”声明，然后更新从 Azure 门户保存的值。

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

还必须确保输入文件在代码中指定的位置上可用。 

### <a name="specify-the-output-location"></a>指定输出位置
在“请求有效负载”中指定输出位置时，*RelativeLocation* 中指定的文件的扩展必须指定为 ilearner。 

请参阅以下示例：

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> 根据你添加 Web 服务输出模块的顺序，输出位置的名称可能与本演练中的名称不同。 由于使用两个输出设置了此训练实验，结果将包含这两个输出的存储位置信息。  
> 
> 

![重新训练输出][6]

图 4：重新训练输出。

## <a name="evaluate-the-retraining-results"></a>评估重新训练的结果
运行应用程序时，输出包括 URL 和访问评估结果所需的 SAS 令牌。

通过组合 *output2*（如前面的重新培训输出图像中所示）的输出结果中的 *BaseLocation* 、 *RelativeLocation* 和 *SasBlobToken* 并在浏览器地址栏中粘贴完整的 URL，可以查看重新训练模型的性能结果。  

检查结果以确定最新重新训练的模型是否执行得很好，足以替换现有的模型。

从输出结果中复制 *BaseLocation*、*RelativeLocation* 和 *SasBlobToken*，你将在重新培训的过程中使用它们。

## <a name="next-steps"></a>后续步骤
如果单击“部署 Web 服务 [经典]”部署预测 Web 服务，请参阅[重新训练经典 Web 服务](machine-learning-retrain-a-classic-web-service.md)。

如果单击“部署 Web 服务 [全新]”部署预测 Web 服务，请参阅[使用机器学习管理 cmdlet 重新训练新 Web 服务](machine-learning-retrain-new-web-service-using-powershell.md)。

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/



<!--HONumber=Jan17_HO2-->


