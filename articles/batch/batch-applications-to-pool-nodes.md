---
title: 将应用程序和数据复制到池节点
description: 了解如何将应用程序和数据复制到池节点。
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385579"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>将应用程序和数据复制到池节点

Azure Batch 支持将数据和应用程序获取到计算节点的多种方法，以便数据和应用程序可供任务使用。 运行整个作业可能需要数据和应用程序，因此需要在每个节点上安装。 有些可能仅针对特定任务是必需的，或者需要为作业安装，但不需要在每个节点上。 Batch 具有针对每种方案的工具。

- **池启动任务资源文件**：对于需要在池中的每个节点上安装的应用程序或数据。 将此方法与应用程序包或启动任务的资源文件集合一起使用，以便执行安装命令。  

示例： 
- 使用启动任务命令行移动或安装应用程序

- 在 Azure 存储帐户中指定特定文件或容器的列表。 有关详细信息，请参阅[在 REST 文档中添加资源文件](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- 在池上运行的每个作业都运行 MyApplication.exe，必须首先安装 MyApplication.msi。 如果使用此机制，则需要将开始任务的**等待成功**属性设置为**true**。 有关详细信息，请参阅 REST[文档中的 add_starttask。](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)

- **池上的应用程序包引用**：对于需要在池中的每个节点上安装的应用程序或数据。 没有与应用程序包关联的安装命令，但您可以使用启动任务运行任何安装命令。 如果应用程序不需要安装，或者由大量文件组成，则可以使用此方法。 应用程序包非常适合大量文件，因为它们将大量文件引用合并到小负载中。 如果尝试将 100 多个单独的资源文件包含在一个任务中，Batch 服务可能会针对单个任务的内部系统限制。 此外，如果您有严格的版本控制要求，其中可能有许多不同版本的同一应用程序，并且需要在它们之间进行选择，请使用应用程序包。 有关详细信息，请阅读[将应用程序部署到具有 Batch 应用程序包的计算节点](https://docs.microsoft.com/azure/batch/batch-application-packages)。

- **作业准备任务资源文件**：对于必须安装的应用程序或数据，作业才能运行，但不需要安装在整个池上。 例如：如果池具有许多不同类型的作业，并且只有一个作业类型需要 MyApplication.msi 运行，则将安装步骤放入作业准备任务中是有意义的。 有关作业准备任务的详细信息，请参阅[在批处理计算节点上运行作业准备和作业发布任务](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/)。

- **任务资源文件**：用于应用程序或数据仅与单个任务相关时。 例如：您有五个任务，每个任务处理不同的文件，然后将输出写入 Blob 存储。  在这种情况下，应在**任务资源文件**集合上指定输入文件，因为每个任务都有自己的输入文件。

## <a name="determine-the-scope-required-of-a-file"></a>确定文件所需的范围

您需要确定文件的范围 - 是池、作业或任务所需的文件。 限定到池的文件应使用池应用程序包或启动任务。 限定到作业的文件应使用作业准备任务。 在池或作业级别范围的文件的一个好示例是应用程序。 限定到任务的文件应使用任务资源文件。

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>将数据获取到批处理计算节点的其他方法

还有其他方法将数据获取到未正式集成到批处理 REST API 中的批处理计算节点。 由于您可以控制 Azure Batch 节点，并且可以运行自定义可执行文件，因此只要 Batch 节点与目标具有连接，并且具有该源的凭据到 Azure Batch 节点，就可以从任意数量的自定义源中提取数据。 一些常见示例包括：

- 从 SQL 下载数据
- 从其他 Web 服务/自定义位置下载数据
- 映射网络共享

### <a name="azure-storage"></a>Azure 存储

Blob 存储具有下载可伸缩性目标。 Azure 存储文件共享可伸缩性目标与单个 Blob 相同。 大小将影响所需的节点和池数。

