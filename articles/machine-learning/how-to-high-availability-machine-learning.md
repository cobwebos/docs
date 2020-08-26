---
title: 如何提高复原能力
titleSuffix: Azure Machine Learning
description: 了解如何使用高可用性配置使 Azure 机器学习资源更具复原能力。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 9b298e10d3eb95bcb0ef525eb973259a3ab1dbbb
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852547"
---
# <a name="increase-azure-machine-learning-resiliency"></a>增加 Azure 机器学习复原

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，你将了解如何使用高可用性配置使 Microsoft Azure 机器学习资源更具弹性。 你可以配置 Azure 机器学习依赖的 Azure 服务以实现高可用性。 本文列出了可配置为实现高可用性的服务，并提供了有关配置这些资源的其他信息的链接。

> [!NOTE]
> Azure 机器学习本身不提供灾难恢复选项。

## <a name="understand-azure-services-for-azure-machine-learning"></a>了解适用于 Azure 机器学习的 Azure 服务

Azure 机器学习依赖于多个 Azure 服务，并具有多个层。 其中一些服务是在 (客户) 订阅中预配的。 你需要负责这些服务的高可用性配置。 其他服务是在 Microsoft 订阅中创建的，由 Microsoft 管理。 

Azure 服务包括：

* **Azure 机器学习基础结构**： Azure 机器学习工作区的 Microsoft 托管的环境。

* **关联资源**：在 Azure 机器学习工作区创建期间在订阅中预配的资源。 这些资源包括 Azure 存储、Azure Key Vault、Azure 容器注册表和 Application Insights。 你负责为这些资源配置高可用性设置。
  * 默认存储具有模型、训练日志数据和数据集等数据。
  * Key Vault 具有 Azure 存储、容器注册表和数据存储的凭据。
  * 容器注册表具有用于定型和推断环境的 Docker 映像。
  * Application Insights 用于监视 Azure 机器学习。

* **计算资源**：在工作区部署后创建的资源。 例如，可以创建计算实例或计算群集来训练机器学习模型。
  * 计算实例和计算群集： Microsoft 托管模型开发环境。
  * 其他资源：可以附加到 Azure 机器学习的 Microsoft 计算资源，如 Azure Kubernetes Service (AKS) 、Azure Databricks、Azure 容器实例和 Azure HDInsight。 你负责为这些资源配置高可用性设置。

* **其他数据存储**： Azure 机器学习可以装载附加的数据存储（例如 azure 存储、Azure Data Lake Storage 和 Azure SQL 数据库）用于定型数据。  这些数据存储在你的订阅中预配。 你负责配置其高可用性设置。

下表显示了由 Microsoft 管理的 Azure 服务，它们由你管理，并且默认情况下具有高可用性。

| 服务 | 管理者 | 默认情况下的高可用性 |
| ----- | ----- | ----- |
| **Azure 机器学习基础结构** | Microsoft | |
| **关联的资源** |
| Azure 存储 | 你 | |
| 密钥保管库 | 你 | ✓ |
| 容器注册表 | 你 | |
| Application Insights | 你 | NA |
| **计算资源** |
| 计算实例 | Microsoft |  |
| 计算群集 | Microsoft |  |
| 其他计算资源，如 AKS、 <br>Azure Databricks，容器实例，HDInsight | 你 |  |
| **其他数据存储** ，如 Azure 存储、SQL 数据库、<br> Azure Database for PostgreSQL、Azure Database for MySQL、 <br>Azure Databricks 文件系统 | 你 | |

本文的其余部分介绍了使其中每个服务高度可用所需执行的操作。

## <a name="associated-resources"></a>关联的资源

> [!IMPORTANT]
> Azure 机器学习不支持使用异地冗余存储的默认存储帐户故障转移 (GRS) ，地域冗余存储 (GZRS) ，读取访问异地冗余存储 (GRS) 或读取访问地域冗余存储 (RA-GZRS) 。

请参阅以下文档，确保配置每个资源的高可用性设置：

* **Azure 存储**：若要配置高可用性设置，请参阅 [Azure 存储冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。
* **Key Vault**：默认情况下 Key Vault 提供高可用性，并且无需用户操作。  请参阅 [Azure 密钥保管库可用性和冗余](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)。
* **容器注册表**：为异地复制选择高级注册表选项。 请参阅 [Azure 容器注册表中的异地复制](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。
* **Application Insights**： Application Insights 不提供高可用性设置。 若要调整数据保持期和详细信息，请参阅 [Application Insights 中的数据收集、保留和存储](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept)。

## <a name="compute-resources"></a>计算资源

请参阅以下文档，确保配置每个资源的高可用性设置：

* **Azure Kubernetes 服务**：请参阅 [Azure Kubernetes 服务 (AKS) 中实现业务连续性和灾难恢复的最佳做法](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)和[创建使用可用性区域的 Azure Kubernetes 服务 (AKS) 群集](https://docs.microsoft.com/azure/aks/availability-zones)。 如果 AKS 群集是使用 Azure 机器学习 Studio、SDK 或 CLI 创建的，则不支持跨区域高可用性。
* **Azure Databricks**：请参阅 [Azure Databricks 群集的区域性灾难恢复](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)。
* **容器实例**：业务流程协调程序负责故障转移。 请参阅 [Azure 容器实例和容器业务流程协调程序](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship)。
* **HDInsight**：请参阅 [Azure HDInsight 支持的高可用性服务](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components)。

## <a name="additional-data-stores"></a>其他数据存储

请参阅以下文档，确保配置每个资源的高可用性设置：

* **Azure Blob 容器/Azure 文件/Data Lake Storage Gen2**：与默认存储相同。
* **Data Lake Storage Gen1**：请参阅 [Data Lake Storage Gen1 的高可用性和灾难恢复指南](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)。
* **Sql 数据库**：请参阅 [Azure SQL 数据库和 SQL 托管实例的高可用性](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability)。
* **Azure Database for PostgreSQL**：请参阅 [Azure Database for PostgreSQL - 单一服务器中的高可用性概念](https://docs.microsoft.com/azure/postgresql/concepts-high-availability)。
* **Azure Database for MySQL**：请参阅[了解 Azure Database for MySQL 中的业务连续性](https://docs.microsoft.com/azure/mysql/concepts-business-continuity)。
* **Azure Databricks 文件系统**：请参阅 [Azure Databricks 群集的区域灾难恢复](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

如果你提供自己的客户托管密钥来部署 Azure 机器学习工作区，则还会在你的订阅中预配 Azure Cosmos DB。 在这种情况下，你需要负责配置其高可用性设置。 请参阅 [Azure Cosmos DB 的高可用性](https://docs.microsoft.com/azure/cosmos-db/high-availability)。

## <a name="next-steps"></a>后续步骤

若要使用关联资源和高可用性设置部署 Azure 机器学习，请使用 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)。
