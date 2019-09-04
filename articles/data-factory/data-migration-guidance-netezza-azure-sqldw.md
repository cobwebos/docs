---
title: 使用 Azure 数据工厂将数据从本地 Netezza 服务器迁移到 Azure |Microsoft Docs
description: 使用 Azure 数据工厂将数据从本地 Netezza 服务器迁移到 Azure。
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
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259556"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>使用 Azure 数据工厂将数据从本地 Netezza 服务器迁移到 Azure 

Azure 数据工厂提供一种功能强大、稳健且经济高效的机制，用于将数据大规模从本地 Netezza 服务器迁移到 Azure 存储或 Azure SQL 数据仓库。 本文提供了以下数据工程师和开发人员信息：

> [!div class="checklist"]
> * 性能 
> * 复制复原能力
> * 网络安全
> * 高级解决方案体系结构 
> * 实施最佳实践  

## <a name="performance"></a>性能

Azure 数据工厂提供了一个无服务器体系结构，该体系结构允许不同级别的并行，使开发人员能够构建管道来充分利用网络带宽和数据库带宽，以最大程度地提高数据移动吞吐量环境.

![性能](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- 单个复制活动可以利用可缩放的计算资源：使用 Azure Integration Runtime 时，可以以无服务器方式为每个复制活动指定[最多 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) ;使用自承载 Integration Runtime 时，可以手动纵向扩展计算机或向外扩展到多台计算机（[最多4个节点](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)），而单个复制活动会将其分区分布到所有节点上。 
- 单个复制活动使用多个线程从数据存储区读取数据并将数据写入数据存储区。 
- Azure 数据工厂控制流可以并行启动多个复制活动，例如，[为每个循环](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)使用。 

可以从[复制活动性能指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)中获取更多详细信息

## <a name="resilience"></a>恢复能力

在单个复制活动运行中，Azure 数据工厂具有内置的重试机制，因此它可以处理数据存储或基础网络中特定级别的暂时性故障。

在源数据存储和接收器数据存储之间复制数据时，Azure 数据工厂复制活动还提供两种处理不兼容行的方法。 如果遇到不兼容的数据，则可以中止并使复制活动失败，也可以通过跳过不兼容的数据行来继续复制 rest 数据。 此外，还可以在 Azure Blob 存储或 Azure Data Lake Store 中记录不兼容的行，以了解失败的原因，修复数据源上的数据，并重试复制活动。

## <a name="network-security"></a>网络安全 

默认情况下，Azure 数据工厂使用通过 HTTPS 协议加密的连接，将数据从本地 Netezza 服务器传输到 Azure 存储或 Azure SQL 数据仓库。 它提供传输中的数据加密，并防止窃听和中间人攻击。

或者，如果你不希望通过公共 Internet 传输数据，则可以通过 Azure Express Route 通过专用对等互连链接传输数据，从而实现更高的安全性。 请参阅下面的解决方案体系结构，了解如何实现此目的。

## <a name="solution-architecture"></a>解决方案体系结构

通过公共 Internet 迁移数据：

![解决方案体系结构-公共网络](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- 在此体系结构中，通过公共 Internet 使用 https 安全地传输数据。
- 需要在企业防火墙后面的 windows 计算机上安装 Azure 数据工厂自承载集成运行时，才能实现此体系结构。 确保 windows 计算机上的 Azure 数据工厂自承载集成运行时可以直接获取对 Netezza 服务器的访问权限。 可以手动扩展计算机或扩展到多台计算机，以充分利用网络和数据存储带宽来复制数据。
- 初始快照数据迁移和增量数据迁移都可以使用此体系结构来实现。

迁移专用链接上的数据： 

![解决方案-体系结构-专用-网络](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- 在此体系结构中，数据迁移通过 Azure Express Route 在专用对等互连链接上完成，使数据永远不会通过公共 Internet 进行遍历。 
- 需要在 Azure 虚拟网络中的 Windows VM 上安装 Azure 数据工厂自承载集成运行时才能实现此体系结构。 你可以手动增加 Vm 或向外扩展到多个 Vm，以充分利用你的网络和数据存储带宽来复制数据。
- 初始快照数据迁移和增量数据迁移都可以使用此体系结构来实现。

## <a name="implementation-best-practices"></a>实施最佳实践 

### <a name="authentication-and-credential-management"></a>身份验证和凭据管理 

- 若要对 Netezza 进行身份验证，可以[通过连接字符串使用 ODBC 身份验证](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)。 
- 支持多种身份验证类型以连接到 Azure Blob 存储。  强烈建议使用[Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)：内置在 Azure AD 中的自动托管的 Azure 数据工厂中，它使你能够配置管道，而无需在链接的服务定义中提供凭据。  或者，你可以使用 "[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)"、"[共享访问签名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)" 或 "[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)" 对 Azure Blob 存储进行身份验证。 
- 还支持多种身份验证类型以连接到 Azure Data Lake Storage Gen2。  强烈建议[对 Azure 资源使用托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)，不过也可以使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 还支持多种身份验证类型以连接到 Azure SQL 数据仓库。 强烈建议[对 Azure 资源使用托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)，不过也可以使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication)或[SQL 身份验证](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)。
- 如果不使用 Azure 资源的托管标识，则强烈建议[在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)，以便更轻松地集中管理和旋转密钥，而无需修改 Azure 数据工厂链接服务。  这也是[CI/CD 的最佳实践](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)之一。 

### <a name="initial-snapshot-data-migration"></a>初始快照数据迁移 

对于较小的表，其卷大小小于 100 GB，或者可以在2小时内迁移到 Azure，你可以使每个复制作业每个表加载数据。 可以运行多个 Azure 数据工厂复制作业来并发加载不同的表，以获得更好的吞吐量。 

在每个复制作业中，还可以通过使用[parallelCopies 设置](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy)和数据分区选项来执行并行查询，并按分区复制数据，从而达到一定程度的并行度。 下面详细介绍了两个数据分区选项。
- 建议从数据切片开始，因为这样会更有效。  请确保 parallelCopies 中的并行度设置低于 Netezza 服务器上表中数据切片分区的总数。  
- 如果每个数据切片分区的卷大小仍很大（例如，大于 10 GB），则建议切换到动态范围分区，以便定义分区数以及每个分区的卷大小。按分区列、上限和下限。

如果大表的卷大小大于 100 GB，或在2小时内无法迁移到 Azure，则建议使用自定义查询对数据进行分区，然后使每个复制作业一次复制一个分区。 可以并发运行多个 Azure 数据工厂复制作业，以获得更好的吞吐量。 请注意，对于每个复制作业目标要通过自定义查询加载一个分区，你仍可以通过数据切片或动态范围启用并行度以增加吞吐量。 

如果任何复制作业由于网络或数据存储暂时性问题而失败，则可以重新运行失败的复制作业，重新加载表中的特定分区。 加载其他分区的所有其他复制作业都不会受到影响。

在将数据加载到 Azure SQL 数据仓库时，建议在使用 Azure blob 存储作为暂存的复制作业内启用 Polybase。

### <a name="delta-data-migration"></a>增量数据迁移 

标识表中新行或更新行的方式是在架构中使用时间戳列或递增键，然后将最新值存储为外部表中的高水印，这可用于筛选增量数据以便在下次加载数据时使用。 

不同的表可以使用不同的水印列来标识新行或更新的行。 建议创建一个外部控制表，其中每行表示 Netezza 服务器上的一个表，其特定的水印列名称和高水印值。 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Azure VM 或计算机上的自承载集成运行时配置

假设你要将数据从 Netezza 服务器迁移到 Azure，无论 Netezza 服务器是在公司防火墙后还是在 VNET 环境中承诺，你都需要在 windows 计算机或 VM 上安装自承载集成运行时，这是要移动的引擎数据.

- 对于每个计算机或 VM，要从其开始的建议配置是具有 32 vCPU 和 128 GB 的内存。 您可以在数据迁移过程中持续监视 IR 计算机的 CPU 和内存利用率，以确定是否需要进一步扩展计算机以获得更好的性能，或缩小计算机以节省成本。
- 还可以通过将最多4个节点与一个自承载 IR 关联来向外扩展。 对自承载 IR 运行的单个复制作业将自动利用所有 VM 节点以并行方式复制数据。 为实现高可用性，建议从2个 VM 节点开始，以避免在数据迁移过程中出现单一故障点。

### <a name="rate-limiting"></a>速率限制

最佳做法是使用有代表性的示例数据集来执行性能 POC，以便确定每个复制活动的适当分区大小。 建议在2小时内将每个分区加载到 Azure。  

首先使用单个复制活动，其中包含单个自承载 IR 计算机，以复制表。 根据表中的数据切片分区数逐渐增加 parallelCopies 设置，并查看是否可以根据从复制作业中看到的吞吐量在2小时内将整个表加载到 Azure。 

如果无法实现此功能，并且不能完全利用自承载 IR 节点和数据存储的容量，则会逐渐增加并发复制活动的数量，直到达到网络或数据存储的带宽限制。 

继续监视自承载 IR 计算机上的 CPU/内存使用率，并准备好在看到 CPU/内存已充分利用的情况下纵向扩展计算机或向外扩展到多台计算机。 

如果遇到 Azure 数据工厂复制活动报告的限制错误，请在 Azure 数据工厂中减小 concurrency 或 parallelCopies 设置，或者考虑增加网络和数据存储的带宽/IOPS 限制。 


### <a name="estimating-price"></a>估计价格 

请考虑以下用于将数据从本地 Netezza 服务器迁移到 Azure SQL 数据仓库的管道：

![定价-管道](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

让我们假设以下内容： 

- 总数据量为 50 TB。 
- 使用第一个解决方案体系结构（Netezza 服务器位于防火墙后面的本地）迁移数据
- 50 TB 分为500个分区，每个复制活动移动一个分区。
- 每个复制活动都是通过一个自承载的 IR 针对4台计算机配置的，达到了 20 MBps 的吞吐量。 （在复制活动中，parallelCopies 设置为4，并从表中加载数据的每个线程实现了 5 MBps 的吞吐量）
- ForEach concurrency 设置为3，聚合吞吐量为 60 MBps。
- 总共需要243小时才能完成迁移。

下面是基于上述假设的预计价格： 

![定价-表](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> 这是一个假设的定价示例。 实际定价取决于环境中的实际吞吐量。 不包括 windows 计算机的价格（安装了自承载集成运行时）。 

### <a name="additional-references"></a>其他参考 
- [使用 Azure 数据工厂将数据从本地关系数据仓库迁移到 Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Netezza 连接器](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC 连接器](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob 存储连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL 数据仓库连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [复制活动性能优化指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [创建和配置自承载 Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自承载集成运行时高可用性和可伸缩性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [数据移动安全注意事项](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [以增量方式从一个表复制数据](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [以增量方式从多个表复制数据](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Azure 数据工厂定价页](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)