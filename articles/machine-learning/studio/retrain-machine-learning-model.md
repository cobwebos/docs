---
title: 重新训练 web 服务
titleSuffix: ML Studio (classic) - Azure
description: 了解如何在 Azure 机器学习 Studio （经典）中更新 web 服务以使用新训练的机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 867d104b58980679dc815238fef14050e7d9e8c7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152850"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>重新训练和部署机器学习模型

重新训练是确保机器学习模型保持准确的一种方法，该方法基于最相关的可用数据。 本文说明如何重新训练和部署机器学习模型，作为 Studio （经典）中的新 web 服务。 如果要重新训练经典 Web 服务，请参阅[此操作说明文章](retrain-classic-web-service.md)。

本文假设你已部署了一个预测 Web 服务。 如果还没有预测性 web 服务，请[在此处了解如何部署 Studio （经典） web 服务。](deploy-a-machine-learning-web-service.md)

你将执行以下步骤来重新训练和部署机器学习新 Web 服务：

1. 部署**重新训练 Web 服务**
1. 使用**重新训练 Web 服务**来训练新模型
1. 将现有的**预测实验**更新为使用新模型

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>部署重新训练 Web 服务

使用重新训练 Web 服务，可以使用一组新的参数（例如新数据）来重新训练模型，并保存它供以后使用。 将 **Web 服务输出**连接到**训练模型**时，训练实验将输出一个新模型供你使用。

使用以下步骤来部署重新训练 Web 服务：

1. 将一个 **Web 服务输入**模块连接到你的数据输入。 通常情况下，想要确保输入数据的处理方式与原始训练数据的处理方式相同。
1. 将一个 **Web 服务输出**模块连接到**训练模型**的输出。
1. 如果你有一个**评估模型**模块，则可以连接一个 **Web 服务输出**模块来输出评估结果。
1. 运行实验。

    运行实验之后，生成的工作流应当类似于下图：

    ![生成的工作流](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    现在，将训练实验部署为重新训练 Web 服务，用于输出经过训练的模型和模型评估结果。

1. 在实验画布的底部，单击“设置 Web 服务”。
1. 选择“部署 Web 服务[新建]”。 Azure 机器学习 Web 服务门户可打开“部署 Web 服务”页。
1. 为 Web 服务键入名称，选择一个付款计划。
1. 选择“部署”。

## <a name="retrain-the-model"></a>重新训练模型

对于此示例，我们使用 C# 创建重新训练的应用程序。 还可使用 Python 或 R 示例代码完成此任务。

使用以下步骤来调用重新训练 API：

1. 在 Visual Studio 中创建 C# 控制台应用程序：“新建” > “项目” > “Visual C#” > “Windows 经典桌面” > “控制台应用(.NET Framework)”。
1. 登录“机器学习 Web 服务”门户。
1. 单击正在使用的 Web 服务。
1. 单击“**使用**”。
1. 在“**使用**”页底部的“**示例代码**”部分中，单击“**Batch**”。
1. 复制 Batch 执行的示例 C# 代码，并将其粘贴到 Program.cs 文件。 请确保命名空间保持不变。

按照注释中指定的方式添加 NuGet 包 Microsoft.AspNet.WebApi.Client。 要添加对 Microsoft.WindowsAzure.Storage.dll 的引用，可能需要安装 [Azure 存储服务的客户端库](https://www.nuget.org/packages/WindowsAzure.Storage)。

以下屏幕截图显示 Azure 机器学习 Web 服务门户中的“使用”页。

![使用页](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>更新 apiKey 声明

定位 **apikey** 声明：

    const string apiKey = "abc123"; // Replace this with the API key for the web service

在“使用”页的“基本使用信息”部分中，找到主密钥，并将其复制到 **apikey** 声明。

### <a name="update-the-azure-storage-information"></a>更新 Azure 存储信息

BES 示例代码将文件从本地驱动器（例如，“C:\temp\CensusInput.csv”）上传到 Azure 存储、对其进行处理，并将结果写回 Azure 存储。

1. 登录到 Azure 门户
1. 在左侧导航栏中，单击“更多服务”，搜索“存储帐户”，然后选择它。
1. 从存储帐户列表中，选择一个来存储重新训练模型。
1. 在左侧导航栏中，单击“访问密钥”。
1. 复制并保存“主访问密钥”。
1. 在左侧导航栏中，单击 " **blob**"。
1. 选择现有容器或创建新的容器并保存名称。

找到“StorageAccountName”、“StorageAccountKey”和“StorageContainerName”声明，然后更新从门户保存的值。

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

还必须确保输入文件在代码中指定的位置上可用。

### <a name="specify-the-output-location"></a>指定输出位置

在“请求有效负载”中指定输出位置时，在 *RelativeLocation* 中指定的文件的扩展必须指定为 `ilearner`。

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

下面是重新训练输出的示例：

![重新训练输出](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>评估重新训练结果

运行应用程序时，输出包括 URL 和访问评估结果所需的共享访问签名令牌。

通过组合 *output2* 的输出结果中的 *BaseLocation*、*RelativeLocation* 和 *SasBlobToken* 并在浏览器地址栏中粘贴完整的 URL，可以查看重新训练模型的性能结果。

检查结果以确定新训练的模型是否比现有模型的表现更好。

保存输出结果中的 *BaseLocation*、*RelativeLocation* 和 *SasBlobToken*。

## <a name="update-the-predictive-experiment"></a>更新预测实验

### <a name="sign-in-to-azure-resource-manager"></a>登录到 Azure 资源管理器

首先，使用[AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 从 PowerShell 环境中登录到 Azure 帐户。

### <a name="get-the-web-service-definition-object"></a>获取 Web 服务定义对象

接下来，通过调用[AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) Cmdlet 获取 Web 服务定义对象。

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

若要确定现有 web 服务的资源组名称，请在不使用任何参数的情况下运行 AzMlWebService cmdlet 以显示订阅中的 web 服务。 定位到 Web 服务，并查看其 Web 服务 ID。 资源组的名称是 ID 中的第四个元素，紧随 *resourceGroups* 元素之后。 在下面的示例中，资源组名称为 Default-MachineLearning-SouthCentralUS。

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

或者，若要确定现有 Web 服务的资源组名称，请登录 Azure Microsoft Azure 机器学习 Web 服务门户。 选择 Web 服务。 资源组名称是 Web 服务的 URL 的第五个元素，紧随 *resourceGroups* 元素之后。 在下面的示例中，资源组名称为 Default-MachineLearning-SouthCentralUS。

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>将 Web 服务定义对象导出为 JSON

若要修改定型模型的定义以使用新训练的模型，必须先使用[AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) cmdlet 将其导出到 JSON 格式的文件。

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>将引用更新到 iLearner blob

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

### <a name="import-the-json-into-a-web-service-definition-object"></a>将 JSON 导入到 Web 服务定义对象

使用[AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) cmdlet 将修改的 JSON 文件转换回可用于更新预测实验的 Web 服务定义对象。

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>更新 Web 服务

最后，使用[AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) cmdlet 更新预测实验。

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>后续步骤

若要了解有关如何管理 Web 服务或跟踪多个实验运行的详细信息，请参阅以下文章：

* [探索 Web 服务门户](manage-new-webservice.md)
* [管理实验迭代](manage-experiment-iterations.md)
