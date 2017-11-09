---
title: "从一个实验中创建多个模型 | Microsoft Docs"
description: "使用 PowerShell 创建多个具有相同算法和不同训练数据集的机器学习模型和 Web 服务终结点。"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: garye;haining
ms.openlocfilehash: cc938fdaa6843f7c9e974d9b88a9b682b4678493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>使用 PowerShell 从一个实验中创建多个机器学习模型和 Web 服务终结点
下面是常见的机器学习问题：想要创建具有相同训练工作流并使用相同算法的多个模型，但是具有不同的训练数据集作为输入。 本文将演示如何使用单个实验在 Azure 机器学习工作室的范围中执行此操作。

例如，假设用户拥有全球自行车租赁特许经营业务。 想要生成回归模型以预测基于历史数据的租赁需求。 在全球拥有 1,000 个租赁位置，并且已为每个位置收集了数据集，其中包括重要特征（如日期、时间、天气，以及特定于每个位置的交通量）。

只要使用所有位置间全部数据集的合并版本，就可以训练模型。 但是由于每个位置都具有唯一环境，更好的方法是使用每个位置的数据集单独训练回归模型。 这样一来，每个训练的模型都可以考虑不同的存储大小、体积、地理位置、人口数量、自行车友好型交通环境*等*。

这可能是最好的方法，但是不想在 Azure 机器学习中创建 1,000 个训练实验，每一个都代表一个唯一位置。 除了是项艰巨的任务，它看起来也非常低效，因为每个实验都具有全部相同的组件（除了训练数据集）。

幸运的是，可以通过使用 [Azure 机器学习重新训练 API](retrain-models-programmatically.md) 和使用 [Azure 机器学习 PowerShell](powershell-module.md) 使任务自动化来完成此操作。

> [!NOTE]
> 要使示例运行更快，可将位置数从 1,000 减少到 10。 但是相同原则和过程可应用于 1,000 个位置。 唯一的区别是，如果想要从 1,000 个数据集中训练，可能需要考虑并行运行以下 PowerShell 脚本。 本文不讨论如何执行此操作，但可在 Internet 上找到 PowerShell 多线程的示例。  
> 
> 

## <a name="set-up-the-training-experiment"></a>设置训练实验
将使用已在 [Cortana Intelligence 库](http://gallery.cortanaintelligence.com)中创建的示例[训练实验](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1)。 在 [Azure 机器学习工作室工作区](https://studio.azureml.net)中打开此实验。

> [!NOTE]
> 为了遵循此示例，可能需要使用标准工作区而不是免费工作区。 将为每个客户创建一个终结点（总共 10 个终结点），这会需要一个标准工作区，因为免费工作区中最多只能有 3 个终结点。 如果只有一个免费工作区，只需将下面的脚本修改为仅允许 3 个位置。
> 
> 

实验使用**导入数据**模块以从 Azure 存储帐户中导入训练数据集 *customer001.csv*。 假设已从所有自行车租赁位置中收集了训练数据集，并将其存储在相同的 Blob 存储位置中，文件名范围为 *rentalloc001.csv* 到 *rentalloc10.csv*。

![图像](./media/create-models-and-endpoints-with-powershell/reader-module.png)

请注意，**Web 服务输出**模块已添加到**训练模型**模块。
如果此实验部署为 Web 服务，那么与此输出关联的终结点以 .ilearner 文件的格式返回训练的模板。

还应注意的是，已为**导入数据**模块使用的 URL 设置了 Web 服务参数。 这允许使用参数以指定单个训练数据集来训练每个位置的模型。
还有其他方法可执行此操作，例如，使用具有 Web 服务参数的 SQL 查询以从 SQL Azure 数据库中获取数据，或只使用 **Web 服务输入**模块以从数据集中传递到 Web 服务。

![图像](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

现在，请使用默认值 *rental001.csv* 作为训练数据集来运行此训练实验。 如果查看**评估**模块的输出（单击输出并选择“可视化”），则可以看到获得了不错的性能 *AUC* = 0.91。 此时，我们已准备好部署超出此训练实验的 Web 服务。

## <a name="deploy-the-training-and-scoring-web-services"></a>部署训练和评分 Web 服务
要部署训练 Web 服务，可单击实验画布下的“设置 Web 服务”按钮，并选择“部署 Web 服务”。 调用此 Web 服务“自行车租赁训练”。

现在需要部署评分 Web 服务。
要执行此操作，可单击画布下的“设置 Web 服务”，并选择“预测 Web 服务”。 这会创建评分实验。
需要进行一些微调以使其作为 Web 服务来工作，例如，从输入数据中删除标签列“cnt”，以及将输出限制为仅限实例 ID 和相应的预测值。

若要保存此工作，只需打开已准备好的库中的[预测实验](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1)。

要部署 Web 服务，请运行预测实验，并单击画布下的“部署 Web 服务”按钮。 将评分 Web 服务命名为“自行车租赁评分”。

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>使用 PowerShell 创建 10 个完全相同的 Web 服务终结点
此 Web 服务附带了一个默认终结点。 但是，我们对此默认终结点不感兴趣，因为它不能进行更新。 需要执行的操作是创建 10 个其他终结点，每个位置一个。 将使用 PowerShell 执行此操作。

首先，设置 PowerShell 环境：

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

然后，运行以下 PowerShell 命令：

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

现在已创建 10 个终结点，所有终结点都包含在 *customer001.csv* 上训练的相同已训练模型。 可以在 Azure 管理门户中查看它们。

![图像](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>更新终结点以通过 PowerShell 使用单独的训练数据集
下一步是使用在每个客户单独数据上唯一训练的模型来更新终结点。 但是首先需要从**自行车租赁训练** Web 服务中生成这些模型。 让我们回到**自行车租赁训练** Web 服务。 需要使用 10 个不同的训练数据集调用其 BES 终结点 10 次，以便生成 10 个不同的模型。 将使用 **InovkeAmlWebServiceBESEndpoint** PowerShell cmdlet 来执行此操作。

还需要将 Blob 存储帐户的凭据提供到 `$configContent` 中，也就是说，字段 `AccountName`、`AccountKey` 和 `RelativeLocation` 中。 `AccountName` 可以是帐户名称的一个，如**经典 Azure 管理门户**（“存储”选项卡）中所示。 单击存储帐户之后，可通过按底部的“管理访问键”按钮和复制“主访问键”来查找其 `AccountKey`。 `RelativeLocation` 是相对于存储（其中存储了新模型）的路径。 例如，以下脚本中的 `hai/retrain/bike_rental/` 路径指向名为 `hai` 的容器，`/retrain/bike_rental/` 是子文件夹。 目前，不能通过门户 UI 创建子文件夹，但是有[几个 Azure 存储资源管理器](../../storage/common/storage-explorers.md)可允许这样做。 建议在存储中创建新的容器以存储新的训练模型（.ilearner 文件），如下所示：在存储页中，单击底部的“添加”按钮并将其命名为 `retrain`。 总之，对以下脚本进行的必要更改包括 `AccountName`、`AccountKey` 和 `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`)。

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> BES 终结点是此操作唯一支持的模式。 RRS 不能用于生成训练模型。
> 
> 

正如上面内容所示，除了构造 10 个不同的 BES 作业配置 JSON 文件，还动态创建了配置字符串，并将其馈送到 **InvokeAmlWebServceBESEndpoint** cmdlet 的 *jobConfigString* 参数，因为实际上无需在磁盘上保留副本。

如果一切顺利，一段时间后应该能在 Azure 存储帐户中看到 10 个 .ilearner 文件，其名称为 *model001.ilearner* 到 *model010.ilearner*。 现在已准备好使用 **Patch-AmlWebServiceEndpoint** PowerShell cmdlet 通过这些模型更新 10 个评分 Web 服务终结点。 再次提醒，只能修补之前以编程方式创建的非默认终结点。

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

这应以相当快的速度运行。 执行完成后，将成功创建 10 个预测 Web 服务终结点，每个包含一个在特定租赁位置数据集上唯一训练的已训练模型，全部来自单个训练实验。 要验证这一点，可尝试使用 **InvokeAmlWebServiceRRSEndpoint** cmdlet 调用这些终结点，并为其提供相同的输入数据，用户应该会看到不同的预测结果，因为模型是使用不同训练集进行的训练。

## <a name="full-powershell-script"></a>完整的 PowerShell 脚本
下面是完整源代码的列表：

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
