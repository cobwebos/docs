---
title: Azure SQL 数据库客户实现技术 | Microsoft 文档
description: 了解有关 Azure SQL 数据库的客户实现的技术详细信息以解决业务问题
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 5eba1897a52c00430a2ced0d1679784177b3d561
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Azure SQL 数据库客户实现技术研究

- [Daxko](sql-database-implementation-daxko.md)：Daxko/CSI Software 面临挑战：归功于其全方位企业软件解决方案的成功，健身与休闲中心的客户群体快速发展，但跟上该不断发展的客户群体的 IT 基础结构需求给该公司的 IT 人员带来了考验。 该公司逐渐受到运营开销不断提高的约束，特别是在管理日渐庞大的数据库方面。 更糟的是，这些运营开销已占用新举措（例如公司软件的新移动功能）的部分开发资源。

- [GEP](sql-database-implementation-gep.md)：GEP 提供软件和服务，可让世界各地的采购领导最大限度地影响其业务运营、策略及财务绩效。 除了咨询和托管的服务，该公司还提供 SMART by GEP® 基于云的全方位采购软件平台。 但是，GEP 在尝试使用自己的本地数据中心来支持 SMART by GEP 时遇到一些限制：所需的投资过高，而其他国家/地区的法规要求则令人在必要的投资上有所顾虑。 通过转移到云，GEP 释放了 IT 资源，可以节省 IT 营运的精力，更专注于为全球客户开发新的价值来源。

- [SnelStart](sql-database-implementation-snelstart.md)：SnelStart 为荷兰的中小型企业 (SMB) 编写流行的财务和业务管理软件。 它的 55,000 家客户由 110 名员工提供服务，其中包括 35 名 IT 人员。 通过从桌面软件转移到 Azure 上的服务型软件 (SaaS) 产品，SnelStart 不仅能够使用 C# 中的熟悉环境将管理自动化，而且还能使用弹性池避免过度预配或预配不足，优化性能与伸缩性，从而充分利用内置服务。 Azure 使得 SnelStart 能够在本地环境与云之间灵活移动客户。

- [Umbraco](sql-database-implementation-umbraco.md)：为了简化客户部署，Umbraco 添加了 Umbraco 即服务 (UaaS)：一个软件即服务 (SaaS) 产品，它无需本地部署，提供内置缩放，使开发人员能够专注于产品创新而不是解决方案管理，节省了管理开销。 Umbraco 依赖于 Microsoft Azure 提供的弹性平台即服务 (PaaS) 模型提供所有这些优势。

- [Quorum](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)：Quorum 如何使用 SQL 数据库将关键数据库的工作负荷提高一倍，并将 DTU 降低 70%。

- [Quest](https://customers.microsoft.com/story/quest)：Quest 通过 SQL Server Enterprise 服务提供其 Spotlight，并且心中有一个目标：向数据库专业人员提供可用来保护数据、四处移动该数据以及监视数据库操作的最佳工具。 有了 Spotlight，SQL Server 数据库管理员无论是坐在办公桌前还是在家中工作，他们都可以使用 Microsoft Azure 和 Azure SQL 数据库监视、检测和诊断 SQL Server 中的性能问题并提供方法来解决这些问题。

- [Microsoft Dynamics](https://customers.microsoft.com/story/dynamics365operationsproductteam)：此简短的案例研究重点介绍了在 Dynamics 365 for Operations 产品团队迁移到 Azure SQL 数据库以向客户提供完全托管的软件即服务 (SaaS) 产品/服务的经历中学到的最佳做法和吸取的教训。 使用 Azure SQL 数据库，Dynamics 365 for Operations 团队能够以显著减少的人员来管理和操作该服务，并能够使用现成的可管理性功能（例如自动数据库备份、数据库备份保留、高可用性和灾难恢复功能）轻松进行缩放。 上述功能连同使用细微自动化预配数据库的能力使得 Azure SQL 数据库成为一个可用于打造大型服务的伟大平台。

- [Microsoft OneDrive 和 SharePoint Online](https://customers.microsoft.com/story/microsoft-azure-sql-database-dicrete-manufacturing-united-states)：这是一个关于将 Microsoft OneDrive 和 SharePoint Online 移至 Azure SQL 数据库的简短案例分析，其中介绍此迁移如何实现近乎无限制的弹性容量管理，同时又能大幅降低运营成本和基础结构开支。
