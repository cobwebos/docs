---
title: 从一个实验中创建多个模型 | Microsoft Docs
description: 使用 PowerShell 创建多个具有相同算法和不同训练数据集的机器学习模型和 Web 服务终结点。
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.openlocfilehash: d8ab7151680546c9b4bf5aef8998e522a79d809c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>使用 PowerShell 从一个实验中创建多个机器学习模型和 Web 服务终结点
这是常见的机器学习问题：你需要创建具有相同训练工作流并使用相同算法的多个模型， 但是需要它们使用不同的训练数据集作为输入。 本文将演示如何使用单个实验在 Azure 机器学习工作室的范围中执行此操作。

例如，假设用户拥有全球自行车租赁特许经营业务。 想要生成回归模型以预测基于历史数据的租赁需求。 你在全世界有 1,000 个租赁位置，并为每个位置收集了一个数据集。 它们包括特定于每个位置的重要功能，例如日期、时间、天气、交通。

只要使用所有位置间全部数据集的合并版本，就可以训练模型。 但是，每个位置的环境都是唯一的。 因此，更好的方法是使用每个位置的数据集单独训练回归模型。 这样一来，每个训练的模型都可以考虑不同的存储大小、体积、地理位置、人口数量、自行车友好型交通环境等。

这可能是最好的方法，但是不想在 Azure 机器学习中创建 1,000 个训练实验，每一个都代表一个唯一位置。 除了是项艰巨的任务，它看起来效率也低，因为每个实验都具有全部相同的组件（除了训练数据集）。

幸运的是，可以通过使用 [Azure 机器学习重新训练 API](retrain-models-programmatically.md) 和 [Azure 机器学习 PowerShell](powershell-module.md) 使任务自动化来完成此操作。

> [!NOTE]
> 若要使示例运行更快，请将位置数从 1,000 减少到 10。 但是相同原则和过程可应用于 1,000 个位置。 但是，如果确实需要根据 1,000 个数据集进行训练，则可能需要并行运行以下 PowerShell 脚本。 本文不讨论如何执行此操作，但可在 Internet 上找到 PowerShell 多线程的示例。  
> 
> 

## <a name="set-up-the-training-experiment"></a>设置训练实验
请使用 [Cortana Intelligence 库](http://gallery.cortanaintelligence.com)中的示例[训练实验](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1)。 在 [Azure 机器学习工作室工作区](https://studio.azureml.net)中打开此实验。

> [!NOTE]
> 为了遵循此示例，可能需要使用标准工作区而不是免费工作区。 请为每个客户创建一个终结点（总共 10 个终结点），这需要一个标准工作区，因为免费工作区中最多只能有 3 个终结点。 如果只有一个免费工作区，只需将脚本更改为仅允许三个位置即可。
> 
> 

实验使用**导入数据**模块以从 Azure 存储帐户中导入训练数据集 *customer001.csv*。 假设已从所有自行车租赁位置中收集了训练数据集，并将其存储在相同的 Blob 存储位置中，文件名范围为 *rentalloc001.csv* 到 *rentalloc10.csv*。

![图像](./media/create-models-and-endpoints-with-powershell/reader-module.png)

请注意，**Web 服务输出**模块已添加到**训练模型**模块。
如果此实验部署为 Web 服务，那么与此输出关联的终结点以 .ilearner 文件的格式返回训练的模板。

另请注意，所设置的 Web 服务参数可定义“导入数据”模块使用的 URL。 这样即可使用参数来指定单个训练数据集，以便训练每个位置的模型。
也可通过其他方式来这样做。 可将 SQL 查询与 Web 服务参数结合使用，从 SQL Azure 数据库获取数据。 也可使用“Web 服务输入”模块将数据集传递到 Web 服务。

![图像](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

现在，请使用默认值 *rental001.csv* 作为训练数据集来运行此训练实验。 如果查看**评估**模块的输出（单击输出并选择“可视化”），则可以看到获得了不错的性能，即 *AUC* = 0.91。 此时，你已准备好部署超出此训练实验的 Web 服务。

## <a name="deploy-the-training-and-scoring-web-services"></a>部署训练和评分 Web 服务
要部署训练 Web 服务，可单击实验画布下的“设置 Web 服务”按钮，并选择“部署 Web 服务”。 调用此 Web 服务“自行车租赁训练”。

现在需要部署评分 Web 服务。
若要执行此操作，可单击画布下的“设置 Web 服务”，然后选择“预测 Web 服务”。 这会创建评分实验。
需要进行一些细微调整，使之可以作为 Web 服务使用。 请从输入数据中删除标签列“cnt”，使输出只能是实例 ID 和相应的预测值。

若要保存该工作，可以打开已准备好的库中的[预测实验](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1)。

要部署 Web 服务，请运行预测实验，并单击画布下的“部署 Web 服务”按钮。 将评分 Web 服务命名为“自行车租赁评分”。

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>使用 PowerShell 创建 10 个完全相同的 Web 服务终结点
此 Web 服务附带了一个默认终结点。 但是，你不会对此默认终结点感兴趣，因为它不能进行更新。 需要执行的操作是创建 10 个其他终结点，每个位置一个。 可以使用 PowerShell 执行此操作。

首先，请设置 PowerShell 环境：

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

现在已创建 10 个终结点，所有终结点都包含在 *customer001.csv* 上训练的同一已训练模型。 可以在 Azure 门户中查看它们。

![图像](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>更新终结点以通过 PowerShell 使用单独的训练数据集
下一步是使用在每个客户单独数据上唯一训练的模型来更新终结点。 但是，首先需要从**自行车租赁训练** Web 服务中生成这些模型。 让我们回到**自行车租赁训练** Web 服务。 需要使用 10 个不同的训练数据集调用其 BES 终结点 10 次，以便生成 10 个不同的模型。 请使用 **InovkeAmlWebServiceBESEndpoint** PowerShell cmdlet 来执行此操作。

还需要将 Blob 存储帐户的凭据提供到 `$configContent` 中。 也即提供到 `AccountName`、`AccountKey` 和 `RelativeLocation` 字段中。 `AccountName` 可以是帐户名称的一个，如 **Azure 门户**（“存储”选项卡）中所示。 单击存储帐户之后，可通过按底部的“管理访问键”按钮和复制“主访问键”来查找其 `AccountKey`。 `RelativeLocation`是相对于存储（其中存储了新模型）的路径。 例如，以下脚本中的 `hai/retrain/bike_rental/` 路径指向名为 `hai` 的容器，`/retrain/bike_rental/` 是子文件夹。 目前，不能通过门户 UI 创建子文件夹，但是有[几个 Azure 存储资源管理器](../../storage/common/storage-explorers.md)可允许这样做。 建议在存储中创建新的容器以存储新的训练模型（.iLearner 文件），如下所示：在存储页中，单击底部的“添加”按钮并将其命名为 `retrain`。 总之，对以下脚本进行的必要更改包括 `AccountName`、`AccountKey` 和 `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`)。

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

如上所示，与其构造 10 个不同的 BES 作业配置 json 文件，不如动态创建配置字符串， 然后将其馈送到 **InvokeAmlWebServceBESEndpoint** cmdlet 的 *jobConfigString* 参数。 确实无需在磁盘上保留副本。

如果一切顺利，一段时间后应该能在 Azure 存储帐户中看到 10 个 .iLearner 文件（从 *model001.ilearner* 到 *model010.ilearner*）。 现在已准备好使用 **Patch-AmlWebServiceEndpoint** PowerShell cmdlet 通过这些模型更新 10 个评分 Web 服务终结点。 再次提醒，只能修补之前以编程方式创建的非默认终结点。

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

这应以相当快的速度运行。 执行完以后，将会成功创建 10 个预测性的 Web 服务终结点。 每个终结点都会包含一个针对数据集特别训练过的模型（该数据集特定于租赁位置），全都来自单个训练实验。 若要验证这一点，可以使用 **InvokeAmlWebServiceRRSEndpoint** cmdlet 尝试调用这些终结点，为其提供相同的输入数据。 预期会看到不同的预测结果，因为这些模型是使用不同的训练集训练的。

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
