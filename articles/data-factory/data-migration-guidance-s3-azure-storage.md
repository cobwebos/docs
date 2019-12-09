---
title: 将数据从 Amazon S3 迁移到 Azure 存储
description: 使用 Azure 数据工厂将数据从 Amazon S3 迁移到 Azure 存储。
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 30990c3d1e3f885e8984227425d3e8e5c44b9286
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927476"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>使用 Azure 数据工厂将数据从 Amazon S3 迁移到 Azure 存储 

Azure 数据工厂提供一种功能强大、稳健且经济高效的机制，用于大规模将数据从 Amazon S3 迁移到 Azure Blob 存储或 Azure Data Lake Storage Gen2。  本文提供了以下数据工程师和开发人员信息： 

> [!div class="checklist"]
> * 性能 
> * 复制复原能力
> * 网络安全
> * 高级解决方案体系结构 
> * 实施最佳实践  

## <a name="performance"></a>性能

ADF 提供的无服务器体系结构允许不同级别的并行度，这允许开发人员构建管道来充分利用网络带宽以及存储 IOPS 和带宽，最大程度地提高环境的数据移动吞吐量。 

客户已成功地将 pb 的数据从 Amazon S3 迁移到 Azure Blob 存储，并具有 2 GBps 和更高的持续吞吐量。 

![性能](media/data-migration-guidance-s3-to-azure-storage/performance.png)

上图演示了如何通过不同级别的并行度实现极佳的数据移动速度：
 
- 单个复制活动可以利用可缩放的计算资源：使用 Azure Integration Runtime 时，可以以无服务器方式为每个复制活动指定[最多 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) ;使用自承载 Integration Runtime 时，可以手动纵向扩展计算机或向外扩展到多台计算机（[最多4个节点](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)），单个复制活动会将其文件集分区在所有节点上。 
- 单个复制活动使用多个线程从数据存储区读取数据并将数据写入数据存储区。 
- ADF 控制流可以并行启动多个复制活动，例如，[为每个循环](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)使用。 

## <a name="resilience"></a>恢复能力

在单个复制活动运行中，ADF 具有内置的重试机制，因此它可以处理数据存储或基础网络中特定级别的暂时性故障。 

执行从 S3 到 Blob 以及从 S3 到 ADLS Gen2 的二进制复制时，ADF 会自动执行检查点操作。  如果复制活动运行失败或超时，则在后续重试时（请确保重试计数 > 1），副本将从上一个故障点继续，而不是从开始处恢复。 

## <a name="network-security"></a>网络安全 

默认情况下，ADF 使用通过 HTTPS 协议加密的连接，将数据从 Amazon S3 传输到 Azure Blob 存储或 Azure Data Lake Storage Gen2。  HTTPS 在传输过程中提供数据加密，并防止窃听和中间人攻击。 

或者，如果你不希望通过公共 Internet 传输数据，则可以通过在 AWS Direct Connect 与 Azure Express Route 之间通过专用对等互连链路传输数据来实现更高的安全性。  请参阅下面的解决方案体系结构，了解如何实现此目的。 

## <a name="solution-architecture"></a>解决方案体系结构

通过公共 Internet 迁移数据：

![解决方案体系结构-公共网络](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- 在此体系结构中，通过公共 Internet 使用 HTTPS 安全地传输数据。 
- 源 Amazon S3 以及目标 Azure Blob 存储或 Azure Data Lake Storage Gen2 都配置为允许来自所有网络 IP 地址的流量。  请参阅下面的第二个体系结构，了解如何限制对特定 IP 范围的网络访问。 
- 您可以轻松地以无服务器的方式增加动力，以充分利用您的网络和存储带宽，以便您的环境获得最佳吞吐量。 
- 初始快照迁移和增量数据迁移都可以使用此体系结构来实现。 

迁移专用链接上的数据： 

![解决方案-体系结构-专用-网络](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- 在此体系结构中，数据迁移通过 AWS 直接连接和 Azure Express 路由之间的专用对等互连链路来完成，使数据永远不会通过公共 Internet 进行遍历。  它需要使用 AWS VPC 和 Azure 虚拟网络。 
- 需要在 Azure 虚拟网络内的 Windows VM 上安装 ADF 自承载集成运行时才能实现此体系结构。  你可以手动扩展你的自承载 IR Vm，或向外扩展到多个 Vm （最多4个节点）以充分利用你的网络和存储 IOPS/带宽。 
- 如果通过 HTTPS 传输数据是可接受的，但你想要将对源 S3 的网络访问锁定到特定的 IP 范围，则可以通过删除 AWS VPC 并使用 HTTPS 替换 private 链接来采用此体系结构的变化形式。  你需要将 Azure 虚拟机和自承载 IR 保留在 Azure VM 上，以便可以将静态可路由的 IP 用于允许列表目的。 
- 初始快照数据迁移和增量数据迁移都可以使用此体系结构来实现。 

## <a name="implementation-best-practices"></a>实施最佳实践 

### <a name="authentication-and-credential-management"></a>身份验证和凭据管理 

- 若要对 Amazon S3 帐户进行身份验证，必须使用[IAM 帐户的访问密钥](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties)。 
- 支持多种身份验证类型以连接到 Azure Blob 存储。  强烈建议使用[Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)：在自动托管的 ADF 上构建在 Azure AD 中，它使你能够配置管道，而无需在链接的服务定义中提供凭据。  或者，你可以使用 "[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)"、"[共享访问签名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)" 或 "[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)" 对 Azure Blob 存储进行身份验证。 
- 还支持多种身份验证类型以连接到 Azure Data Lake Storage Gen2。  强烈建议[对 Azure 资源使用托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)，不过也可以使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 如果不使用 Azure 资源的托管标识，则强烈建议[在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)，以便更轻松地集中管理和旋转密钥，而无需修改 ADF 链接服务。  这也是[CI/CD 的最佳实践](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)之一。 

### <a name="initial-snapshot-data-migration"></a>初始快照数据迁移 

建议在迁移超过 10 TB 的数据时使用数据分区。  若要对数据进行分区，请利用 "前缀" 设置按名称筛选 Amazon S3 中的文件夹和文件，然后每个 ADF 复制作业可以一次复制一个分区。  可以并发运行多个 ADF 复制作业，以获得更好的吞吐量。 

如果任何复制作业由于网络或数据存储暂时性问题而失败，则可以重新运行失败的复制作业，重新加载 AWS S3 中的特定分区。  加载其他分区的所有其他复制作业都不会受到影响。 

### <a name="delta-data-migration"></a>增量数据迁移 

通过使用时间分区命名约定来识别新的或更改的文件的最高性能方法是使用时间分区命名约定–当 AWS S3 中的数据已使用文件或文件夹名称中的时间段信息进行了时间（例如，/yyyy/mm/dd/file.csv）时，你的管道可以轻松地识别要增量复制的文件/文件夹。 

或者，如果 AWS S3 中的数据未进行时间分区，则 ADF 可以通过 LastModifiedDate 来识别新文件或更改的文件。   它的工作方式是 ADF 将扫描 AWS S3 中的所有文件，并且仅复制最后修改的时间戳大于特定值的新文件和已更新文件。  请注意，如果在 S3 中有大量文件，则初始文件扫描可能需要很长时间，而不考虑与筛选条件匹配的文件数。  在这种情况下，建议先使用与初始快照迁移相同的 "前缀" 设置对数据进行分区，以便可以并行进行文件扫描。  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>在 Azure VM 上需要自承载集成运行时的情况 

无论是通过专用链接迁移数据，还是希望允许在 Amazon S3 防火墙上启用特定的 IP 范围，都需要在 Azure Windows VM 上安装自承载集成运行时。 

- 对于每个 Azure VM，要从每个 Azure VM 开始 Standard_D32s_v3 建议配置为 32 vCPU 和 128 GB 内存。  可在数据迁移过程中持续监视 IR VM 的 CPU 和内存利用率，以确定是否需要进一步增加 VM 的性能，或缩小 VM 以节省成本。 
- 你还可以通过将最多4个 VM 节点与一个自承载 IR 关联来进行扩展。  针对自承载 IR 运行的单个复制作业将自动对文件集进行分区，并利用所有 VM 节点并行复制文件。  为实现高可用性，建议从2个 VM 节点开始，以避免在数据迁移过程中出现单一故障点。 

### <a name="rate-limiting"></a>速率限制 

最佳做法是使用有代表性的示例数据集来执行性能 POC，以便确定适当的分区大小。 

从单个分区开始，使用默认的 DIU 设置启动单个复制活动。  逐渐增加 DIU 设置，直到达到网络的带宽限制或数据存储的 IOPS/带宽限制，或者已达到单个复制活动允许的最大 256 DIU。 

接下来，逐渐增加并发复制活动的数量，直到达到环境限制。 

当你遇到 ADF 复制活动报告的限制错误时，减少 ADF 中的 concurrency 或 DIU 设置，或者考虑增加网络和数据存储的带宽/IOPS 限制。  

### <a name="estimating-price"></a>估计价格 

> [!NOTE]
> 这是一个假设的定价示例。  实际定价取决于环境中的实际吞吐量。

请考虑以下构造用于将数据从 S3 迁移到 Azure Blob 存储的管道： 

![定价-管道](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

让我们假设以下内容： 

- 总数据量为 2 PB 
- 使用第一个解决方案体系结构通过 HTTPS 迁移数据 
- 2 PB 分为 1 K 分区，每个副本移动一个分区 
- 每个副本都配置了 DIU = 256，实现 1 GBps 的吞吐量 
- ForEach concurrency 设置为2，聚合吞吐量为 2 GBps 
- 总共需要292小时才能完成迁移 

下面是基于上述假设的预计价格： 

![定价-表](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>其他参考 
- [Amazon 简单存储服务连接器](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
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

## <a name="template"></a>模板

下面是一个[模板](solution-template-migration-s3-azure.md)，可从 Amazon S3 将包含数百个以上文件的数据量迁移到 Azure Data Lake Storage Gen2。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)