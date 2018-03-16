---
title: "面向开发人员的 Azure Batch API 和工具 | Microsoft Docs"
description: "了解通过 Azure Batch 服务开发解决方案时可以使用的 API 和工具。"
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: get-started-article
ms.date: 02/26/2018
ms.author: danlep
ms.openlocfilehash: 0d3720d33de2237b0d8cce2ae56bcc7ccf6acc43
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="overview-of-batch-apis-and-tools"></a>批处理 API 和工具概述

使用 Azure Batch 处理并行工作负荷通常是使用[批处理 API](#batch-development-apis) 之一以编程方式实现的。 客户端应用程序或服务使用批处理 API 与批处理服务通信。 使用批处理 API 可以创建和管理计算节点池（不管是虚拟机还是云服务）。 然后即可计划作业和任务，使之在这些节点上运行。 

可以为组织高效处理大量工作负荷，或提供服务前端给客户，让他们可以在一个、数百个甚至数千个节点上，按需要或按计划运行作业和任务。 还可以在 [Azure 数据工厂](../data-factory/v1/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json)等工具管理的大型工作流中使用 Azure Batch。

> [!TIP]
> 若要深入了解 Batch API 所提供的功能，请参阅 [Batch feature overview for developers](batch-api-basics.md)（面向开发人员的 Batch 功能概述）。
> 
> 

## <a name="azure-accounts-for-batch-development"></a>用于批处理开发的 Azure 帐户
开发 Batch 解决方案时，请在 Microsoft Azure 中使用以下帐户。

* **Azure 帐户和订阅** - 如果还没有 Azure 订阅，可以激活 [Visual Studio 订户权益][msdn_benefits]或注册 [Azure 免费帐户][free_account]。 创建帐户时，系统为用户创建默认订阅。
* **批处理帐户** - Azure Batch 资源（包括池、计算节点、作业和任务）与 Azure Batch 帐户相关联。 当应用程序针对 Batch 服务提出请求时，会使用 Azure Batch 帐户名称、帐户的 URL 以及访问密钥或 Azure Active Directory 令牌对请求进行身份验证。 可以通过 Azure 门户或编程方式[创建 Batch 帐户](batch-account-create-portal.md)。
* **存储帐户** - 批处理提供的内置支持允许处理 [Azure 存储][azure_storage]中的文件。 几乎每个批处理方案都使用 Azure Blob 存储来暂存任务所运行的程序及其处理的数据，以及存储任务生成的输出数据。 若要创建存储帐户，请参阅 [关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。

## <a name="batch-service-apis"></a>批处理服务 API

应用程序和服务可以发出直接 REST API 调用或使用一个或多个下述客户端库，以便运行和管理 Azure Batch 工作负荷。

| API | API 参考 | 下载 | 教程 | 代码示例 | 更多信息 |
| --- | --- | --- | --- | --- | --- |
| **批处理 REST** |[docs.microsoft.com][batch_rest] |不适用 |- |- | [支持的版本](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[教程](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [发行说明](http://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[教程](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [自述文件](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **批处理 Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[教程](batch-nodejs-get-started.md) |- | [自述文件](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **批处理 Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[自述文件][api_sample_java] | [自述文件](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>批处理管理 API

适用于 Batch 的 Azure 资源管理器 API 允许通过编程方式访问批处理帐户。 可以使用这些 API 通过 Microsoft.Batch 提供程序以编程方式管理 Batch 帐户、配额、应用程序包和其他资源。  

| API | API 参考 | 下载 | 教程 | 代码示例 |
| --- | --- | --- | --- | --- |
| **批次管理 REST** |[docs.microsoft.com][api_rest_mgmt] |不适用 |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [教程](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **批次管理 Python** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **批次管理 Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **批次管理 Java** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>批处理命令行工具

这些命令行工具提供的功能与批处理服务和批处理管理 API 相同： 

* [批处理 PowerShell cmdlet][batch_ps]：[Azure PowerShell](/powershell/azure/overview) 模块中的 Azure Batch cmdlet 可让用户使用 PowerShell 管理批处理资源。
* [Azure CLI 2.0](/cli/azure)：Azure 命令行界面 (Azure CLI) 是一个跨平台工具集，提供用来与许多 Azure 服务（包括 Batch 服务和 Batch 管理服务）交互的 shell 命令。 请参阅[使用 Azure CLI 管理批处理资源](batch-cli-get-started.md)，详细了解如何将 Azure CLI 与批处理配合使用。

## <a name="other-tools-for-application-development"></a>适合应用程序开发的其他工具

下面是一些其他的工具，这些工具可能适合生成和调试批处理应用程序和服务：

* [Azure 门户][portal]：可以在 Azure 门户中创建、监视和删除 Batch 池、作业和任务。 用户运行作业时，可以查看这些资源和其他资源的状态信息，甚至从池中的计算节点下载文件。 例如，在进行故障排除时下载失败任务的 `stderr.txt`。 用户还可以下载可用于登录到计算节点的远程桌面 (RDP) 文件。
* [Azure BatchLabs][batch_labs]：BatchLabs 是一个功能丰富的免费独立客户端工具，可帮助创建、调试和监视 Azure Batch 应用程序。 下载适用于 Mac、Linux 或 Windows 的[安装包](https://azure.github.io/BatchLabs/)。
* [Microsoft Azure 存储资源管理器][storage_explorer]：严格地说，虽然存储资源管理器不算是 Azure Batch 工具，但却是开发和调试批处理解决方案时的另一个很有用的工具。

## <a name="additional-resources"></a>其他资源

- 若要了解批处理应用程序中的日志记录事件，请参阅[记录事件以用来对批处理解决方案进行诊断评估和监视](batch-diagnostics.md)。 如需参考批处理服务引发的事件，请参阅[批处理分析](batch-analytics.md)。
- 若要了解计算节点的环境变量，请参阅 [Azure Batch 计算节点环境变量](batch-compute-node-environment-variables.md)。

## <a name="next-steps"></a>后续步骤

* 对于准备使用 Batch 的任何人，有必要阅读 [面向开发人员的 Batch 功能概述](batch-api-basics.md)了解基本信息。 本文中包含有关 Batch 服务资源（如池、节点、作业和任务）以及生成 Batch 应用程序时可以使用的许多 API 功能的更多详细信息。
* [Get started with the Azure Batch library for .NET](tutorial-parallel-dotnet.md) （适用于 .NET 的 Azure Batch 库入门），了解如何使用 C# 和 Batch .NET 库在常见的 Batch 工作流中执行简单的工作负荷。 也提供 [Python 版](tutorial-parallel-python.md)和 [Node.js 教程](batch-nodejs-get-started.md)。
* 下载 [GitHub 上的代码示例][github_samples]，了解如何通过综合使用 C# 和 Python 与批处理来计划和处理示例工作负荷。


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch/clientlibrary:
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-mgmt-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/azurerm.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchLabs/
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com
