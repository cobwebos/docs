---
title: 部署和使用
titleSuffix: ML Studio (classic) - Azure
description: You can use Azure Machine Learning Studio (classic) to deploy machine learning workflows and models as web services. 然后，可以使用这些 Web 服务，通过 Internet 从应用程序调用机器学习模型，从而实时或者在批处理模式下进行预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 1c97fd4f4d5646b6654f5261abd99372c521c389
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228240"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (classic) Web Services: Deployment and consumption

You can use Azure Machine Learning Studio (classic) to deploy machine learning workflows and models as web services. 然后，可以使用这些 Web 服务，通过 Internet 从应用程序调用机器学习模型，从而实时或者在批处理模式下进行预测。 由于 Web 服务是 RESTful，可以从各种编程语言和平台（如 .NET 和 Java）以及应用程序（如 Excel）调用它们。

接下来的部分提供指向演练、代码和文档的链接，可帮助你开始操作。

## <a name="deploy-a-web-service"></a>部署一个 Web 服务

### <a name="with-azure-machine-learning-studio-classic"></a>With Azure Machine Learning Studio (classic)

The Studio (classic) portal and the Microsoft Azure Machine Learning Web Services portal help you deploy and manage a web service without writing code.

以下链接提供有关如何部署新 Web 服务的常规信息：

* 有关如何部署基于 Azure 资源管理器的新 Web 服务的概述，请参阅[部署新 Web 服务](deploy-a-machine-learning-web-service.md)。
* 有关如何部署 Web 服务的演练，请参阅[部署 Azure 机器学习 Web 服务](deploy-a-machine-learning-web-service.md)。
* For a full walkthrough about how to create and deploy a web service, start with [Tutorial 1: Predict credit risk](tutorial-part1-credit-risk.md).
* 有关部署 Web 服务的特定示例，请参阅：

  * [Tutorial 3: Deploy credit risk model](tutorial-part3-credit-risk-deploy.md)
  * [如何将 Web 服务部署到多个区域](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>使用 Web 服务资源提供程序 API (Azure 资源管理器 API)

The classic version of Azure Machine Learning Studio resource provider for web services enables deployment and management of web services by using REST API calls. 有关详细信息，请参阅[机器学习 Web 服务 (REST)](/rest/api/machinelearning/index) 参考。

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>使用 PowerShell cmdlet

The classic version of Azure Machine Learning Studio resource provider for web services enables deployment and management of web services by using PowerShell cmdlets.

To use the cmdlets, you must first sign in to your Azure account from within the PowerShell environment by using the [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet. 如果对调用基于资源管理器的 PowerShell 命令不熟悉，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](../../azure-resource-manager/manage-resources-powershell.md)。

若要导出预测实验，则使用[此代码示例](https://github.com/ritwik20/AzureML-WebServices)。 从代码创建 .exe 文件后，可以键入：

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

运行应用程序将创建 Web 服务 JSON 模板。 若要使用模板部署 Web 服务，必须添加以下信息：

* 存储帐户名和密钥

    可以从 [Azure 门户](https://portal.azure.com/)获取存储帐户名和密钥。
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

* [Azure Machine Learning Studio (classic) Cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) reference on MSDN
* GitHub 上的示例[演练](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt)

## <a name="consume-the-web-services"></a>使用 Web 服务

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>从 Azure 机器学习 Web 服务 UI（测试）

可以从 Azure 机器学习 Web 服务门户测试 Web 服务。 这包括测试请求-响应服务 (RRS) 和批处理执行服务 (BES) 接口。

* [部署新 Web 服务](deploy-a-machine-learning-web-service.md)
* [部署 Azure 机器学习 Web 服务](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: Deploy credit risk model](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>从 Excel

可以下载使用 Web 服务的 Excel 模板：

* [从 Excel 使用 Azure 机器学习 Web 服务](consuming-from-excel.md)
* [适用于 Azure 机器学习 Web 服务的 Excel 加载项](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>从基于 REST 的客户端

Azure 机器学习 Web 服务是 RESTful API。 You can consume these APIs from various platforms, such as .NET, Python, R, Java, etc. The **Consume** page for your web service on the [Microsoft Azure Machine Learning Web Services portal](https://services.azureml.net) has sample code that can help you get started. 有关详细信息，请参阅[如何使用 Azure 机器学习 Web 服务](consume-web-services.md)。
