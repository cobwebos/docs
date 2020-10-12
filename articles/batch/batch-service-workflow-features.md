---
title: Batch 服务工作流和资源
description: 从开发的角度了解 Batch 服务的功能及其高级工作流。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 76a0b140f3bea4b07a6de632abbcbc3fd26e582d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85965206"
---
# <a name="batch-service-workflow-and-resources"></a>Batch 服务工作流和资源

这篇 Azure Batch 服务核心组件的概述，将介绍 Batch 开发人员可用来构建大规模并发计算解决方案的高级工作流，以及使用的主要服务资源。

不管是在开发可发出直接 [REST API](/rest/api/batchservice/) 调用的分布式计算应用程序或服务，还是使用某个 [Batch SDK](batch-apis-tools.md#batch-service-apis)，都可以使用此处介绍的多种资源和功能。

> [!TIP]
> 有关 Batch 服务的更全面介绍，请参阅 [Basics of Azure Batch](batch-technical-overview.md)（Azure Batch 基础知识）。 另请参阅最新的 [Batch 服务更新](https://azure.microsoft.com/updates/?product=batch)。

## <a name="basic-workflow"></a>基本工作流

几乎所有使用 Batch 服务处理并行工作负荷的应用程序和服务都使用以下典型高级工作流：

1. 将要处理的**数据文件**上传到 [Azure 存储](../storage/index.yml)帐户。 Batch 包含访问 Azure Blob 存储的内置支持，在运行任务时，任务可以将这些文件下载到[计算节点](nodes-and-pools.md#nodes) 。
2. 上传任务所要运行的 **应用程序文件** 。 这些文件可能是二进制文件或脚本及其依赖项，并由作业中的任务执行。 任务可以从存储帐户下载这些文件，也可使用 Batch 的 [应用程序包](nodes-and-pools.md#application-packages) 功能来管理和部署应用程序。
3. 创建计算节点的 [池](nodes-and-pools.md#pools) 。 创建池时，可以指定池的计算节点数目、其大小和操作系统。 运行作业中的每个任务时，会将任务分配到池中的某个节点以执行。
4. 创建 [作业](jobs-and-tasks.md#jobs)。 作业管理任务的集合。 可将每个作业关联到要运行该作业的任务的特定池。
5. 将[任务](jobs-and-tasks.md#tasks)添加到作业。 每个任务将运行上传的应用程序或脚本，以处理它从存储帐户下载的数据文件。 当每个任务完成时，可将其输出上传到 Azure 存储。
6. 监视作业进度并从 Azure 存储检索任务输出。

> [!NOTE]
> 需要有[批处理帐户](accounts.md)才能使用批处理服务。 此外，大多数 Batch 解决方案都可以使用关联的 [Azure 存储](../storage/index.yml)帐户存储和检索文件。

## <a name="batch-service-resources"></a>Batch 服务资源

以下主题介绍可启用分布式计算方案的 Batch 资源。

- [Batch 帐户和存储帐户](accounts.md)
- [节点和池](nodes-and-pools.md)
- [作业和任务](jobs-and-tasks.md)
- [文件和目录](files-and-directories.md)

## <a name="next-steps"></a>后续步骤

- 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。
- 了解使用[批处理 .NET 客户端库](quick-run-dotnet.md)或 [Python](quick-run-python.md) 开发支持批处理的应用程序的基本概念。 这些快速入门介绍了使用 Batch 服务在多个计算节点上执行工作负荷的示例应用程序，并说明了如何使用 Azure 存储进行工作负荷文件暂存和检索。
- 下载并安装 [Batch Explorer](https://azure.github.io/BatchExplorer/)，供开发 Batch 解决方案时使用。 借助 Batch Explorer 来创建、调试和监视 Azure Batch 应用程序。
- 请参阅社区资源，包括 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch)、[Batch 社区存储库](https://github.com/Azure/Batch)和 [Azure Batch 论坛](/answers/topics/azure-batch.html)。
