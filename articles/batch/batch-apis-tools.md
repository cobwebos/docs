---
title: 面向开发人员的 API 和工具
description: 了解通过 Azure Batch 服务开发解决方案时可以使用的 API 和工具。
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: 502eb08631223215933b75dca882c12c02d17bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146396"
---
# <a name="overview-of-batch-apis-and-tools"></a>批处理 API 和工具概述

使用 Azure Batch 处理并行工作负荷通常是使用批处理 API 之一以编程方式实现的。 客户端应用程序或服务可使用 Batch API 与 Batch 服务通信。 Batch API 允许用户创建和管理计算节点（虚拟机或云服务）池。 随后可将作业和任务计划为在这些节点上运行。

可以为组织高效处理大量工作负荷，或提供服务前端给客户，让他们可以在一个、数百个甚至数千个节点上，按需要或按计划运行作业和任务。 还可以在 [Azure 数据工厂](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json)等工具管理的大型工作流中使用 Azure Batch 。

> [!TIP]
> 若要详细了解 Azure Batch 中所用的功能和工作流，请参阅 [Batch 服务工作流和资源](batch-service-workflow-features.md)。

## <a name="azure-accounts-for-batch-development"></a>用于批处理开发的 Azure 帐户

开发 Batch 解决方案时，请在 Azure 订阅中使用以下帐户：

- **Batch 帐户** - Azure Batch 资源（包括池、计算节点、作业和任务）与 Azure [Batch 帐户](accounts.md)相关联。 当应用程序针对 Batch 服务提出请求时，会使用 Azure Batch 帐户名称、帐户的 URL 以及访问密钥或 Azure Active Directory 令牌对请求进行身份验证。 可以通过 Azure 门户或编程方式[创建 Batch 帐户](batch-account-create-portal.md)。
- **存储帐户** - Batch 提供的内置支持允许处理 [Azure 存储](../storage/index.yml)中的文件。 几乎每个 Batch 方案都使用 Azure Blob 存储暂存任务所运行的程序及其处理的数据，以及存储任务生成的输出数据。 每个 Batch 帐户通常与相应的存储帐户关联。

## <a name="service-level-and-management-level-apis"></a>服务级别和管理级 API

Azure Batch 具有两组 API，一个用于服务级别，另一个用于管理级别。 命名通常相似，但它们返回不同的结果。

活动日志中只跟踪来自管理 API 的操作。 服务级别 API 会绕过 Azure 资源管理层 (management.azure.com)，并且不会进行记录。

例如，[用于删除池的 Batch 服务 API](/rest/api/batchservice/pool/delete) 直接以批处理帐户为目标：`DELETE {batchUrl}/pools/{poolId}`

而[用于删除池的 Batch 管理 API](/rest/api/batchmanagement/pool/delete) 的目标是 management.azure.com 层：`DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>批处理服务 API

应用程序和服务可发出直接的 REST API 调用，或使用以下一个或多个客户端库，运行和管理 Azure Batch 工作负荷。

| API | API 参考 | 下载 | 教程 | 代码示例 | 更多信息 |
| --- | --- | --- | --- | --- | --- |
| **批处理 REST** |[Azure REST API - Docs](/rest/api/batchservice/) |空值 |- |- | [支持的版本](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[用于 .NET 的 Azure SDK - Docs](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[教程](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [发行说明](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[用于 Python 的 Azure SDK - Docs](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[教程](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [自述文件](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **批处理 Node.js** |[用于 JavaScript 的 Azure SDK - Docs](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[教程](batch-nodejs-get-started.md) |- | [自述文件](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **批处理 Java** |[用于 Java 的 Azure SDK - Docs](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [自述文件](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>批处理管理 API

通过用于 Batch 的 Azure Resource Manager API，可以编程方式访问批处理帐户。 可以使用这些 API 通过 Microsoft.Batch 提供程序以编程方式管理 Batch 帐户、配额、应用程序包和其他资源。  

| API | API 参考 | 下载 | 教程 | 代码示例 |
| --- | --- | --- | --- | --- |
| **批次管理 REST** |[Azure REST API - Docs](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[用于 .NET 的 Azure SDK - Docs](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [教程](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **批次管理 Python** |[用于 Python 的 Azure SDK - Docs](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **批次管理 Node.js** |[用于 JavaScript 的 Azure SDK - Docs](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **批次管理 Java** |[用于 Java 的 Azure SDK - Docs](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>批处理命令行工具

这些命令行工具提供的功能与批处理服务和批处理管理 API 相同： 

- [Batch PowerShell cmdlet](/powershell/module/az.batch/)：[Azure PowerShell](/powershell/azure/) 模块中的 Azure Batch cmdlet 可让用户使用 PowerShell 管理 Batch 资源。
- [Azure CLI](/cli/azure)：Azure CLI 是一个跨平台工具集，提供用来与许多 Azure 服务（包括 Batch 服务和 Batch 管理服务）交互的 shell 命令。 请参阅[使用 Azure CLI 管理批处理资源](batch-cli-get-started.md)，详细了解如何将 Azure CLI 与批处理配合使用。

## <a name="other-tools-for-application-development"></a>适合应用程序开发的其他工具

这些附加工具可能有助于生成和调试 Batch 应用程序和服务。

- [Azure 门户](https://portal.azure.com/)：可以在 Azure 门户中创建、监视和删除 Batch 池、作业和任务。 用户运行作业时，可以查看这些资源和其他资源的状态信息，甚至可以从池中的计算节点下载文件。 例如，在进行故障排除时下载失败任务的 `stderr.txt`。 用户还可以下载可用于登录到计算节点的远程桌面 (RDP) 文件。
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/)：Batch Explorer（以前称为 BatchLabs）是一个功能丰富的免费独立客户端工具，可帮助创建、调试和监视 Azure Batch 应用程序。 下载适用于 Mac、Linux 或 Windows 的[安装包](https://azure.github.io/BatchExplorer/)。
- [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard)：Batch Shipyard 是一个帮助在 Azure Batch 上预配、执行和监视基于容器的批处理和 HPC 工作负荷的工具。
- [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)：严格地说，虽然存储资源管理器不算是 Azure Batch 工具，但却是开发和调试 Batch 解决方案时的另一个很有用的工具。

## <a name="additional-resources"></a>其他资源

- 若要了解如何从 Batch 应用程序记录事件，请参阅[用于诊断评估和监视的 Batch 指标、警报和日志](batch-diagnostics.md)。
- 如需参考 Batch 服务引发的事件的信息，请参阅 [Batch 分析](batch-analytics.md)。
- 若要了解计算节点的环境变量，请参阅 [Azure Batch 运行时环境变量](batch-compute-node-environment-variables.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)，例如池、节点、作业和任务。
- [Get started with the Azure Batch library for .NET](tutorial-parallel-dotnet.md) （适用于 .NET 的 Azure Batch 库入门），了解如何使用 C# 和 Batch .NET 库在常见的 Batch 工作流中执行简单的工作负荷。 也提供 [Python 版](tutorial-parallel-python.md)和 [Node.js 教程](batch-nodejs-get-started.md)。
- 下载 [GitHub 上的代码示例](https://github.com/Azure-Samples/azure-batch-samples)，了解 C# 和 Python 如何与 Batch 交互以计划和处理示例工作负荷。
