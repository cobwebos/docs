---
title: 数据访问策略
description: Azure 数据工厂现在支持静态 IP 地址范围。
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 0b966b10c5bbc7bb90a4226d94dda8b75e25c3af
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247472"
---
# <a name="data-access-strategies"></a>数据访问策略

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

组织的一个重要安全目标是保护其数据存储（可以是本地或云/SaaS 数据存储），使其免于通过 Internet 进行的随机访问。 

通常，云数据存储使用以下机制来控制访问：
* 按 IP 地址限制连接的防火墙规则
* 要求用户证明其身份的身份验证机制
* 将用户限制于特定操作和数据的授权机制

> [!TIP]
> 通过[引入静态 IP 地址范围](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)，现在可以允许列出特定 Azure Integration Runtime 区域的 IP 范围，以确保不必在云数据存储中允许所有 Azure IP 地址。 这样，就可以限制允许访问数据存储的 IP 地址。

> [!NOTE] 
> 阻止该 IP 地址范围访问 Azure Integration Runtime，当前仅用于数据移动、管道和外部活动。 数据流现在不使用这些 IP 范围。 

这应该适用于许多场景，我们知道为每个集成运行时配置唯一的静态 IP 地址会很理想，但在目前使用无服务器 Azure Integration Runtime 的情况下，这是不可能的。 如有必要，你始终可以设置自承载集成运行时并对其使用静态 IP。 

## <a name="data-access-strategies-through-azure-data-factory"></a>通过 Azure 数据工厂的数据访问策略

* **[受信任的服务](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** - Azure 存储（Blob、ADLS Gen2）支持允许精选受信任 Azure 平台服务安全访问存储帐户的防火墙配置。 受信任的服务强制实施托管标识身份验证，这可以确保其他数据工厂不能连接到此存储，除非使用其托管标识将其列入了执行此操作的白名单。 可在[此博客](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)中找到更多详细信息。 因此，这这种方法非常安全，建议使用。 
* **唯一静态 IP** - 需要设置一个自承载集成运行时，以便获取静态 IP 以建立数据工厂连接器。 此机制可确保阻止来自其他所有 IP 地址的访问。 
* **[静态 IP 范围](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** - 可以使用 Azure Integration Runtime 的 IP 地址，允许在你的存储（如 S3、Salesforce 等）中列出它。 它肯定会限制可连接到数据存储但又依赖于身份验证/授权规则的 IP 地址。
* **[服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** - 服务标记是来自给定 Azure 服务（例如 Azure 数据工厂）的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而尽量简化网络安全规则的频繁更新。 要将虚拟网络中 IaaS 托管数据存储上的数据访问列入允许列表时，此方法非常有用。
* **允许 Azure 服务** - 使用某些服务时，如果选择此选项，可以允许所有 Azure 服务连接到它。 

有关 Azure Integration Runtime 和自承载集成运行时中数据存储上支持的网络安全机制的详细信息，请参阅下面两个表。  
* **Azure Integration Runtime**

    | 数据存储                  | 数据存储上支持的网络安全机制         | 受信任的服务     | 静态 IP 范围 | 服务标记 | 允许 Azure 服务 |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|
    | Azure PaaS 数据存储       | Azure Cosmos DB                                             | -                   | 是             | -            | 是                  |
    |                              | Azure 数据资源管理器                                         | -                   | 是*            | 是*         | -                    |
    |                              | Azure Data Lake Gen1                                        | -                   | 是             | -            | 是                  |
    |                              | Azure Database for MariaDB、Azure Database for MySQL、Azure Database for PostgreSQL               | -                   | 是             | -            | 是                  |
    |                              | Azure 文件存储                                          | -                   | 是             | -            | 。                    |
    |                              | Azure 存储（Blob、ADLS Gen2）                             | 是（仅 MSI 身份验证） | 是             | -            | 。                    |
    |                              | Azure SQL DB、SQL DW (Synapse Analytics)、SQL Ml          | -                   | 是             | -            | 是                  |
    |                              | Azure Key Vault（用于提取机密/连接字符串） | 是                 | 是             | -            | -                    |
    | 其他 PaaS/SaaS 数据存储 | AWS S3、SalesForce、Google Cloud Storage 等。            | -                   | 是             | -            | -                    |
    | Azure laaS                   | SQL Server、Oracle 等。                                  | -                   | 是             | 是          | -                    |
    | 本地 laaS              | SQL Server、Oracle 等。                                  | -                   | 是             | -            | -                    |
    
    *仅当 Azure 数据资源管理器插入虚拟网络，且 IP 范围可应用于 NSG/防火墙时适用。 

* （Vnet 中/本地的）自承载集成运行时
    
    | 数据存储                  | 数据存储上支持的网络安全机制         | 静态 IP | 受信任的服务  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Azure PaaS 数据存储       | Azure Cosmos DB                                               | 是       | -                   |
    |                                | Azure 数据资源管理器                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | 是       | -                   |
    |                                | Azure Database for MariaDB、Azure Database for MySQL、Azure Database for PostgreSQL               | 是       | -                   |
    |                                | Azure 文件存储                                            | 是       | -                   |
    |                                | Azure 存储（Blob、ADLS Gen2）                             | 是       | 是（仅 MSI 身份验证） |
    |                                | Azure SQL DB、SQL DW (Synapse Analytics)、SQL Ml          | 是       | -                   |
    |                                | Azure Key Vault（用于提取机密/连接字符串） | 是       | 是                 |
    | 其他 PaaS/SaaS 数据存储 | AWS S3、SalesForce、Google Cloud Storage 等。              | 是       | -                   |
    | Azure laaS                     | SQL Server、Oracle 等。                                  | 是       | -                   |
    | 本地 laaS              | SQL Server、Oracle 等。                                  | 是       | -                   |    

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下相关文章：
* [支持的数据存储](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* [Azure Key Vault“受信任的服务”](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* [Azure 存储“受信任的 Microsoft 服务”](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [数据工厂的托管标识](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
