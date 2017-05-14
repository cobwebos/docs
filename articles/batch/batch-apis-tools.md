---
title: "使用 Azure 批处理 API 和工具开发大型并行处理解决方案 | Microsoft Docs"
description: "了解通过 Azure 批处理服务开发解决方案时可以使用的 API 和工具。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 1c0f8f3fede88b6e0bace35372a2d54bb53e5182
ms.contentlocale: zh-cn
ms.lasthandoff: 05/04/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>批处理 API 和工具概述

使用 Azure 批处理处理并行工作负荷通常是使用[批处理 API](#batch-development-apis) 之一以编程方式实现的。 客户端应用程序或服务使用批处理 API 与批处理服务通信。 使用批处理 API 可以创建和管理计算节点池（不管是虚拟机还是云服务）。 然后即可计划作业和任务，使之在这些节点上运行。 

可以为组织高效处理大量工作负荷，或提供服务前端给客户，让他们可以在一个、数百个甚至数千个节点上，按需要或按计划运行作业和任务。 还可以在 [Azure 数据工厂](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json)等工具管理的大型工作流中使用 Azure 批处理。

> [!TIP]
> 若要深入了解 Batch API 所提供的功能，请参阅 [Batch feature overview for developers](batch-api-basics.md)（面向开发人员的 Batch 功能概述）。
> 
> 

## <a name="azure-accounts-for-batch-development"></a>用于批处理开发的 Azure 帐户
开发 Batch 解决方案时，你将在 Microsoft Azure 中使用以下帐户。

* **Azure 帐户和订阅** - 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益][msdn_benefits]或注册 [Azure 免费帐户][free_account]。 创建帐户时，系统为用户创建默认订阅。
* **批处理帐户** - Azure 批处理资源（包括池、计算节点、作业和任务）与 Azure 批处理帐户相关联。 当应用程序针对批处理服务提出请求时，会使用 Azure 批处理帐户名称、帐户的 URL 以及访问密钥对请求进行身份验证。 可以在 Azure 门户中 [创建 Batch 帐户](batch-account-create-portal.md) 。
* **存储帐户** - 批处理提供的内置支持允许处理 [Azure 存储][azure_storage]中的文件。 几乎每个批处理方案都使用 Azure Blob 存储来暂存任务所运行的程序及其处理的数据，以及存储任务生成的输出数据。 若要创建存储帐户，请参阅 [关于 Azure 存储帐户](../storage/storage-create-storage-account.md)。

## <a name="batch-service-apis"></a>批处理服务 API

应用程序和服务可以发出直接 REST API 调用或使用一个或多个下述客户端库，以便运行和管理 Azure 批处理工作负荷。

| API | API 参考 | 下载 | 教程 | 代码示例 | 更多信息 |
| --- | --- | --- | --- | --- | --- |
| **批处理 REST** |[MSDN][batch_rest] |不适用 |- |- | [支持的版本](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[教程](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [发行说明](https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/Client/changelog.md) |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[教程](batch-python-tutorial.md)|[GitHub][api_sample_python] | [自述文件](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **批处理 Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [自述文件](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **批处理 Java** |[github.io][api_java] |[Maven][api_java_jar] |- |[自述文件][api_sample_java] | [自述文件](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>批处理管理 API

适用于批处理的 Azure Resource Manager API 允许通过编程方式访问批处理帐户。 可以使用这些 API 通过编程方式管理批处理帐户、配额和应用程序包。  

| API | API 参考 | 下载 | 教程 | 代码示例 |
| --- | --- | --- | --- | --- |
| **批处理 Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |不适用 |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **批处理 Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [教程](batch-management-dotnet.md) |[GitHub][api_sample_net] |

## <a name="batch-command-line-tools"></a>批处理命令行工具

这些命令行工具提供的功能与批处理服务和批处理管理 API 相同： 

* [批处理 PowerShell cmdlet][batch_ps]：[Azure PowerShell](/powershell/azure/overview) 模块中的 Azure 批处理 cmdlet 可让用户使用 PowerShell 管理批处理资源。
* [Azure CLI](/cli/azure/overview)：Azure 命令行界面 (Azure CLI) 是一个跨平台工具集，提供用来与许多 Azure 服务（包括批处理服务和批处理管理服务）交互的 shell 命令。 请参阅[使用 Azure CLI 管理批处理资源](batch-cli-get-started.md)，详细了解如何将 Azure CLI 与批处理配合使用。

## <a name="other-tools-for-application-development"></a>适合应用程序开发的其他工具

下面是一些其他的工具，这些工具可能适合生成和调试批处理应用程序和服务：

* [Azure 门户][portal]：可以在 Azure 门户的批处理边栏选项卡中创建、监视和删除批处理池、作业和任务。 用户运行作业时，可以查看这些资源和其他资源的状态信息，甚至从池中的计算节点下载文件。 例如，在进行故障排除时下载失败任务的 `stderr.txt`。 用户还可以下载可用于登录到计算节点的远程桌面 (RDP) 文件。
* [Azure 批处理资源管理器][batch_explorer]：批处理资源管理器提供与 Azure 门户类似的批处理资源管理功能，但位于独立的 Windows Presentation Foundation (WPF) 客户端应用程序中。 [GitHub][github_samples] 上提供的一个批处理 .NET 示例应用程序，用户可以使用 Visual Studio 2015 或更高版本生成它，然后在开发和调试批处理解决方案时，使用它浏览和管理批处理帐户中的资源。 可以查看作业、池和任务详细信息，从计算节点下载文件，以及使用可通过 Batch Explorer 下载的远程桌面 (RDP) 文件以远程方式连接到节点。
* [Microsoft Azure 存储资源管理器][storage_explorer]：严格地说，虽然存储资源管理器不算是 Azure 批处理工具，但却是开发和调试批处理解决方案时的另一个很有用的工具。

## <a name="additional-resources"></a>其他资源

- 若要了解批处理应用程序中的日志记录事件，请参阅[记录事件以用来对批处理解决方案进行诊断评估和监视](batch-diagnostics.md)。 如需参考批处理服务引发的事件，请参阅[批处理分析](batch-analytics.md)。
- 若要了解计算节点的环境变量，请参阅 [Azure 批处理计算节点环境变量](batch-compute-node-environment-variables.md)。

## <a name="next-steps"></a>后续步骤

* 对于准备使用 Batch 的任何人，有必要阅读 [面向开发人员的 Batch 功能概述](batch-api-basics.md)了解基本信息。 本文中包含有关 Batch 服务资源（如池、节点、作业和任务）以及生成你的 Batch 应用程序时可以使用的许多 API 功能的更多详细信息。
* [Get started with the Azure Batch library for .NET](batch-dotnet-get-started.md) （适用于 .NET 的 Azure Batch 库入门），了解如何使用 C# 和 Batch .NET 库在常见的 Batch 工作流中执行简单的工作负荷。 若要了解如何使用 Batch 服务，应先学习此文。 此外还提供了该教程的 [Python 版本](batch-python-tutorial.md) 。
* 下载 [GitHub 上的代码示例][github_samples]，了解如何通过综合使用 C# 和 Python 与批处理来计划和处理示例工作负荷。
* 查看[批处理学习路径][learning_path]，了解学习使用批处理时可用的资源。


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

