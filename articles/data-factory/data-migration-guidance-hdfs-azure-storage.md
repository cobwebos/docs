---
title: 使用 Azure 数据工厂将数据从本地 Hadoop 群集迁移到 Azure 存储
description: 了解如何使用 Azure 数据工厂将数据从本地 Hadoop 群集迁移到 Azure 存储。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/30/2019
ms.openlocfilehash: b952be49bf5bc00b338aa04ed51e9dc451b5c4f9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675814"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>使用 Azure 数据工厂将数据从本地 Hadoop 群集迁移到 Azure 存储 

Azure 数据工厂提供一种功能强大、稳健且经济高效的机制，用于将数据从本地 HDFS 大规模迁移到 Azure Blob 存储或 Azure Data Lake Storage Gen2。 

数据工厂提供了两种基本方法，用于将数据从本地 HDFS 迁移到 Azure。 你可以根据方案选择方法。 

- **数据工厂 DistCp 模式**（推荐）：在数据工厂中，可使用[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) （分布式复制）按原样将文件复制到 Azure Blob 存储（包括[暂存复制](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)）或 Azure Data Lake Store Gen2。 使用集成了 DistCp 的数据工厂来利用现有的强大群集来实现最佳的复制吞吐量。 您还可以从数据工厂获得灵活的计划和统一的监视体验。 根据数据工厂配置，复制活动会自动构造 DistCp 命令，将数据提交到 Hadoop 群集，然后监视复制状态。 建议使用数据工厂 DistCp 模式将数据从本地 Hadoop 群集迁移到 Azure。
- **数据工厂本机集成运行时模式**： DistCp 不是所有方案中的选项。 例如，在 Azure 虚拟网络环境中，DistCp 工具不支持与 Azure 存储虚拟网络终结点建立 Azure ExpressRoute 专用对等互连。 此外，在某些情况下，你不希望使用现有的 Hadoop 群集作为迁移数据的引擎，因此你不会在群集上施加繁重的负载，这可能会影响现有 ETL 作业的性能。 相反，可以使用数据工厂集成运行时的本机功能作为将数据从本地 HDFS 复制到 Azure 的引擎。

本文提供了有关这两种方法的下列信息：
> [!div class="checklist"]
> * 性能 
> * 复制复原能力
> * 网络安全性
> * 高级解决方案体系结构 
> * 有关实现的最佳做法  

## <a name="performance"></a>性能

在数据工厂 DistCp 模式下，吞吐量与单独使用 DistCp 工具时的吞吐量相同。 数据工厂 DistCp 模式最大限度地提高了现有 Hadoop 群集的容量。 可以使用 DistCp 进行大型群集间复制或群集内复制。 

DistCp 使用 MapReduce 来有效地处理其分布、错误处理和恢复以及报告。 它将文件和目录的列表展开为任务映射的输入。 每个任务复制在源列表中指定的文件分区。 你可以使用集成了 DistCp 的数据工厂构建管道，以充分利用网络带宽、存储 IOPS 和带宽，以最大程度地提高环境的数据移动吞吐量。  

数据工厂本机集成运行时模式也允许处于不同级别的并行度。 可以使用并行来充分利用网络带宽、存储 IOPS 和带宽，以最大程度地提高数据移动吞吐量：

- 单个复制活动可以利用可缩放的计算资源。 使用自承载集成运行时，你可以手动增加计算机或向外扩展到多台计算机（[最多四个节点](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)）。 单个复制活动在所有节点上对其文件集进行分区。 
- 单个复制活动通过使用多个线程从数据存储区读取数据并将数据写入数据存储区。 
- 数据工厂控制流可以并行启动多个复制活动。 例如，可以[对每个循环](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)使用。 

有关详细信息，请参阅[复制活动性能指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)。

## <a name="resilience"></a>复原能力

在 "数据工厂" DistCp 模式下，你可以使用不同的 DistCp 命令行参数（例如 `-i`、忽略失败或 `-update`、在源文件和目标文件的大小不同时写入数据）以获得不同级别的复原能力。

在数据工厂本机集成运行时模式下，在单个复制活动运行中，数据工厂具有内置的重试机制。 它可以处理数据存储或基础网络中的特定暂时性故障级别。 

在从本地 HDFS 复制到 Blob 存储以及从本地 HDFS 复制到 Data Lake Store Gen2 时，数据工厂会自动执行对较大范围的检查点操作。 如果复制活动运行失败或超时，则在后续重试时（请确保重试计数为 > 1），副本将从上一个故障点继续，而不是从开头开始。

## <a name="network-security"></a>网络安全性 

默认情况下，数据工厂将数据从本地 HDFS 传输到 Blob 存储，或使用通过 HTTPS 协议加密的连接 Azure Data Lake Storage Gen2。 HTTPS 提供传输中数据加密，并可防止窃听和中间人攻击。 

或者，如果你不希望通过公共 internet 传输数据，为了提高安全性，你可以通过 ExpressRoute 通过专用对等互连链接传输数据。 

## <a name="solution-architecture"></a>解决方案体系结构

此图描述了如何通过公共 internet 迁移数据：

![显示用于在公共网络上迁移数据的解决方案体系结构的关系图](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- 在此体系结构中，通过公共 internet 使用 HTTPS 安全地传输数据。 
- 建议在公用网络环境中使用数据工厂 DistCp 模式。 你可以利用强大的现有群集来实现最佳的复制吞吐量。 您还可以从数据工厂获得灵活的计划和统一的监视体验。
- 对于此体系结构，必须在企业防火墙后面的 Windows 计算机上安装数据工厂自承载集成运行时，将 DistCp 命令提交到 Hadoop 群集并监视复制状态。 由于计算机不是要移动数据的引擎（仅用于控制用途），因此计算机的容量不会影响数据移动的吞吐量。
- 支持 DistCp 命令中的现有参数。

此图描述了通过专用链接迁移数据的步骤： 

![显示用于在专用网络上迁移数据的解决方案体系结构的关系图](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- 在此体系结构中，通过 Azure ExpressRoute 通过专用对等互连链接来迁移数据。 数据永远不会通过公共 internet 进行遍历。
- DistCp 工具不支持与 Azure 存储虚拟网络终结点的 ExpressRoute 专用对等互连。 建议你通过集成运行时使用数据工厂的本机功能来迁移数据。
- 对于此体系结构，必须在 Azure 虚拟网络中的 Windows VM 上安装数据工厂自承载集成运行时。 可以手动增加 VM 或向外扩展到多个 Vm，以充分利用网络和存储 IOPS 或带宽。
- 为每个 Azure VM （安装了数据工厂自承载集成运行时）启动的建议配置是 Standard_D32s_v3 32 vCPU 和 128 GB 的内存。 可在数据迁移过程中监视 VM 的 CPU 和内存使用情况，以确定是否需要纵向扩展 VM 以获得更好的性能，或缩减 VM 以降低成本。
- 你还可以通过将最多4个 VM 节点与一个自承载集成运行时关联来向外扩展。 针对自承载集成运行时运行的单个复制作业会自动对文件集进行分区，并使用所有 VM 节点并行复制文件。 为实现高可用性，我们建议从两个 VM 节点开始，避免在数据迁移过程中出现单点故障方案。
- 使用此体系结构时，可以使用初始快照数据迁移和增量数据迁移。

## <a name="implementation-best-practices"></a>有关实现的最佳做法

建议你在实现数据迁移时遵循这些最佳做法。

### <a name="authentication-and-credential-management"></a>身份验证和凭据管理 

- 若要对 HDFS 进行身份验证，可以使用 [Windows (Kerberos) 或“匿名”](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)。 
- 支持多种身份验证类型以连接到 Azure Blob 存储。  强烈建议使用[Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)。 托管标识在 Azure Active Directory （Azure AD）中的自动托管数据工厂标识的基础上构建，使你能够配置管道，而无需在链接的服务定义中提供凭据。 或者，你可以通过使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共享访问签名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)或[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)对 Blob 存储进行身份验证。 
- 还支持多种身份验证类型以连接到 Data Lake Storage Gen2。  我们强烈建议使用[Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)，但也可以使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 如果不使用 Azure 资源的托管标识，强烈建议将[凭据存储在 Azure Key Vault 中](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)，以便更轻松地集中管理和旋转密钥，而无需修改数据工厂链接服务。 这也是[CI/CD 的最佳实践](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)。 

### <a name="initial-snapshot-data-migration"></a>初始快照数据迁移 

在 "数据工厂" DistCp 模式下，你可以创建一个复制活动来提交 DistCp 命令，并使用不同的参数来控制初始数据迁移行为。 

在 "数据工厂本机集成运行时" 模式下，我们建议数据分区，尤其是迁移超过 10 TB 的数据时。 若要对数据进行分区，请使用 HDFS 上的文件夹名称。 然后，每个数据工厂复制作业一次可以复制一个文件夹分区。 可以并发运行多个数据工厂复制作业，以获得更好的吞吐量。

如果任何复制作业由于网络或数据存储暂时性问题而失败，则可以重新运行失败的复制作业，以从 HDFS 重载该特定分区。 其他加载其他分区的复制作业不受影响。

### <a name="delta-data-migration"></a>增量数据迁移 

在 "数据工厂" DistCp 模式下，可以使用 DistCp 命令行参数 `-update`，在源文件和目标文件的大小不同时写入数据，以进行增量数据迁移。

在数据工厂本机集成模式下，通过使用时间分区命名约定，从 HDFS 标识新文件或更改的文件的最高性能方法是使用分区命名约定。 当 HDFS 中的数据已通过文件或文件夹名称中的时间段信息进行了时间分区（例如， */yyyy/mm/dd/file.csv*）时，管道可以轻松地识别要增量复制的文件和文件夹。

或者，如果你在 HDFS 中的数据没有时间分区，数据工厂可以使用其**LastModifiedDate**值标识新文件或已更改的文件。 数据工厂从 HDFS 扫描所有文件，并且仅复制新的和更新的文件，这些文件的上次修改时间戳大于设置值。 

如果 HDFS 中有大量文件，则初始文件扫描可能需要较长时间，而不考虑与筛选条件匹配的文件数。 在此方案中，我们建议您首先使用用于初始快照迁移的同一分区对数据进行分区。 然后，文件扫描可能会并行发生。

### <a name="estimate-price"></a>估计价格 

请考虑以下用于将数据从 HDFS 迁移到 Azure Blob 存储的管道： 

![显示定价管道的关系图](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

假设有以下信息： 

- 总数据量为 1 PB。
- 使用数据工厂本机集成运行时模式迁移数据。
- 1 PB 分为1000个分区，每个副本移动一个分区。
- 每个复制活动都配置有一个自承载集成运行时，该运行时与四台计算机关联，并达到 500 MBps 的吞吐量。
- ForEach concurrency 设置为**4** ，聚合吞吐量为 2 GBps。
- 完成迁移总共需要花费 146 小时。

下面是基于假设的预计价格： 

![显示价格计算的表](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> 这是一个虚构的定价示例。 实际价格取决于环境中的实际吞吐量。
> 不包括 Azure Windows VM 的价格（安装了自承载集成运行时）。

### <a name="additional-references"></a>其他参考

- [HDFS 连接器](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob 存储连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [复制活动性能和优化指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [创建和配置自承载集成运行时](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自承载集成运行时高可用性和可伸缩性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [数据移动安全注意事项](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [基于时间分区的文件名以增量方式复制文件](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [基于 LastModifiedDate 复制新文件和更改的文件](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [数据工厂定价页](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂从多个容器复制文件](solution-template-copy-files-multiple-containers.md)