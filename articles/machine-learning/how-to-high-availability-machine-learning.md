---
title: 如何提高复原能力
titleSuffix: Azure Machine Learning
description: 了解如何使用高可用性配置使 Azure 机器学习相关资源对中断性更强。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096460"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>提高 Azure 机器学习的复原能力

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何使用高可用性配置使 Azure 机器学习相关资源更具弹性。 Azure 机器学习依赖的 Azure 服务可以配置为实现高可用性。 本文提供了可为实现高可用性而配置哪些服务的相关信息，以及指向有关配置这些资源的信息的链接。

> [!NOTE]
> Azure 机器学习本身不提供灾难恢复选项。

## <a name="understand-azure-services-for-azure-machine-learning"></a>了解适用于 Azure 机器学习的 Azure 服务

Azure 机器学习依赖于多个 Azure 服务，具有多个层。 其中一些部分在你的（客户）订阅中预配。 你需要负责这些服务的高可用性配置。 有些是在 Microsoft 订阅中创建的，由 Microsoft 管理。

* **Azure 机器学习基础结构**： Azure 机器学习工作区的 Microsoft 管理的环境。

* **关联资源**：在 Azure 机器学习工作区创建期间在订阅中预配的资源。 其中包括 Azure 存储、Azure Key Vault、Azure 容器注册表（ACR）和 App Insights。 你需要负责这些资源的高可用性设置。
  * 默认存储包含模型、定型日志数据和数据集等数据。
  * Key Vault 具有存储、ACR、数据存储的凭据。
  * ACR 具有用于定型和推断环境的 docker 映像。
  * App Insights 用于监视 Azure 机器学习。

* **计算资源**：在工作区部署后创建的资源。 例如，可以创建计算实例或计算群集来训练机器学习模型。
  * 计算实例和计算群集： Microsoft 托管模型开发环境。
  * 其他资源：可以将计算资源附加到 Azure 机器学习例如 Azure Kubernetes Service （AKS）、Azure Databricks、Azure 容器实例（ACI）和 HDInsight。 你负责高可用性设置。

* **其他数据存储**： Azure 机器学习可以装载附加的数据存储（例如 azure 存储、Azure Data Lake Storage 和 Azure SQL 数据库）用于定型数据。  它们位于你的订阅中，你负责实现高可用性设置。

下表显示由 Microsoft 管理的服务，这些服务由你管理，并且默认情况下具有高可用性：

| 服务 | 管理者 | 默认情况下为 HA |
| ----- | ----- | ----- |
| **Azure 机器学习基础结构** | Microsoft | |
| **关联的资源** |
| Azure 存储 | 你 | |
| Azure Key Vault | 你 | ✓ |
| Azure 容器注册表 | 你 | |
| Application Insights | 你 | NA |
| **计算资源** |
| 计算实例 | Microsoft |  |
| 计算群集 | Microsoft |  |
| 其他资源，例如 Azure Kubernetes 服务、 <br>Azure Databricks，Azure 容器实例，Azure HDInsight | 你 |  |
| **其他数据存储**，如 azure 存储、Azure SQL 数据库、<br> Azure Database for PostgreSQL、Azure Database for MySQL、 <br>Azure Databricks 文件系统 | 你 | |

使用本文档的其余部分中的信息来了解使其中每个服务高度可用所需执行的操作。

## <a name="associated-resources"></a>关联的资源

> [!IMPORTANT]
> Azure 机器学习不支持使用异地冗余存储（GRS）或地域冗余存储（GZRS）或读取访问异地冗余存储（）或读取访问异地冗余存储（RA-GZRS）的默认存储帐户故障转移。

请确保具有以下信息的每个资源的高可用性设置。

* **Azure 存储**：若要配置高可用性设置，请参阅[Azure 存储冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。
* **Azure Key Vault**：它提供默认的高可用性服务，不需要用户执行任何操作。  请参阅[Azure Key Vault 可用性和冗余](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)。
* **Azure 容器注册表**：为异地复制选择高级 SKU。 请参阅[Azure 容器注册表中的异地复制](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。
* **Application Insights**：不提供高可用性设置。 可以在[Application Insights 中调整数据收集、保留和存储](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept)中的数据保持期和详细信息。

## <a name="compute-resources"></a>计算资源

请确保具有以下文档的每个资源的高可用性设置。

* **Azure Kubernetes 服务**：[有关 azure KUBERNETES Service （AKS）中的业务连续性和灾难恢复的最佳实践](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)，请参阅[创建使用可用性区域的 azure Kubernetes Service （AKS）群集](https://docs.microsoft.com/azure/aks/availability-zones)。 如果 AKS 群集是通过 Azure 机器学习（使用 studio、SDK 或 ML CLI）创建的，则不支持跨区域高可用性。
* **Azure Databricks**：请参阅[Azure Databricks 群集的区域灾难恢复](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)。
* **Azure 容器实例**： ACI orchestrator 负责故障转移。 请参阅[Azure 容器实例和容器协调器](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship)。
* **Azure hdinsight**：请参阅[azure Hdinsight 支持的高可用性服务](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components)。

## <a name="additional-data-stores"></a>其他数据存储

请确保具有以下文档的每个资源的高可用性设置。

* **Azure Blob 容器/Azure 文件共享/Azure Data Lake Gen2**：与默认存储相同。
* **Azure Data Lake Gen1**：[有关 Azure Data Lake Storage Gen1 中的数据，请参阅灾难恢复指南](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)。
* **AZURE Sql 数据库**：请参阅[高可用性和 Azure sql 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability)。
* **Azure Database for PostgreSQL**：请参阅[Azure Database for PostgreSQL-单一服务器中的高可用性概念](https://docs.microsoft.com/azure/postgresql/concepts-high-availability)。
* **Azure Database for MySQL**：请参阅[了解 Azure Database for MySQL 中的业务连续性](https://docs.microsoft.com/azure/mysql/concepts-business-continuity)。
* **Databricks 文件系统**：请参阅[区域灾难恢复 Azure Databricks 群集](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

如果你提供自己的密钥（客户管理的密钥）来部署 Azure 机器学习工作区，则还会在你的订阅中预配 Cosmos DB。 在这种情况下，你负责实现高可用性。 请参阅[Azure Cosmos DB 的高可用性](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>后续步骤

若要使用关联资源和高可用性设置部署 Azure 机器学习，请使用[Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)。