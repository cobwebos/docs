---
title: 使用 Azure 数据工厂将数据从本地 Netezza 服务器迁移到 Azure
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
ms.openlocfilehash: 762e7d4a7356c11171355b5325e77569e9acb1e2
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555100"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>使用 Azure 数据工厂将数据从本地 Netezza 服务器迁移到 Azure 

Azure 数据工厂提供一种功能强大、稳健且经济高效的机制，用于从本地 Netezza 服务器大规模将数据迁移到 Azure 存储帐户或 Azure SQL 数据仓库数据库。 

本文提供了以下数据工程师和开发人员信息：

> [!div class="checklist"]
> * 性能 
> * 复制复原能力
> * 网络安全
> * 高级解决方案体系结构 
> * 实施最佳实践  

## <a name="performance"></a>性能

Azure 数据工厂提供了一个无服务器体系结构，允许在不同级别实现并行。 如果你是一名开发人员，这意味着你可以构建管道来完全使用网络和数据库带宽，以最大程度地提高环境的数据移动吞吐量。

![性能图表](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

前面的关系图可以解释如下：

- 单个复制活动可以利用可缩放的计算资源。 使用 Azure Integration Runtime 时，可以以无服务器方式为每个复制活动[最多指定256个 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) 。 使用自承载集成运行时（自承载 IR），可以手动向上扩展计算机或向外扩展到多台计算机（最多[四个节点](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)），单个复制活动会在所有节点之间分发其分区。 

- 单个复制活动通过使用多个线程从数据存储区读取数据并将数据写入数据存储区。 

- Azure 数据工厂控制流可以并行启动多个复制活动。 例如，它可以通过[对每个循环使用来](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)启动它们。 

有关详细信息，请参阅[复制活动性能和可伸缩性指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)。

## <a name="resilience"></a>恢复能力

在单个复制活动运行中，Azure 数据工厂具有内置的重试机制，使其能够处理数据存储或基础网络中特定程度的暂时性故障。

使用 Azure 数据工厂复制活动时，在源数据存储和接收器数据存储之间复制数据时，可以通过两种方式处理不兼容的行。 可以通过跳过不兼容的数据行来中止和失败复制活动，或继续复制其余数据。 此外，若要了解失败的原因，可以在 Azure Blob 存储或 Azure Data Lake Store 中记录不兼容的行，修复数据源上的数据，并重试复制活动。

## <a name="network-security"></a>网络安全 

默认情况下，通过使用超文本传输协议（HTTPS）上的加密连接，Azure 数据工厂将数据从本地 Netezza 服务器传输到 Azure 存储帐户或 Azure SQL 数据仓库数据库。 HTTPS 在传输过程中提供数据加密，并防止窃听和中间人攻击。

或者，如果不希望通过公共 internet 传输数据，则可以通过 Azure Express Route 通过专用对等互连链接传输数据，从而提高安全性。 

下一节将讨论如何实现更高的安全性。

## <a name="solution-architecture"></a>解决方案体系结构

本部分介绍迁移数据的两种方法。

### <a name="migrate-data-over-the-public-internet"></a>通过公共 internet 迁移数据

![通过公共 internet 迁移数据](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

前面的关系图可以解释如下：

- 在此体系结构中，你通过公共 internet 使用 HTTPS 安全地传输数据。

- 若要实现此体系结构，需要在企业防火墙后面的 Windows 计算机上安装 Azure 数据工厂集成运行时（自承载）。 请确保此集成运行时可以直接访问 Netezza 服务器。 若要充分利用网络和数据存储带宽来复制数据，可以手动增加计算机规模，或向外扩展到多台计算机。

- 使用此体系结构，可以迁移初始快照数据和增量数据。

### <a name="migrate-data-over-a-private-network"></a>通过专用网络迁移数据 

![通过专用网络迁移数据](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

前面的关系图可以解释如下：

- 在此体系结构中，你通过 Azure Express Route 在专用对等互连链接上迁移数据，而数据从不通过公共 internet 进行遍历。 

- 若要实现此体系结构，需要在 Azure 虚拟网络中的 Windows 虚拟机（VM）上安装 Azure 数据工厂集成运行时（自承载）。 若要充分利用网络和数据存储带宽来复制数据，可以手动增加 VM 或向外扩展到多个 Vm。

- 使用此体系结构，可以迁移初始快照数据和增量数据。

## <a name="implement-best-practices"></a>实施最佳实践 

### <a name="manage-authentication-and-credentials"></a>管理身份验证和凭据 

- 若要对 Netezza 进行身份验证，可以[通过连接字符串使用 ODBC 身份验证](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)。 

- 若要对 Azure Blob 存储进行身份验证： 

   - 强烈建议使用[Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)。 托管标识在 Azure Active Directory （Azure AD）中自动管理的 Azure 数据工厂标识基础上构建，使你能够配置管道，而不必在链接的服务定义中提供凭据。  

   - 或者，你可以通过使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共享访问签名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)或[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)，对 Azure Blob 存储进行身份验证。 

- 若要对 Azure Data Lake Storage Gen2 进行身份验证： 

   - 强烈建议使用[Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)。
   
   - 你还可以使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[存储帐户密钥](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 

- 向 Azure SQL 数据仓库进行身份验证：

   - 强烈建议使用[Azure 资源的托管标识](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)。
   
   - 你还可以使用[服务主体](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication)或[SQL 身份验证](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)。

- 如果不使用 Azure 资源的托管标识，强烈建议将[凭据存储在 Azure Key Vault 中](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)，以便更轻松地集中管理和旋转密钥，而无需修改 Azure 数据工厂链接服务。 这也是[CI/CD 的最佳实践](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)之一。 

### <a name="migrate-initial-snapshot-data"></a>迁移初始快照数据 

对于小型表（即，容量小于 100 GB 或可在两小时内迁移到 Azure 的表），可以使每个复制作业在每个表中加载数据。 为了获得更大的吞吐量，可以运行多个 Azure 数据工厂复制作业来同时加载单独的表。 

在每个复制作业中，若要运行并行查询并按分区复制数据，还可以通过使用 " [`parallelCopies`" 属性设置](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy)和以下任一数据分区选项来达到一定程度的并行度：

- 为了获得更高的效率，我们鼓励你从数据切片开始。  请确保 "`parallelCopies`" 设置中的值小于 Netezza 服务器上的表中的数据切片分区总数。  

- 如果每个数据切片分区的量仍然很大（例如，10 GB 或更大），我们鼓励您切换到动态范围分区。 使用此选项可以更灵活地定义分区数以及每个分区的分区列、上下限和下限。

对于较大的表（即，容量为 100 GB 或更大的表，或者在两个小时内*无法*迁移到 Azure 的表），我们建议你按自定义查询对数据进行分区，然后将每个复制作业一次复制一个分区。 为了获得更好的吞吐量，可以同时运行多个 Azure 数据工厂复制作业。 对于通过自定义查询加载一个分区的每个复制作业目标，可以通过数据切片或动态范围启用并行性来提高吞吐量。 

如果由于网络或数据存储暂时性问题而导致任何复制作业失败，则可以重新运行失败的复制作业，以从表中重新加载该特定分区。 其他加载其他分区的复制作业不会受到影响。

当你将数据加载到 Azure SQL 数据仓库数据库时，我们建议你使用 Azure Blob 存储作为暂存在复制作业中启用 PolyBase。

### <a name="migrate-delta-data"></a>迁移增量数据 

若要从表中标识新行或更新的行，请在该架构中使用时间戳列或递增键。 然后，你可以将最新值存储为外部表中的高水印，然后使用它在下次加载数据时筛选增量数据。 

每个表可以使用不同的水印列来标识其新行或更新的行。 建议您创建一个外部控制表。 在表中，每一行代表 Netezza 服务器上的一个表，其特定的水印列名称和高水印值。 

### <a name="configure-a-self-hosted-integration-runtime"></a>配置自承载集成运行时

如果要将数据从 Netezza 服务器迁移到 Azure，无论服务器是在企业防火墙后还是虚拟网络环境中，都需要在 Windows 计算机或 VM 上安装自承载 IR，这是用于移动数据。 安装自承载 IR 时，建议采用以下方法：

- 对于每个 Windows 计算机或 VM，请从 32 vCPU 和 128 GB 内存的配置开始。 您可以在数据迁移过程中持续监视 IR 计算机的 CPU 和内存使用情况，以确定是否需要进一步纵向扩展计算机以获得更好的性能，或缩小计算机以节省成本。

- 还可以通过将最多四个节点与一个自承载 IR 关联来向外扩展。 对自承载 IR 运行的单个复制作业会自动应用所有 VM 节点以并行复制数据。 为实现高可用性，请从四个 VM 节点开始，以避免在数据迁移过程中出现单一故障点。

### <a name="limit-your-partitions"></a>限制分区

最佳做法是，使用有代表性的示例数据集进行性能概念证明（POC），以便您可以确定每个复制活动的适当分区大小。 建议在两个小时内将每个分区加载到 Azure。  

若要复制表，请首先使用单个自承载 IR 计算机的单个复制活动。 根据表中的数据切片分区数逐渐增加 `parallelCopies` 设置。 根据复制作业生成的吞吐量，查看是否可以在两小时内将整个表加载到 Azure。 

如果在两个小时内无法将其加载到 Azure 中，并且未完全使用自承载 IR 节点和数据存储的容量，则会逐渐增加并发复制活动的数量，直到达到网络的限制或数据存储的带宽限制些. 

继续监视自承载 IR 计算机上的 CPU 和内存使用情况，并在看到 CPU 和内存完全使用时，准备好向上扩展计算机或向外扩展到多台计算机。 

如果遇到由 Azure 数据工厂复制活动报告的限制错误，请在 Azure 数据工厂中减少并发或 `parallelCopies` 设置，或考虑增加网络和数据存储的带宽或每秒 i/o 操作数（IOPS）限制。 


### <a name="estimate-your-pricing"></a>估计定价 

请考虑使用以下管道，它构造用于将数据从本地 Netezza 服务器迁移到 Azure SQL 数据仓库数据库：

![定价管道](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

假设以下语句为 true： 

- 总数据量为 50 tb。 

- 我们要使用第一个解决方案体系结构（Netezza 服务器在本地、防火墙后面）迁移数据。

- 50 TB 的卷划分为500个分区，每个复制活动会移动一个分区。

- 每个复制活动都配置有一个自承载的 IR，并针对四台计算机，实现的吞吐量为每秒 20 mb （MBps）。 （在复制活动中，`parallelCopies` 设置为4，每个要从表中加载数据的线程实现 5 MBps 的吞吐量。）

- ForEach concurrency 设置为3，聚合吞吐量为 60 MBps。

- 总共需要243小时才能完成迁移。

根据上述假设，这是预估价格： 

![定价表](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> 上表中显示的价格是假设的。 实际定价取决于环境中的实际吞吐量。 未包含 Windows 计算机（安装了自承载 IR）的价格。 

### <a name="additional-references"></a>其他参考

有关详细信息，请参阅以下文章和指南：

- [使用 Azure 数据工厂将数据从本地关系数据仓库数据库迁移到 Azure](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza 连接器](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC 连接器](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob 存储连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL 数据仓库连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [复制活动性能优化指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [创建和配置自承载集成运行时](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自承载集成运行时高可用性和可伸缩性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [数据移动安全注意事项](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [在 Azure Key Vault 中存储凭据](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [以增量方式从一个表复制数据](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [以增量方式从多个表复制数据](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Azure 数据工厂定价页](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂从多个容器复制文件](solution-template-copy-files-multiple-containers.md)
