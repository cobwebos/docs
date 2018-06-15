---
title: 重新训练现有预测 Web 服务 | Microsoft Docs
description: 了解如何重新训练模型并更新 Web 服务以使用 Azure 机器学习中的最新训练模型。
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.openlocfilehash: d399c8c3a47d374549d7ea7815567d7b879b49c8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835294"
---
# <a name="retrain-an-existing-predictive-web-service"></a>重新训练现有预测 Web 服务
本文档介绍了以下方案的重新训练流程：

* 具有训练实验以及已部署为运营 Web 服务的预测实验。
* 具有想要预测 Web 服务用以执行评分的新数据。

> [!NOTE]
> 若要部署新的 Web 服务，必须对要部署 Web 服务的订阅拥有充分的权限。 有关详细信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。

若要开始使用现有 Web 服务和实验，需要遵循以下操作：

1. 更新模型。
   1. 修改训练实验以支持 Web 服务输入和输出。
   2. 将训练实验部署为重新训练的 Web 服务。
   3. 使用此训练实验 Batch 执行服务 (BES) 以重新训练模型。
2. 使用 Azure 机器学习 PowerShell cmdlet 更新预测实验。
   1. 登录到 Azure 资源管理器帐户。
   2. 获取 Web 服务定义。
   3. 将 Web 服务定义导出为 JSON。
   4. 将引用更新到 JSON 中的 iLearner blob。
   5. 将 JSON 导入到 Web 服务定义中。
   6. 使用新的 Web 服务定义更新 Web 服务。

## <a name="deploy-the-training-experiment"></a>部署训练实验
要将训练实验部署为重新训练的 Web 服务，必须将 Web 服务输入和输出添加到模型。 通过将“Web 服务输出”模块连接到实验的*[训练模型][train-model]* 模块，训练实验能够生成一个新的训练模型，可以在预测实验中使用它。 如果有“评估模型”模块，还可以附加 Web 服务输出，可将评估结果作为输出。

若要更新训练实验：

1. 将“Web 服务输入”模块连接到数据输入（例如，“清除缺失数据”模块）。 通常情况下，想要确保输入数据的处理方式与原始训练数据的处理方式相同。
2. 将“Web 服务输出”模块连接到“训练模型”模块的输出。
3. 如果具有“评估模型”模块，并想要输出评估结果，可将“Web 服务输出”模块连接到“评估模型”模块的输出。

运行实验。

接下来，必须将训练实验部署为 Web 服务，该服务将生成训练模型和模型评估结果。

在实验画布的底部，单击“设置 Web 服务”，并选择“部署 Web 服务 [全新]”。 Azure 机器学习 Web 服务门户可打开“部署 Web 服务”页。 为 Web 服务键入名称，选择付款计划，并单击“部署”。 只可将 Batch 执行方法用于创建训练模型。

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>使用 BES 通过新数据重新训练模型
对于此示例，我们使用 C# 创建重新训练的应用程序。 还可使用 Python 或 R 示例代码完成此任务。

若要调用重新训练 API：

1. 在 Visual Studio 中创建 C# 控制台应用程序：“新建” > “项目” > “Visual C#” > “Windows 经典桌面” > “控制台应用(.NET Framework)”。
2. 登录“机器学习 Web 服务”门户。
3. 单击正在使用的 Web 服务。
4. 单击“**使用**”。
5. 在“**使用**”页底部的“**示例代码**”部分中，单击“**Batch**”。
6. 复制 Batch 执行的示例 C# 代码，并将其粘贴到 Program.cs 文件。 请确保命名空间保持不变。

按照注释中指定的方式添加 NuGet 包 Microsoft.AspNet.WebApi.Client。 要将引用添加到 Microsoft.WindowsAzure.Storage.dll，可能首先需要[为 Azure 存储服务安装客户端库](https://www.nuget.org/packages/WindowsAzure.Storage)。

以下屏幕截图显示 Azure 机器学习 Web 服务门户中的“使用”页。

![使用页][1]

### <a name="update-the-apikey-declaration"></a>更新 apiKey 声明
定位 **apikey** 声明：

    const string apiKey = "abc123"; // Replace this with the API key for the web service

在“**使用**”页的“**基本使用信息**”部分中，定位到主键，并将其复制到 **apikey** 声明。

### <a name="update-the-azure-storage-information"></a>更新 Azure 存储信息
BES 示例代码将本地驱动器（例如，“C:\temp\CensusIpnput.csv”）中的文件上传到 Azure 存储、对其进行处理，并将结果写回 Azure 存储。

运行试验之后，生成的工作流应与以下内容类似：

![运行后生成的工作流][4]

1. 登录到 Azure 门户。
2. 在左侧导航栏中，单击“更多服务”，搜索“存储帐户”，然后选择它。
3. 从存储帐户列表中，选择一个来存储重新训练模型。
4. 在左侧导航栏中，单击“访问密钥”。
5. 复制并保存“主访问密钥”。
6. 在左侧导航栏中，单击“容器”。
7. 选择现有容器或创建新的容器并保存名称。

找到“StorageAccountName”、“StorageAccountKey”和“StorageContainerName”声明，然后更新从门户保存的值。

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

还必须确保输入文件在代码中指定的位置上可用。

### <a name="specify-the-output-location"></a>指定输出位置
在“请求有效负载”中指定输出位置时，在 *RelativeLocation* 中指定的文件的扩展必须指定为 `ilearner`。 请参阅以下示例：

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

以下是重新训练输出的示例：

![重新训练输出][6]

## <a name="evaluate-the-retraining-results"></a>评估重新训练结果
运行应用程序时，输出包括 URL 和访问评估结果所需的共享访问签名令牌。

通过组合 *output2*（如前面的重新培训输出图像中所示）的输出结果中的 *BaseLocation* 、 *RelativeLocation* 和 *SasBlobToken* 并在浏览器地址栏中粘贴完整的 URL，可以查看重新训练模型的性能结果。

检查结果以确定最新重新训练的模型是否执行得很好，足以替换现有的模型。

从输出结果中复制 *BaseLocation*、*RelativeLocation* 和 *SasBlobToken*。

## <a name="retrain-the-web-service"></a>重新训练 Web 服务
重新训练新的 Web 服务时，将更新预测 Web 服务定义以引用新的训练模型。 Web 服务定义是 Web 服务训练模型的内部表示形式，它不能直接进行修改。 请确保为预测实验而不是训练实验检索 Web 服务定义。

## <a name="sign-in-to-azure-resource-manager"></a>登录到 Azure 资源管理器
首先必须使用 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet 从 PowerShell 环境中登录到 Azure 帐户。

## <a name="get-the-web-service-definition-object"></a>获取 Web 服务定义对象
然后，通过调用 [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet 获取 Web 服务定义对象。

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

若要确定现有 Web 服务的资源组名称，请运行 Get-AzureRmMlWebService cmdlet 而不是任何参数，以显示订阅中的 Web 服务。 定位到 Web 服务，并查看其 Web 服务 ID。 资源组的名称是 ID 中的第四个元素，紧随 *resourceGroups* 元素之后。 在下面的示例中，资源组名称为 Default-MachineLearning-SouthCentralUS。

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

或者，若要确定现有 Web 服务的资源组名称，请登录 Azure Microsoft Azure 机器学习 Web 服务门户。 选择 Web 服务。 资源组名称是 Web 服务的 URL 的第五个元素，紧随 *resourceGroups* 元素之后。 在下面的示例中，资源组名称为 Default-MachineLearning-SouthCentralUS。

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>将 Web 服务定义对象导出为 JSON
要修改训练模型定义以使用新训练的模型，必须先使用 [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) cmdlet 将其导出到 JSON 格式的文件。

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>将引用更新到 iLearner blob
在资产中，定位到 [训练的模型]，使用 iLearner blob 的 URI 更新 *locationInfo* 节点中的 *URI* 值。 通过组合 BES 重新训练调用的输出结果中的 *BaseLocation* 和 *RelativeLocation* 生成 URI。

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>将 JSON 导入到 Web 服务定义对象
必须使用 [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) cmdlet 将修改的 JSON 文件转换回可用于更新预测实验的 Web 服务定义对象。

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>更新 Web 服务
最后，使用 [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) cmdlet 更新预测实验。

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
