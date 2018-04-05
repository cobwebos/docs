---
title: 将已完成作业和任务的结果或日志保存到数据存储 - Azure 批处理 | Microsoft Docs
description: 了解用于保存批处理任务和作业的输出数据的不同选项。 可以将数据保存到 Azure 存储，或保存到其他数据存储。
services: batch
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb8b1ca3514e27221e95cb2def823c8f89d151e5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="persist-job-and-task-output"></a>持久性作业和任务输出

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

任务输出的一些常见示例包括：

- 任务处理输入数据时创建的文件。
- 与任务执行关联的日志文件。 

本文介绍了保存任务输出的多个选项，以及各选项最适用的应用场景。   

## <a name="about-the-batch-file-conventions-standard"></a>关于批处理文件约定标准

批处理定义了用于为 Azure 存储中的任务输出文件命名的一组可选的约定。 [批处理文件约定标准](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)介绍了这些约定。 文件约定标准根据作业和任务的名称确定 Azure 存储中用于给定输出文件的目标容器和 blob 路径的名称。

是否要使用文件约定标准为输出数据文件命名由你决定。 也可以根据自己的需要为目标容器和 blob 命名。 如果使用文件约定标准为输出文件命名，则输出文件可在 [Azure 门户][portal]中进行查看。

文件约定标准有几种不同的使用方法：

- 如果使用批处理服务 API 保存输出文件，则可以选择按照文件约定标准为目标容器和 blob 命名。 通过批处理服务 API，无需修改任务应用程序即可保存客户端代码的输出文件。
- 如果使用 .NET 进行开发，则可以使用[适用于 .NET 的 Azure 批处理文件约定库][nuget_package]。 使用此库的优势是其支持根据其 ID 或用途查询输出文件。 通过内置查询功能可以轻松访问客户端应用程序或其他任务的输出文件。 但是，必须将任务应用程序修改为调用文件约定库。 有关详细信息，请参阅[适用于 .NET 的文件约定库](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx)参考。
- 如果使用除 .NET 之外的语言进行开发，则可以在应用程序中实现文件约定标准。

## <a name="design-considerations-for-persisting-output"></a>保存输出的设计注意事项 

设计批处理解决方案时，请考虑以下几个与作业和任务输出相关的因素。

* **计算节点生存期**：计算节点通常是瞬态的，尤其是在启用了自动缩放的池中。 在某个节点上运行的任务的输出仅在该节点存在时才可用，并且仅在你为任务设置的文件保留期内才可用。 如果任务完成后可能需要任务生成的输出，则任务必须将其输出文件上传到持久存储（例如 Azure 存储）。

* **输出存储**：建议将 Azure 存储用作任务输出的数据存储，但可使用任意持久存储。 将任务输出写入 Azure 存储的功能已集成到批处理服务 API。 如果使用其他形式的持久存储，需要自行写入用于保存任务输出的应用程序逻辑。   

* **输出检索**：可以直接从池中的计算节点检索任务输出；如果已保存任务输出，则可以从 Azure 存储或其他数据存储检索任务输出。 若要直接从计算节点检索任务输出，需要获取文件名及其在节点上的输出位置。 如果将任务输出保存到 Azure 存储，则需要文件在 Azure 存储中的完整路径才能使用 Azure 存储 SDK 下载输出文件。

* **查看输出**：导航到 Azure 门户中的某个 Batch 任务并选择“节点上的文件”时，将看到与该任务关联的所有文件，而不仅仅是想要查看的输出文件。 同样，计算节点上的文件仅在该节点存在时才可用，并且仅在为任务设置的文件保留时间范围内才可用。 若要查看已保存到 Azure 存储的任务输出，可使用 Azure 门户或 Azure 存储客户端应用程序（例如 [Azure 存储资源管理器][storage_explorer]）。 若要使用门户或其他工具查看 Azure 存储中的输出数据，则必须知道文件的位置并直接导航到该文件。

## <a name="options-for-persisting-output"></a>保存输出的选项

根据应用场景，可以使用几种不同的方法保存任务输出：

- 使用批处理服务 API。  
- 使用适用于 .NET 的批处理文件约定库。  
- 在应用程序中实现批处理文件约定标准。
- 实现自定义文件移动解决方案。

以下各节详细介绍了各个方法。

### <a name="use-the-batch-service-api"></a>使用批处理服务 API

在 2017 年 5 月 1 日的版本中，批处理服务添加了在[向作业添加任务](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job)或在[向作业添加任务集合](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job)时指定任务数据在 Azure 存储中的输出文件的支持。

批处理服务 API 支持将使用虚拟机配置创建的池中的任务数据保存到 Azure 存储帐户。 借助批处理服务 API，无需修改任务运行的应用程序即可保存任务数据。 或者，可以选择遵守[批处理文件约定标准](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)，为保存到 Azure 存储的文件命名。 

在以下情况下使用批处理服务 API 保存任务输出：

- 想要保存使用虚拟机配置创建的池中的批处理任务和作业管理器任务的数据。
- 想要将数据保存到具有任意名称的 Azure 存储容器。
- 想要将数据保存到按照[批处理文件约定标准](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)命名的 Azure 存储容器。 

> [!NOTE]
> 批处理服务 API 不支持保存使用云服务配置创建的池中运行的任务数据。 有关保存运行云服务配置的池的任务输出的信息，请参阅[使用适用于 .NET 的批处理文件约定库将作业和任务数据保存到 Azure 存储](batch-task-output-file-conventions.md)
> 
> 

有关使用批处理服务 API 保存任务输出的详细信息，请参阅[使用批处理服务 API 将任务数据保存到 Azure 存储](batch-task-output-files.md)。 另请参阅 GitHub 上的 [PersistOutputs][github_persistoutputs] 示例项目，该示例演示了如何使用适用于 .NET 的批处理客户端库将任务输出保存到持久存储。

### <a name="use-the-batch-file-conventions-library-for-net"></a>使用适用于 .NET 的批处理文件约定库

使用 C# 和 .NET 生成批处理解决方案的开发人员可以使用[适用于 .NET 的文件约定库][nuget_package]，按照[批处理文件约定标准](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)将任务数据保存到 Azure 存储帐户。 文件约定库以众所周知的方法处理将输出文件移动到 Azure 存储并为目标容器和 blob 命名的过程。

文件约定库支持根据 ID 或用途查询输出文件，方便轻松进行查找，而无需完整的文件 URI。 

在以下情况下使用适用于 .NET 的批处理文件约定库保存任务输出：

- 在任务仍在运行时，想要将数据流式传输到 Azure 存储。
- 希望保存使用云服务配置或虚拟机配置创建的池中的数据。
- 作业中的客户端应用程序或其他任务需要根据 ID 或用途定位并下载任务输出文件。 
- 想要执行检查点或初始结果的早期上传。
- 想要在 Azure 门户中查看任务输出。

有关使用适用于 .NET 的文件约定库保存任务输出的详细信息，请参阅[使用适用于 .NET 的批处理文件约定库将作业和任务数据保存到 Azure 存储](batch-task-output-file-conventions.md)。 另请参阅 GitHub 上的 [PersistOutputs][github_persistoutputs] 示例项目，该示例演示了如何使用适用于 .NET 的文件约定库将任务输出保存到持久存储。

GitHub 上的 [PersistOutputs][github_persistoutputs] 示例项目演示了如何使用适用于 .NET 的批处理客户端库将任务输出保存到持久存储。

### <a name="implement-the-batch-file-conventions-standard"></a>实现批处理文件约定标准

如果使用的是除 .NET 之外的语言，则可以在自己的应用程序中实现[批处理文件约定标准](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)。 

在需要经认证的命名方案或想要在 Azure 门户中查看任务输出时，可能需要自行实现文件约定命名标准。

### <a name="implement-a-custom-file-movement-solution"></a>实现自定义文件移动解决方案

也可实现自己的完整文件移动解决方案。 在以下情况下使用此方法：

- 想要将任务数据保存到除 Azure 存储之外的数据存储。 若要将文件上传到 Azure SQL 或 Azure Data Lake 等数据存储，可以创建自定义脚本或可执行文件来上传到该位置。 然后，在运行主要的可执行文件后，可在命令行上调用它。 例如，在 Windows 节点上，可以调用以下两个命令：`doMyWork.exe && uploadMyFilesToSql.exe`
- 想要执行检查点或初始结果的早期上传。
- 想要维持对错误处理的具体控制。 例如，在想要使用任务相关性操作根据特定任务退出代码执行某些上传操作时，可能需要实现自己的解决方案。 有关任务相关性操作的详细信息，请参阅[创建任务相关性以运行依赖于其他任务的任务](batch-task-dependencies.md)。 

## <a name="next-steps"></a>后续步骤

- 在[使用批处理服务 API 将任务数据保存到 Azure 存储](batch-task-output-files.md)中了解如何使用批处理服务 API 中的新功能保存任务数据。
- 在[使用适用于 .NET 的批处理文件约定库将作业和任务数据保存到 Azure 存储](batch-task-output-file-conventions.md)中了解如何使用适用于 .NET 的批处理文件约定库。
- 请参阅 GitHub 上的 [PersistOutputs][github_persistoutputs] 示例项目，该示例演示了如何使用适用于 .NET 的批处理客户端库和适用于 .NET 的文件约定库将任务输出保存到持久存储。

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
