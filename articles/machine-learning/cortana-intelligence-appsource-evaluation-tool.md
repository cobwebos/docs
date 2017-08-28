---
title: "Cortana Intelligence 解决方案评估工具 | Microsoft Docs"
description: "作为 Microsoft 合作伙伴，以下是将 Cortana Intelligence 解决方案发布到 AppSource 需要执行的所有步骤。"
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams;v-bruham;garye
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: e79ca131bddc9e65dd24da109b9e8a08606f7bc6
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

--- 
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Cortana Intelligence 解决方案评估工具
## <a name="overview"></a>概述
可以使用 Cortana Intelligence 解决方案评估工具来评估高级分析解决方案是否符合 Microsoft 推荐的最佳做法。 Microsoft 很高兴能与我们的合作伙伴 (ISV/SI) 协作，为客户、经销商和实现过程提供高质量解决方案。 本指南演示使用解决方案评估工具评估解决方案的过程，并描述检查中的具体最佳做法。

## <a name="getting-started"></a>入门
请[下载](https://aka.ms/aa-evaluation-tool-download)并安装 Cortana Intelligence 解决方案评估工具。

先决条件：
- Windows 10：[Windows 10 官方网站](https://www.microsoft.com/en-us/windows)
- Azure Powershell：[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0)。

## <a name="identifying-your-app"></a>标识应用程序
安装完成后，打开工具并开始首次评估。

![打开评估工具](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

提供有关解决方案的标识信息。

![连接 Azure 订阅](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

连接到你的 Azure 订阅，并提供包含应用程序的资源组。

![选择资源](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

加载资源组后，请选择解决方案中包含的资源，并将任意数据资源的可访问性标识为以下选项之一：
- 引入
- 消耗
- 内部

通过此信息，可以更好地了解解决方案如何利用各种组件，并确保面向用户的组件符合最佳做法。

### <a name="ingestion"></a>引入
这里的引入是指用于从解决方案外部拉取数据的任何数据源，或者用于将数据推送到解决方案的任何服务，这些服务不包括在解决方案内。

### <a name="consumption"></a>消耗
这里的消耗是指用于直接或间接地将数据推送给最终用户的任何数据集。 例如：
- 用于从 PowerBI 进行直接查询的数据集。
- 在 WebApp 中查询的数据集。

>[!NOTE]
如果特定资源可同时用于引入和消耗，请选择消耗。

### <a name="internal"></a>内部
为仅在内部应用程序进程中使用的任何数据资源使用“内部”。

接下来，系统将提示你提供之前步骤中指定的任何数据库的有效凭据：

![设置测试先决条件](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>解决方案测试用例
解决方案工具将针对你的解决方案执行一系列自动测试。

![设置测试执行](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

测试完成后，系统会要求你提供对于解决方案不符合要求的解释或理由。

![提供业务理由](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

例如，如果你的解决方案发布到 Azure SQL DW，那么评估测试还会要求你同时发布到 Azure Analysis Services。 

解决方案可能会使用运行 SQL Server Analysis Services（而不是 Azure Analysis Services）的 IaaS虚拟机。 这会是测试失败的一个可接受原因。
## <a name="packaging-your-evaluation-results"></a>打包评估结果
完成测试用例后，评估包将导出为 zip 文件，系统会要求你提供评估工具的反馈。 

需要先将此测试结果 zip 文件与 Microsoft 共享，然后再申请添加到 AppSource

![为评估工具评分](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

本文上面部分介绍了该工具的各种功能，现在让我们查看此工具将评估的最佳做法类型。

## <a name="security-evaluation-considerations"></a>安全评估注意事项
### <a name="databases-should-use-azure-active-directory-authentication"></a>数据库应使用 Azure Active Directory 身份验证
进行 Azure Active Directory (AAD) 身份验证时，应启用解决方案中的任何 Azure SQL 或 Azure SQL DW 资源。 借助 AAD，在一个位置即可管理所有标识和角色。

| 详细信息 | 参考文章 |
| --- | --- |
| 结合使用 AAD 与 SQL 数据库和 SQL 数据仓库 | [将 Azure Active Directory 身份验证与 SQL 数据库或 SQL 数据仓库结合使用](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) |
| 配置和管理 AAD | [使用 SQL 数据库或 SQL 数据仓库配置和管理 Azure Active Directory 身份验证](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) |
| Azure WebApps 身份验证 | [Azure 应用服务中的身份验证和授权](https://docs.microsoft.com/en-us/azure/app-service/app-service-authentication-overview) |
| 通过 AAD 配置 WebApps | [如何将应用服务应用程序配置为使用 Azure Active Directory 登录](https://docs.microsoft.com/en-us/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>最终用户可访问的数据集应支持基于角色的访问控制
在执行评估工具时，系统会要求你指定任何报告或发布资源。 假定这些资源计划由最终用户（而不是开发者）访问。 这些资源应提供基于角色的访问控制 (RBAC)，以确保最终用户只能访问授权的数据。

具体而言，任何以下 Azure 资源都可以使用 RBAC 进行配置，并可视为可接受：
- Secure HDInsight，请参阅[已加入域的 HDInsight 群集的 Hadoop 安全性简介](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)
- Azure SQL，请参阅[使用 Azure SQL 进行 AAD 身份验证]( https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication)
- Azure Analysis Services，请参阅[管理 Azure Analysis Services 的数据库角色和用户](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users)
- Azure SQL 数据仓库（请注意，由于 SQL DW 不支持 RBAC，因此不建议直接最终用户访问）。

如果你使用的是支持 RBAC 的不同资源类型，请在测试用例理由中指定类型。

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store 应使用静态加密
Azure Data Lake Store (ADLS) 默认使用 ADLS 管理的加密密钥支持静态加密。 你也可以使用 Azure Key Vault 配置加密。

有关指定 ADLS 加密设置的信息，请参阅[创建 Azure Data Lake Store 帐户](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account)。

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>Azure SQL 和 Azure SQL 数据仓库应使用加密
Azure SQL 和 Azure SQL DW 均支持透明数据加密 (TDE)，它提供数据和日志文件的实时加密和解密。

| 详细信息 | 参考文章 |
| --- | --- |
| 透明数据加密 (TDE) | [透明数据加密](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Azure SQL 数据仓库与 TDE | [SQL 数据仓库加密 TDE TSQL](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| 使用 TDE 配置 Azure SQL | [使用 Azure SQL 数据库的透明数据加密](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| 使用 Always Encrypted 配置 Azure SQL | [SQL 数据库 Always Encrypted Azure Key Vault](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

除 TDE 之外，Azure SQL 还支持 Always Encrypted，这是一种新的数据加密技术，在服务器上执行命令时，它可以确保不仅在静态时以及在客户端和服务器之间移动时加密数据，还可在使用数据时进行加密。

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>必须从 Azure Marketplace 部署任何虚拟机
为了在 AppSource 中提供一致的安全级别，请在 Azure Marketplace 中认证和发布任何作为 Cortana Intelligence 解决方案一部分部署的虚拟机。

要搜索 Azure Marketplace 映像的当前列表，请参阅 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute)。

有关如何发布 Azure Marketplace 的虚拟机映像的信息，请参阅[为 Azure Marketplace 创建虚拟机映像的指南](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)。

## <a name="scalability-evaluation-considerations"></a>可伸缩性评估注意事项
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Cortana Intelligence 解决方案应包括可缩放的大数据平台
Cortana Intelligence 解决方案应延伸到超大规模的数据。 在 Azure 中，这意味着它们应该包括两个 PB 缩放数据平台之一：
- Azure Data Lake Store
- Azure SQL 数据仓库

如果解决方案不要求支持这些数据大小，或者如果你正在使用其他数据平台，请在测试用例理由中对此进行解释。
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Cortana Intelligence 解决方案应包括专用的引入数据环境
Cortana Intelligence 解决方案通常应避免将数据直接插入关系数据源。 相反，原始数据应存储在非结构化环境中，并使用 Azure 数据工厂将幂等插入/更新到任意关系存储区。

有关使用 Azure 数据工厂复制数据的详细信息，请参阅[教程：使用 Visual Studio 创建带有复制活动的管道](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-copy-activity-tutorial-using-visual-studio)。

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>Azure SQL 数据仓库应使用 PolyBase 进行数据引入
Azure SQL DW 支持 PolyBase 进行高度可缩放的并行数据引入。 PolyBase 允许你使用 Azure SQL DW 对 Azure Blob 存储或 Azure Data Lake Store 中存储的外部数据集发出查询。 这为批量更新的替代方法提供了卓越性能。

有关 PolyBase 和 Azure SQL DW 的入门说明，请参阅[在 SQL 数据仓库中使用 PolyBase 加载数据](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase)。

有关 PolyBase 和 Azure SQL DW 的最佳做法的详细信息，请参阅[在 SQL 数据仓库中使用 PolyBase 的指南](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide)。

## <a name="availability-evaluation-considerations"></a>可用性评估注意事项

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>最终用户可访问的数据集应支持大量并发用户
在执行评估工具时，系统会要求你指定任何报告或发布资源。 假定这些资源计划由最终用户（而不是开发者）访问。 这些资源应该支持中等数量的并发用户。

具体而言，Azure SQL 数据仓库不应是最终用户可用的唯一数据源。 如果将 Azure SQL DW 作为 Power Users 的资源提供，则 Azure Analysis Services 应该可供典型用户使用。

有关 Azure SQL DW 并发限制的详细信息，请参阅[SQL 数据仓库中的并发和工作负荷管理](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency)。

有关 Azure Analysis Services 的详细信息，请参阅 [Analysis Services 概述](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview)。

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Azure SQL 资源应具有用于故障转移的只读副本
Azure SQL 数据库支持到辅助实例的异地复制。 此实例随后可用作故障转移实例，以提供高可用性应用程序。

有关 Azure SQL 数据库的异地复制的详细信息，请参阅 [SQL 数据库异地复制概述](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview)。

有关如何配置 Azure SQL 异地复制的说明，请参阅[使用 Transact-SQL 为 Azure SQL 数据库配置活动异地复制](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-transact-sql)。

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>Azure SQL 数据仓库应启用异地冗余备份
Azure SQL DW 支持到异地冗余存储的日常备份。 此异地复制功能可确保即使在无法访问主要区域中存储的快照的情况下，你仍可以还原数据仓库。 默认启用此功能，并且 Cortana Intelligence 解决方案不应禁用此功能。

有关 Azure SQL DW 备份和还原的详细信息，请参阅此处[SQL 数据仓库备份](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-backups)。

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>虚拟机应通过可用性集进行配置
Azure 虚拟机应在可用性集中进行配置，从而尽量将已计划和未计划的维护事件的影响降到最小。

有关 Azure 虚拟机可用性的详细信息，请参阅[在 Azure 中管理 Windows 虚拟机的可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability)。

## <a name="other-evaluation-considerations"></a>其他评估注意事项
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Cortana Intelligence 应用程序应使用集中式工具执行数据业务流程
使用单一工具管理和计划数据移动和转换，确保任务关键数据的一致性。 它为重试逻辑、依赖项管理、警报/日志记录等提供了清晰的逻辑。我们建议在 Azure 中使用 [Azure 数据工厂](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-introduction)执行数据业务流程。

如果你使用除 Azure 数据工厂以外的工具执行数据业务流程，请说明你所使用的工具。
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>使用 Azure 数据工厂重新训练 Azure 机器学习模型
Azure机器学习 (AzureML) 提供了易于使用的工具，用于创建和部署预测性建模和机器学习管道。 然而，重要的是这些 AzureML 模型的生产部署不是基于单个固定数据集，而是适应真实世界的动态变化。

有关在 Azure ML 中创建重新训练 Web 服务的详细信息，请参阅[以编程方式重新训练机器学习模型](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-retrain-models-programmatically)。

有关使用 Azure 数据工厂自动执行模型训练过程的详细信息，请参阅[使用更新资源活动更新 Azure 机器学习模型](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-azure-ml-update-resource-activity)。

## <a name="existing-documentation"></a>现有文档
[通过 Microsoft Azure 认证发展云业务](https://azure.microsoft.com/en-us/marketplace/programs/certified/)

[Microsoft Azure Cortana Intelligence 认证](https://azure.microsoft.com/en-us/marketplace/programs/certified/cortana/)


