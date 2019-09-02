---
title: 使用 Azure 数据工厂将数据从本地 Hadoop 群集迁移到 Azure 存储 |Microsoft Docs
description: 使用 Azure 数据工厂将数据从本地 Hadoop 群集迁移到 Azure 存储。
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
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211612"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>使用 Azure 数据工厂将数据从本地 Hadoop 群集迁移到 Azure 存储 

Azure 数据工厂提供一种功能强大、稳健且经济高效的机制, 用于将数据从本地 HDFS 大规模迁移到 Azure Blob 存储或 Azure Data Lake Storage Gen2。 基本上, Azure 数据工厂包含两种方法, 可用于将数据从本地 HDFS 迁移到 Azure。 你可以根据方案选择每个方案。 

- ADF DistCp 模式。 ADF 支持使用[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html)将文件按原样复制到 Azure Blob (包括[暂存复制](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)) 或 Azure Data Lake Store, 在这种情况下, 它可以充分利用群集的功能, 而不是在 ADF 自承载集成运行时 (IR) 上运行。 当群集非常强大时, 它将提供更好的复制吞吐量。 根据 Azure 数据工厂中的配置, 复制活动会自动构造 DistCp 命令, 将其提交到 Hadoop 群集, 并监视复制状态。 通过使用与 DistCp 集成的 ADF, 客户不仅可以利用现有的强大群集来实现最佳复制吞吐量, 还可以从 ADF 获得灵活的计划和统一的监视体验。 默认情况下, 建议使用 ADF DistCp 模式将数据从本地 hadoop 群集迁移到 Azure。
- ADF 本机 IR 模式。 在某些情况下, DistCp 无法适用于你的情况 (例如, 在 VNET 环境中, DistCp 工具不支持快速路由专用对等互连 + Azure 存储 VNet 终结点)。 此外, 在某些情况下, 你不希望使用现有的 hadoop 群集作为引擎来迁移数据, 因为它会在群集上引入大量负载, 这可能会影响现有 ETL 作业的性能。 如果是这种情况, 则可以使用 ADF 本机功能, 其中 ADF 集成运行时 (IR) 可以是将数据从本地 HDFS 复制到 Azure 的引擎。

本文提供了两种方法来为数据工程师和开发人员提供以下信息:
> [!div class="checklist"]
> * 性能 
> * 复制复原能力
> * 网络安全
> * 高级解决方案体系结构 
> * 实施最佳实践  

## <a name="performance"></a>性能

在 ADF DistCp 模式下, 吞吐量与单独使用 DistCp 工具相同, 它利用现有 hadoop 群集的容量。 DistCp (分布式复制) 是用于大规模/内部群集复制的工具。 它使用 MapReduce 来有效地处理其分布、错误处理和恢复以及报告。 它将文件和目录的列表展开为映射任务的输入, 每个任务都将复制在源列表中指定的文件的分区。 通过使用与 DistCp 集成的 ADF, 你可以构建管道来充分利用网络带宽以及存储 IOPS 和带宽, 以最大程度地提高环境的数据移动吞吐量。  

在 ADF 本机 IR 模式下, 它还允许处于不同级别的并行度, 这可充分利用你的网络带宽以及存储 IOPS 和带宽, 通过手动扩展自承载 IR 计算机或扩展到来最大程度地提高数据移动吞吐量自承载 IR 多台计算机。

- 单个复制活动可以利用可缩放的计算资源。 使用自承载集成运行时, 你可以手动增加计算机或向外扩展到多台计算机 ([最多4个节点](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), 单个复制活动会在所有节点上对其文件集进行分区。 
- 单个复制活动使用多个线程从数据存储区读取数据并将数据写入数据存储区。 
- ADF 控制流可以并行启动多个复制活动, 例如,[为每个循环](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)使用。 

可以从[复制活动性能指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)中获取更多详细信息

## <a name="resilience"></a>恢复能力

在 ADF DistCp 模式下, 你可以利用不同的 DistCp 命令行参数 (例如,-i、忽略失败;-更新、在源文件和目标文件的大小不同时写入数据) 来实现不同级别的复原能力。

在 ADF 本机 IR 模式下, 在单个复制活动运行中, ADF 具有内置的重试机制, 因此它可以处理数据存储或基础网络中特定级别的暂时性故障。 在从本地 HDFS 复制到 Blob 以及从本地 HDFS 复制到 ADLS Gen2 时, ADF 会自动对大范围执行检查点操作。 如果复制活动运行失败或超时, 则在后续重试时 (请确保重试计数 > 1), 副本将从上一个故障点继续, 而不是从开始处恢复。

## <a name="network-security"></a>网络安全 

默认情况下, ADF 使用通过 HTTPS 协议加密的连接, 将数据从本地 HDFS 传输到 Azure Blob 存储或 Azure Data Lake Storage Gen2。  HTTPS 在传输过程中提供数据加密, 并防止窃听和中间人攻击。 

或者, 如果你不希望通过公共 Internet 传输数据, 则可以通过 Azure Express Route 通过专用对等互连链接传输数据, 从而实现更高的安全性。 请参阅下面的解决方案体系结构, 了解如何实现此目的。

## <a name="solution-architecture"></a>解决方案体系结构

通过公共 Internet 迁移数据:

![解决方案体系结构-公共网络](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- 在此体系结构中, 通过公共 Internet 使用 HTTPS 安全地传输数据。 
- 建议在公用网络环境中使用 ADF DistCp 模式。 这样, 你不仅可以利用现有的强大群集来实现最佳的复制吞吐量, 而且还可以从 ADF 获得灵活的计划和统一的监视体验。
- 需要在企业防火墙后面的 Windows 计算机上安装 ADF 自承载集成运行时, 将 DistCp 命令提交到 hadoop 群集, 并监视复制状态。 由于此计算机不是移动数据的引擎 (仅用于控制目的), 因此计算机的容量不会影响数据移动的吞吐量。
- 支持 DistCp 命令中的现有参数。


迁移专用链接上的数据: 

![解决方案-体系结构-专用-网络](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- 在此体系结构中, 数据迁移通过 Azure Express Route 在专用对等互连链接上完成, 使数据永远不会通过公共 Internet 进行遍历。
- DistCp 工具不支持快速路由专用对等互连 + Azure 存储 VNet 终结点, 因此建议通过集成运行时使用 ADF 本机功能来迁移数据。
- 需要在 Azure 虚拟网络内的 Windows VM 上安装 ADF 自承载集成运行时才能实现此体系结构。 可以手动增加 VM 或向外扩展到多个 Vm, 以充分利用网络和存储 IOPS/带宽。
- 为每个 Azure VM (已安装 ADF 自承载集成运行时) 启动的建议配置是 Standard_D32s_v3 32 vCPU 和 128 GB 内存。 在数据迁移期间, 你可以继续监视 VM 的 CPU 和内存利用率, 以确定是否需要进一步增加 VM 以提高性能, 或缩减 VM 以节省成本。
- 你还可以通过将最多4个 VM 节点与一个自承载 IR 关联来进行扩展。 针对自承载 IR 运行的单个复制作业将自动对文件集进行分区, 并利用所有 VM 节点并行复制文件。 为实现高可用性, 建议从两个 VM 节点开始, 以避免在数据迁移过程中出现单一故障点。
- 初始快照数据迁移和增量数据迁移都可以使用此体系结构来实现。


## <a name="implementation-best-practices"></a>实施最佳实践 

### <a name="authentication-and-credential-management"></a>身份验证和凭据管理 

- 若要向 HDFS 进行身份验证, 可以使用[Windows (Kerberos) 或匿名](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)的。 
- 支持多种身份验证类型以连接到 Azure Blob 存储。  强烈建议使用[Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity): 在自动托管的 ADF 上构建在 Azure AD 中, 它使你能够配置管道, 而无需在链接的服务定义中提供凭据。  或者, 你可以使用 "[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)"、"[共享访问签名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)" 或 "[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)" 对 Azure Blob 存储进行身份验证。 
- 还支持多种身份验证类型以连接到 Azure Data Lake Storage Gen2。  强烈建议[对 Azure 资源使用托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity), 不过也可以使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 如果不使用 Azure 资源的托管标识, 则强烈建议[在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault), 以便更轻松地集中管理和旋转密钥, 而无需修改 ADF 链接服务。  这也是[CI/CD 的最佳实践](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)之一。 

### <a name="initial-snapshot-data-migration"></a>初始快照数据迁移 

在 ADF DistCp 模式下, 你可以创建一个复制活动来提交带有不同参数的 DistCp 命令, 以控制初始数据迁移行为。 

在 ADF 本机 IR 模式下, 建议在迁移超过 10 TB 的数据时使用数据分区。 若要对数据进行分区, 请在 HDFS 上利用文件夹名称, 然后每个 ADF 复制作业一次只能复制一个文件夹分区。 可以并发运行多个 ADF 复制作业, 以获得更好的吞吐量。
如果任何复制作业由于网络或数据存储暂时性问题而失败, 则可以重新运行失败的复制作业, 以再次从 HDFS 重新加载该特定分区。 加载其他分区的所有其他复制作业都不会受到影响。

### <a name="delta-data-migration"></a>增量数据迁移 

在 ADF DistCp 模式下, 你可以利用 DistCp 命令行参数 "-更新, 在源文件和目标文件的大小不同时写入数据" 以实现增量数据迁移。

在 ADF 本机 IR 模式下, 通过使用时间分区命名约定来识别 hdfs 中新文件或已更改文件的最高性能方法是: HDFS 中的数据在文件或文件夹名称中使用时间段信息进行了时间分区 (例如,/yyyy/mm/dd/文件 .csv), 则管道可以轻松地识别要增量复制的文件/文件夹。
或者, 如果你在 HDFS 中的数据没有时间分区, 则 ADF 可以通过其 LastModifiedDate 识别新文件或更改的文件。 它的工作方式是 ADF 将扫描 HDFS 中的所有文件, 并且仅复制上次修改时间戳大于特定值的新文件和已更新文件。 请注意, 如果 HDFS 中有大量文件, 则初始文件扫描可能需要很长时间, 而不考虑与筛选条件匹配的文件数。 在这种情况下, 建议先对数据进行分区, 并使用同一分区进行初始快照迁移, 以便可以并行执行文件扫描。

### <a name="estimating-price"></a>估计价格 

请考虑以下构造用于将数据从 HDFS 迁移到 Azure Blob 存储的管道: 

![定价-管道](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

让我们假设以下内容: 

- 总数据量为 1 PB
- 使用第二种解决方案体系结构 (ADF 本机 IR 模式) 迁移数据
- 1 PB 分为1000个分区, 每个副本移动一个分区
- 每个复制活动都配置有一个与4台计算机关联的自承载 IR, 并达到 500 MBps 吞吐量。
- ForEach concurrency 设置为 4, 聚合吞吐量为 2 GBps
- 总共需要146小时才能完成迁移。


下面是基于上述假设的预计价格: 

![定价-表](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> 这是一个假设的定价示例。  实际定价取决于环境中的实际吞吐量。
> 不包括 Azure Windows VM 的价格 (安装了自承载集成运行时)。

### <a name="additional-references"></a>其他参考 
- [HDFS 连接器](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob 存储连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [复制活动性能优化指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [创建和配置自承载 Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自承载集成运行时高可用性和可伸缩性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [数据移动安全注意事项](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [基于时间分区文件名增量复制文件](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [基于 LastModifiedDate 复制新文件和更改的文件](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF 定价页](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)