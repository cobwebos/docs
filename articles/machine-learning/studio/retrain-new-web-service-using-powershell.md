---
title: "使用 PowerShell 重新训练新的 Azure 机器学习 Web 服务 | Microsoft 文档"
description: "了解如何使用机器学习管理 PowerShell cmdlet 以编程方式重新训练模型并更新 Web 服务，以在 Azure 中使用新的训练模型。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: 3953a398-6174-4d2d-8bbd-e55cf1639415
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 77bc78e7ed27f1566e5e5f6a3539c93c9aa73e2d
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="retrain-a-new-resource-manager-based-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>使用机器学习管理 PowerShell cmdlet 重新训练新的基于 Resource Manager 的 Web 服务
重新训练新的 Web 服务时，将更新预测 Web 服务定义以引用新的训练模型。  

## <a name="prerequisites"></a>先决条件
必须设置训练实验和预测实验，如[以编程方式重新训练机器学习模型](retrain-models-programmatically.md)中所示。 

> [!IMPORTANT]
> 预测实验必须部署为基于 Azure Resource Manager（全新）的机器学习 Web 服务。 若要部署新的 Web 服务，必须对要部署 Web 服务的订阅拥有充分的权限。 有关详细信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。 

有关部署 Web 服务的其他信息，请参阅[部署 Azure 机器学习 Web 服务](publish-a-machine-learning-web-service.md)。

此过程要求已安装 Azure 机器学习 Cmdlet。 有关安装机器学习 cmdlet 的信息，请参阅 MSDN 上的 [Azure 机器学习 Cmdlet](https://msdn.microsoft.com/library/azure/mt767952.aspx) 参考。

已从重新训练输出复制以下信息：

* BaseLocation
* RelativeLocation

可采取以下步骤：

1. 登录到 Azure Resource Manager 帐户。
2. 获取 Web 服务定义
3. 将 Web 服务定义导出为 JSON
4. 将引用更新到 JSON 中的 iLearner blob。
5. 将 JSON 导入到 Web 服务定义中
6. 使用新的 Web 服务定义更新 Web 服务

## <a name="sign-in-to-your-azure-resource-manager-account"></a>登录到 Azure Resource Manager 帐户
首先必须使用 [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet 从 PowerShell 环境内登录到 Azure 帐户。

## <a name="get-the-web-service-definition"></a>获取 Web 服务定义
然后，通过调用 [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet 获取 Web 服务。 Web 服务定义是 Web 服务训练模型的内部表示形式，它不能直接进行修改。 请确保为预测实验而不是训练实验检索 Web 服务定义。

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

若要确定现有 Web 服务的资源组名称，请运行 Get-AzureRmMlWebService cmdlet 而不是任何参数，以显示订阅中的 Web 服务。 定位到 Web 服务，并查看其 Web 服务 ID。 资源组的名称是 ID 中的第四个元素，紧随 *resourceGroups* 元素之后。 在下面的示例中，资源组名称为 Default-MachineLearning-SouthCentralUS。

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

或者，若要确定现有 Web 服务的资源组名称，请登录到 Microsoft Azure 机器学习 Web 服务门户。 选择 Web 服务。 资源组名称是 Web 服务的 URL 的第五个元素，紧随 *resourceGroups* 元素之后。 在下面的示例中，资源组名称为 Default-MachineLearning-SouthCentralUS。

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>将 Web 服务定义导出为 JSON
要将定义修改为训练的模型以使用重新训练的模型，首先必须使用 [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) cmdlet 将其导出到 JSON 格式的文件。

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>将引用更新到 JSON 中的 iLearner blob。
在资产中，定位到 [训练的模型]，使用 iLearner blob 的 URI 更新 *locationInfo* 节点中的 *URI* 值。 通过组合 BES 重新训练调用的输出结果中的 *BaseLocation* 和 *RelativeLocation* 生成 URI。 这会更新引用新训练的模型的路径。

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>将 JSON 导入到 Web 服务定义中
必须使用 [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) 将修改的 JSON 文件转换回可用于更新 Web 服务定义的 Web 服务定义。

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>使用新的 Web 服务定义更新 Web 服务
最后，可使用 [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) cmdlet 更新 Web 服务定义。

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>摘要
通过使用机器学习管理 PowerShell cmdlet，可以更新预测 Web 服务的训练模型，从而启用如下方案：

* 使用新数据定期重新训练模型。
* 向客户分配模型，以便他们可以使用自己的数据重新训练模型。


