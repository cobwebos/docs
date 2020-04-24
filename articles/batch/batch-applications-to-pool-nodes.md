---
title: 将应用程序和数据复制到池节点
description: 了解如何将应用程序和数据复制到池节点。
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 700e9b80f8420266c0300b47bdd30bc271f8421c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115578"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>将应用程序和数据复制到池节点

Azure Batch 支持多种方法将数据和应用程序获取到计算节点，以便任务可以使用数据和应用程序。 数据和应用程序可能需要运行整个作业，因此需要在每个节点上安装。 某些情况下可能只需要特定任务，或者需要为作业安装，但不需要在每个节点上安装。 批处理具有适用于每种方案的工具。

- **池启动任务资源文件**：需要在池中的每个节点上安装的应用程序或数据。 将此方法与应用程序包或启动任务的资源文件集合一起使用，以便执行安装命令。  

示例： 
- 使用 "启动任务" 命令行移动或安装应用程序

- 指定 Azure 存储帐户中的特定文件或容器的列表。 有关详细信息，请参阅[add # resourcefile IN REST 文档](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- 池上运行的每个作业都将运行 MyApplication，必须首先安装 MyApplication。 如果使用此机制，则需要将启动任务的 "**等待成功**" 属性设置为 " **true**"。 有关详细信息，请参阅[add # starttask IN REST 文档](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)。

- 池上的**应用程序包引用**：对于需要在池中的每个节点上安装的应用程序或数据。 没有与应用程序包相关联的安装命令，但你可以使用启动任务来运行任何安装命令。 如果你的应用程序不需要安装或包含大量文件，则可以使用此方法。 应用程序包非常适合于大量文件，因为它们会将大量文件引用组合到小型有效负载中。 如果尝试将超过100个单独的资源文件包含到一个任务中，则批处理服务可能会出现单个任务的内部系统限制。 此外，如果你有严格的版本控制要求，并且你可能有许多不同版本的相同应用程序，并且需要在它们之间进行选择，请使用应用程序包。 有关详细信息，请参阅[将应用程序部署到带有批处理应用程序包的计算节点](https://docs.microsoft.com/azure/batch/batch-application-packages)。

- **作业准备任务资源文件**：对于必须为要运行的作业安装的应用程序或数据，但不需要将其安装在整个池中。 例如：如果你的池有许多不同类型的作业，并且只有一种作业类型需要 MyApplication 才能运行，则有必要将安装步骤放入作业准备任务。 有关作业准备任务的详细信息，请参阅[在 Batch 计算节点上运行作业准备和作业释放任务](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/)。

- **任务资源文件**：适用于应用程序或数据仅适用于单个任务的情况。 例如：有五个任务，每个任务处理不同的文件，然后将输出写入 blob 存储。  在这种情况下，应在**任务资源文件**集合上指定输入文件，因为每个任务都有其自己的输入文件。

## <a name="determine-the-scope-required-of-a-file"></a>确定文件所需的范围

需要确定文件的作用域-是池、作业或任务所需的文件。 作用域为池的文件应使用池应用程序包或启动任务。 作用域为作业的文件应使用作业准备任务。 在池或作业级别范围很好的文件示例就是应用程序。 作用域到任务的文件应使用任务资源文件。

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>将数据获取到 Batch 计算节点的其他方法

还有其他方法可将数据导入未正式集成到批处理 REST API 中的 Batch 计算节点。 由于你可以控制 Azure Batch 节点，并且可以运行自定义可执行文件，因此，只要 Batch 节点连接到目标，并且你具有该源的凭据到 Azure Batch 节点，就可以从任意数量的自定义源中拉取数据。 一些常见示例包括：

- 从 SQL 下载数据
- 从其他 web 服务/自定义位置下载数据
- 映射网络共享

### <a name="azure-storage"></a>Azure 存储

Blob 存储具有下载可伸缩性目标。 Azure 存储文件共享的可伸缩性目标与单个 blob 相同。 大小会影响所需的节点和池的数量。

