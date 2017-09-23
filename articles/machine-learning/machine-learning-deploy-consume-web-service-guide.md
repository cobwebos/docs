---
title: "Azure 机器学习 Web 服务：部署和使用 | Microsoft Docs"
description: "用于部署和使用 Web 服务的资源。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 18edabe267ec06c08074d7a7a6d71435cedc8489
ms.contentlocale: zh-cn
ms.lasthandoff: 06/07/2017

---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure 机器学习 Web 服务：部署和使用
可以使用 Azure 机器学习来部署机器学习工作流和模型作为 Web 服务。 然后，可以使用这些 Web 服务，通过 Internet 从应用程序调用机器学习模型，从而实时或者在批处理模式下进行预测。 由于 Web 服务是 RESTful，可以从各种编程语言和平台（如 .NET 和 Java）以及应用程序（如 Excel）调用它们。

接下来的部分提供指向演练、代码和文档的链接，可帮助你开始操作。

## <a name="deploy-a-web-service"></a>部署 Web 服务
### <a name="with-azure-machine-learning-studio"></a>使用 Azure 机器学习工作室
机器学习工作室和 Microsoft Azure 机器学习 Web 服务门户可帮助你部署和管理 Web 服务，而无需编写代码。

以下链接提供有关如何部署新 Web 服务的常规信息：

* 有关如何部署基于 Azure Resource Manager 的新 Web 服务的概述，请参阅[部署新 Web 服务](machine-learning-webservice-deploy-a-web-service.md)。
* 有关如何部署 Web 服务的演练，请参阅[部署 Azure 机器学习 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)。
* 有关如何创建和部署 Web 服务的完整演练，请参阅[演练步骤 1：创建机器学习工作区](machine-learning-walkthrough-1-create-ml-workspace.md)。
* 有关部署 Web 服务的特定示例，请参阅：

  * [演练步骤 5：部署 Azure 机器学习 Web 服务](machine-learning-walkthrough-5-publish-web-service.md)
  * [如何将 Web 服务部署到多个区域](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>使用 Web 服务资源提供程序 API (Azure Resource Manager API)
用于 Web 服务的 Azure 机器学习资源提供程序支持使用 REST API 调用来部署和管理 Web 服务。 有关其他详细信息，请参阅[机器学习 Web 服务 (REST)](/rest/api/machinelearning/index) 参考。

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>使用 PowerShell cmdlet
用于 Web 服务的 Azure 机器学习资源提供程序支持使用 PowerShell cmdlet 来部署和管理 Web 服务。

要使用 cmdlet，必须先使用 [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet 从 PowerShell 环境内登录到 Azure 帐户。 如果对调用基于资源管理器的 PowerShell 命令不熟悉，请参阅[将 Azure PowerShell 与 Azure Resource Manager 结合使用](../azure-resource-manager/powershell-azure-resource-manager.md#log-in-to-your-azure-account)。

若要导出预测实验，则使用[此代码示例](https://github.com/ritwik20/AzureML-WebServices)。 从代码创建 .exe 文件后，可以键入：

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

运行应用程序将创建 Web 服务 JSON 模板。 若要使用模板部署 Web 服务，必须添加以下信息：

* 存储帐户名和密钥

    可以从 [Azure 门户](https://portal.azure.com/)或 [Azure 经典门户](http://manage.windowsazure.com/)获取存储帐户名和密钥。
* 承诺计划 ID

    通过登录并单击计划名称，可以从 [Azure 机器学习 Web 服务](https://services.azureml.net)获取计划 ID。

在与 *MachineLearningWorkspace* 节点相同的级别上，将它们添加到 JSON 模板作为 *Properties* 节点的子节点。

下面是一个示例：

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

有关更多详细信息，请参阅以下文章和示例代码：

* MSDN 上的 [Azure 机器学习 Cmdlet](https://msdn.microsoft.com/library/azure/mt767952.aspx) 参考
* GitHub 上的示例[演练](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt)

## <a name="consume-the-web-services"></a>使用 Web 服务
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>从 Azure 机器学习 Web 服务 UI（测试）
可以从 Azure 机器学习 Web 服务门户测试 Web 服务。 这包括测试请求-响应服务 (RRS) 和批处理执行服务 (BES) 接口。

* [部署新 Web 服务](machine-learning-webservice-deploy-a-web-service.md)
* [部署 Azure 机器学习 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)
* [演练步骤 5：部署 Azure 机器学习 Web 服务](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>从 Excel
可以下载使用 Web 服务的 Excel 模板：

* [从 Excel 使用 Azure 机器学习 Web 服务](machine-learning-consuming-from-excel.md)
* [适用于 Azure 机器学习 Web 服务的 Excel 加载项](machine-learning-excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>从基于 REST 的客户端
Azure 机器学习 Web 服务是 RESTful API。 可以从各种平台使用这些 API，例如 .NET、Python、R、Java 等。[Microsoft Azure 机器学习 Web 服务门户](https://services.azureml.net)上的 Web 服务“使用”页提供示例代码，从而有助于开始操作。 有关详细信息，请参阅[如何使用 Azure 机器学习 Web 服务](machine-learning-consume-web-services.md)。

