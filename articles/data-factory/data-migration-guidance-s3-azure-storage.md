---
title: 使用 Azure 数据工厂将数据从 Amazon S3 迁移到 Azure 存储
description: 使用 Azure 数据工厂将数据从 Amazon S3 迁移到 Azure 存储。
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
ms.date: 8/04/2019
ms.openlocfilehash: 4d4e0453105dacfbf35624a2a9acb9d5994f4dea
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675745"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>使用 Azure 数据工厂将数据从 Amazon S3 迁移到 Azure 存储 

Azure 数据工厂提供高性能、稳健且经济高效的机制用于将数据从 Amazon S3 大规模迁移到 Azure Blob 存储或 Azure Data Lake Storage Gen2。  本文提供面向数据工程师和开发人员的以下信息： 

> [!div class="checklist"]
> * 性能 
> * 复制复原能力
> * 网络安全性
> * 高级解决方案体系结构 
> * 有关实现的最佳做法  

## <a name="performance"></a>性能

ADF 提供一个可在不同级别实现并行度的无服务器体系结构，使开发人员能够生成管道，以充分利用网络带宽以及存储 IOPS 和带宽将环境的数据移动吞吐量最大化。 

客户已成功地将包含数亿个文件的 PB 量级的数据从 Amazon S3 迁移到 Azure Blob 存储，同时可以保持 2 GBps 或更高的吞吐量。 

![性能](media/data-migration-guidance-s3-to-azure-storage/performance.png)

上图演示了如何通过不同的并行度实现极佳的数据移动速度：
 
- 单个复制活动可以利用可缩放的计算资源：使用 Azure Integration Runtime 时，能够以无服务器方式为每个复制活动指定[最多 256 个 DIU](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units)；使用自承载集成运行时时，可以手动纵向扩展计算机或横向扩展为多个计算机（[最多 4 个节点](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)），单个复制活动会在所有节点之间将其文件集分区。 
- 单个复制活动使用多个线程读取和写入数据存储。 
- ADF 控制流可以同时启动多个复制活动（例如，使用 [For Each 循环](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)）。 

## <a name="resilience"></a>复原能力

在单个复制活动运行中，ADF 具有内置的重试机制，因此，它可以处理数据存储或底层网络中特定级别的暂时性故障。 

执行从 S3 到 Blob 以及从 S3 到 ADLS Gen2 的二元复制时，ADF 会自动执行检查点设置。  如果某个复制活动运行失败或超时，在后续重试时（请确保将重试计数设置为 > 1），复制将从上一个失败点继续，而不是从头开始。 

## <a name="network-security"></a>网络安全性 

ADF 默认通过 HTTPS 协议使用加密的连接将数据从 Amazon S3 传输到 Azure Blob 存储或 Azure Data Lake Storage Gen2。  HTTPS 提供传输中数据加密，并可防止窃听和中间人攻击。 

如果你不希望通过公共 Internet 传输数据，可以通过 AWS Direct Connect 与 Azure Express Route 之间的专用对等互连链路传输数据，以此实现更高的安全性。  请参阅下面的解决方案体系结构来了解如何实现此目的。 

## <a name="solution-architecture"></a>解决方案体系结构

通过公共 Internet 迁移数据：

![solution-architecture-public-network](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- 在此体系结构中，将通过公共 Internet 使用 HTTPS 安全传输数据。 
- 源 Amazon S3 和目标 Azure Blob 存储或 Azure Data Lake Storage Gen2 配置为允许来自所有网络 IP 地址的流量。  请参考下面的第二种体系结构来了解如何限制对特定 IP 范围的网络访问。 
- 可以轻松地以无服务器方式增大计算力，以充分利用网络和存储带宽，获得环境的最佳吞吐量。 
- 可以使用此体系结构实现初始快照迁移和增量数据迁移。 

通过专用链路迁移数据： 

![solution-architecture-private-network](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- 在此体系结构中，数据迁移是通过 AWS Direct Connect 与 Azure Express Route 之间的专用对等互连链路完成的，因此，数据永远不会遍历公共 Internet。  它需要使用 AWS VPC 和 Azure 虚拟网络。 
- 需要在 Azure 虚拟网络中的 Windows VM 上安装 ADF 自承载集成运行时才能实现此体系结构。  可以手动纵向扩展自承载 IR VM 或横向扩展到多个 VM（最多 4 个节点），以充分利用网络和存储 IOPS/带宽。 
- 如果可以接受通过 HTTPS 传输数据，但你想要将源 S3 的网络访问权限锁定为特定的 IP 范围，你可以采用此体系结构的一种变体：删除 AWS VPC 并使用 HTTPS 替换专用链路。  将在 Azure VM 上保留 Azure Virtual 和自承载 IR，因此，可以使用可公开路由的静态 IP 进行白名单操作。 
- 可以使用此体系结构实现初始快照数据迁移和增量数据迁移。 

## <a name="implementation-best-practices"></a>有关实现的最佳做法 

### <a name="authentication-and-credential-management"></a>身份验证和凭据管理 

- 若要对 Amazon S3 帐户进行身份验证，必须使用 [IAM 帐户的访问密钥](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties)。 
- 支持使用多种身份验证类型连接到 Azure Blob 存储。  强烈建议使用 [Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)：托管标识构建在 Azure AD 中自动管理的 ADF 标识基础之上，使你无需在链接服务定义中提供凭据，即可配置管道。  或者，可以使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共享访问签名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)或[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)对 Azure Blob 存储进行身份验证。 
- 也支持使用多种身份验证类型连接到 Azure Data Lake Storage Gen2。  强烈建议使用 [Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)，不过，也可以使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 如果不使用 Azure 资源的托管标识，我们强烈建议[在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)，以便更轻松地集中管理和轮换密钥，而无需修改 ADF 链接服务。  这也是一项 [CI/CD 最佳做法](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)。 

### <a name="initial-snapshot-data-migration"></a>初始快照数据迁移 

建议使用数据分区，尤其是在迁移 10 TB 以上的数据时。  若要将数据分区，请利用“前缀”设置按名称筛选 Amazon S3 中的文件夹和文件，然后，每个 ADF 复制作业一次可以复制一个分区。  可以并行运行多个 ADF 复制作业，以获得更好的吞吐量。 

如果网络或数据存储的暂时性问题导致任何复制作业失败，你可以重新运行失败的复制作业，以再次从 AWS S3 中加载特定的分区。  加载其他分区的所有其他复制作业不受影响。 

### <a name="delta-data-migration"></a>增量数据迁移 

识别 AWS S3 中的新文件或已更改文件的最高效方法是使用时间分区命名约定 - 如果 AWS S3 中的数据经过时间分区，并且文件或文件夹名称中包含时间切片信息（例如 /yyyy/mm/dd/file.csv），则管道可以轻松识别要增量复制的文件/文件夹。 

或者，如果 AWS S3 中的数据未经过时间分区，则 ADF 可按文件的 LastModifiedDate 来识别新文件或更改的文件。   ADF 的工作原理是扫描 AWS S3 中的所有文件，仅复制上次修改时间戳大于特定值的新文件和更新文件。  请注意，如果 S3 中有大量文件，则初始文件扫描可能需要很长时间，而不管有多少个文件与筛选条件相匹配。  在这种情况下，我们建议先将数据分区，并使用同一“前缀”设置进行初始快照迁移，以便可以并行执行文件扫描。  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>对于需要在 Azure VM 上安装自承载集成运行时的方案 

无论是通过专用链路迁移数据，还是想要在 Amazon S3 防火墙中允许特定的 IP 范围，都需要在 Azure Windows VM 上安装自承载集成运行时。 

- 建议先对每个 Azure VM 使用以下配置：Standard_D32s_v3 大小，32 个 vCPU，128 GB 内存。  可以在数据迁移过程中持续监视 IR VM 的 CPU 和内存利用率，以确定是否需要进一步扩展 VM 来提高性能，或缩减 VM 来节省成本。 
- 还可以通过将最多 4 个 VM 节点关联到一个自承载 IR 进行横向扩展。  针对自承载 IR 运行的单个复制作业将自动为文件集分区，并利用所有 VM 节点来并行复制文件。  为实现高可用性，我们建议从 2 个 VM 节点着手，以避免在数据迁移过程中出现单一故障点。 

### <a name="rate-limiting"></a>速率限制 

最佳做法是使用有代表性的示例数据集执行性能 POC，以便能够确定适当的分区大小。 

一开始使用单个分区，以及使用默认 DIU 设置的单个复制活动。  逐渐增大 DIU 设置，直到达到网络的带宽限制或数据存储的 IOPS/带宽限制，或者达到单个复制活动允许的最大 DIU 数目 (256)。 

接下来，逐渐增大并发复制活动的数目，直到达到环境的限制。 

遇到 ADF 复制活动报告的限制错误时，请在 ADF 中减小并发性或 DIU 设置，或考虑提高网络和数据存储的带宽/IOPS 限制。  

### <a name="estimating-price"></a>估算价格 

> [!NOTE]
> 这是一个虚构的定价示例。  实际价格取决于环境中的实际吞吐量。

假设构造了以下管道用于将数据从 S3 迁移到 Azure Blob 存储： 

![pricing-pipeline](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

假设条件如下： 

- 总数据量为 2 PB 
- 使用第一种解决方案体系结构通过 HTTPS 迁移数据 
- 2 PB 划分为 1000 个分区，每个复制操作移动一个分区。 
- 为每个复制作业配置了 DIU=256，可实现 1 GBps 吞吐量 
- ForEach 并发性设置为 2，聚合吞吐量为 2 GBps 
- 完成迁移总共需要花费 292 小时 

下面是根据上述假设的估算出的价格： 

![pricing-table](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>其他参考 
- [Amazon 简单存储服务连接器](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob 存储连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [复制活动性能和优化指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [创建和配置自承载集成运行时](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自承载集成运行时的高可用性和可伸缩性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [数据移动安全注意事项](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [基于时间分区文件名增量复制文件](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [基于 LastModifiedDate 复制新文件和更改的文件](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF 定价页](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>模板

下面是一个[模板](solution-template-migration-s3-azure.md)，可从 Amazon S3 将包含数百个以上文件的数据量迁移到 Azure Data Lake Storage Gen2。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)