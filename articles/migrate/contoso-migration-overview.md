---
title: 到 Azure 的 Contoso 迁移概述 | Microsoft Docs
description: 提供 Contoso 用于将其本地数据中心迁移到 Azure 的迁移策略和方案的概述。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 1f5eb085239ac985aa3355b1e91dac9639827339
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54155064"
---
# <a name="contoso-migration-overview"></a>Contoso 迁移：概述


本文演示虚拟组织 Contoso 如何将本地基础结构迁移到 [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) 云。 

本文档是系列文章中的第一篇，目的是展示虚拟公司 Contoso 如何迁移到 Azure。 该系列包括相关信息和方案来说明如何设置基础结构的迁移及运行不同类型的迁移。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。 文章展示 Contoso 公司如何完成其迁移任务，但始终提供一般阅读和特定说明的指针。

## <a name="introduction"></a>介绍

Azure 提供了针对一套全面的云服务的访问权限。 开发者和 IT 专业人员可以使用这些服务借助一系列工具和框架在一个全球性的数据中心网络内构建、部署和管理应用程序。 当你的业务面临与数字化变迁相关的挑战时，Azure 云可以帮助你规划如何优化资源和运营，与客户和员工进行沟通，以及对产品进行变革。

Azure 意识到，尽管云提供了诸多优点，包括速度和灵活性、最低的成本、高性能和可靠性，但是许多组织在将来的一段时间内仍然需要运行本地数据中心。 为了破除云采用障碍，Azure 提供了混合云策略，该策略在本地数据中心与 Azure 公有云之间架起桥梁。 例如，使用 Azure 云资源（例如 Azure 备份）来保护本地资源，或者使用 Azure 分析来洞察本地工作负荷。 

作为混合云策略的一部分，Azure 将针对将本地应用和工作负荷迁移到云提供不断增长的解决方案。 你可以使用简单的步骤全面评估你的本地资源，进而规划它们将如何在 Azure 云中运行。 然后，根据完成的深度评估，你可以自信地将资源迁移到 Azure。 当资源已就位并在 Azure 中运行时，可以对它们进行优化来保持和提高可访问性、灵活性、安全性和可靠性。

## <a name="migration-strategies"></a>迁移策略

用于迁移到云的策略分为以下四大类：重新托管、重构、重新架构或重新生成。 所采用的策略取决于业务驱动因素和迁移目标。 可采用多项策略。 例如，可选择重新托管（直接迁移）简单应用或对业务不重要的应用，但重新架构那些更复杂且对业务重要的应用。 让我们来看一下这些策略。


**策略** | **定义** | **使用时机** 
--- | --- | --- 
**重新托管** | 通常被称为“直接迁移”迁移。 此选项无需更改代码，允许将现有应用快速迁移到 Azure。 每个应用都按原样迁移，以便从云中获益，而不会产生与更改代码相关的风险和费用。 | 当需要将应用快速移动到云中时。<br/><br/> 当想要在不修改应用的情况下移动该应用时。<br/><br/> 当重新架构应用，以便它们可以在迁移后利用 [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) 可伸缩性时。<br/><br/> 当应用对业务非常重要，但无需立即更改应用功能时。
**重构** | 通常被称为“重新打包”，重构需要对应用进行最小程度的更改，以便它们可以连接到 [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/)，并使用云产品/服务。<br/><br/> 例如，可以将现有应用迁移到 Azure 应用服务或 Azure Kubernetes 服务 (AKS)。<br/><br/> 或者，将关系数据库和非关系数据库重构为 Azure SQL 数据库托管实例、Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure Cosmos DB 等选项。 | 如果可轻松重新打包应用，使其在 Azure 中工作。<br/><br/> 如果想要应用 Azure 提供的创新性 DevOps 实践，或正在考虑将容器策略用于工作负荷的 DevOps。<br/><br/> 对于重构，需要考虑现有基本代码的可移植性，以及可用的开发技能。
**重新架构** | 重新架构以便迁移的重点在于修改和扩展应用功能及基本代码，以优化云可伸缩性的应用体系结构。<br/><br/> 例如，可将整体应用分解为一组一起工作且缩放轻松的微服务。<br/><br/> 或者，将关系数据库和非关系数据库重新构建为完全托管的 DBaaS 解决方案，如 Azure SQL 数据库托管实例、Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure Cosmos DB。 | 当应用需要进行重大修订，以便纳入新功能或在云平台上有效工作时。<br/><br/> 当想要使用现有应用程序投资、满足可伸缩性需求、应用创新性 Azure DevOps 实践和最小化虚拟机的使用时。
**重新生成** | 重新生成通过使用 Azure 云技术从头开始重新生成应用来使操作更进一步。<br/><br/> 例如，可使用[原生于云](http://azure.com/cloudnative)的技术（如 Azure Functions、Azure AI、Azure SQL 数据库托管实例、Azure Cosmos DB 等）生成尚无人开发的应用。 | 当希望快速开发且现有应用的功能和生存期有限时。<br/><br/> 当准备好加快业务创新（包括 Azure 提供的 DevOps 实践）、使用原生于云的技术生成新应用程序，以及充分利用 AI、区块链和 IoT 中的改进时。

## <a name="migration-articles"></a>迁移文章

下表总结了此系列中的文章。  

- 每个迁移方案均由决定迁移策略的略有不同的业务目标所驱动。
- 对于每个部署方案，我们提供有关业务驱动因素和目标、建议的体系结构、执行迁移的步骤、迁移完成后的清理建议和下一步骤的信息。

**文章** | **详细信息** | **Status**
--- | --- | ---
文章 1：概述 | 概述文章系列、Contoso 的迁移策略和该系列所用的示例应用。 | 本文
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | Contoso 准备其本地和 Azure 基础结构进行迁移。 该系列的所有迁移文章共用同一个基础结构。 | 可用
[文章 3：评估要迁移到 Azure 的本地资源](contoso-migration-assessment.md)  | Contoso 评估 VMware 上运行的本地 SmartHotel360 应用。 Contoso 使用 Azure Migrate 服务评估应用 VM，并使用数据迁移助手评估应用 SQL Server 数据库。 | 可用
[文章 4：在 Azure VM 和 SQL 数据库托管实例上重新托管应用](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso 将其本地 SmartHotel360 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM。 Contoso 使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 Azure SQL 数据库托管实例。 | 可用   
[文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | Contoso 使用 Site Recovery 服务将其 SmartHotel360 应用 VM 迁移到 Azure VM。 | 可用   [文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | Contoso 使用 Site Recovery 服务将其 SmartHotel360 应用 VM 迁移到 Azure VM。 | 可用
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组中重新托管应用](contoso-migration-rehost-vm-sql-ag.md) | Contoso 迁移 SmartHotel360 应用。 Contoso 使用 Site Recovery 迁移应用 VM。 它使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 可用 [文章 7：在 Azure VM 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm.md) | Contoso 使用 Site Recovery 服务将其 Linux osTicket 应用直接迁移到 Azure VM。 | 可用
[文章 8：在 Azure VM 和 Azure Database for MySQL 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | Contoso 使用 Site Recovery 将其 Linux osTicket 应用迁移到 Azure VM。 它使用 MySQL Workbench 将应用数据库迁移到 Azure Database for MySQL。 | 可用
[文章 9：在 Azure Web 应用和 Azure SQL 数据库中重构应用](contoso-migration-refactor-web-app-sql.md) | Contoso 将其 SmartHotel360 应用迁移到 Azure Web 应用，并使用数据库迁移助手将应用数据库迁移到 Azure SQL Server 实例。 | 可用    
[文章 10：在 Azure Web 应用和 Azure Database for MySQL 中重构 Linux 应用](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso 使用 Azure 流量管理器将其 Linux osTicket 应用迁移到多个 Azure 区域上的 Azure Web 应用，并与 GitHub 集成以实现持续交付。 Contoso 将应用数据库迁移到 Azure Database for MySQL 实例。 | 可用
[文章 11：在 Azure DevOps Services 中重构 Team Foundation Server](contoso-migration-tfs-vsts.md) | Contoso 将其本地 Team Foundation Server 部署迁移到 Azure 中的 Azure DevOps Services。 | 可用
[文章 12：在 Azure 容器和 Azure SQL 数据库中重构应用](contoso-migration-rearchitect-container-sql.md) | Contoso 将其 SmartHotel 应用迁移到 Azure。 然后，它将应用 Web 层重新架构为 Azure Service Fabric 中运行的 Windows 容器，以及具有 Azure SQL 数据库的数据库。 | 可用    
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | Contoso 使用一系列 Azure 功能和服务（包括 Azure 应用服务、Azure Kubernetes 服务 (AKS)、Azure Functions、Azure 认知服务和 Azure Cosmos DB）重新生成其 SmartHotel 应用。 | 可用 
[文章 14：到 Azure 的大规模迁移](contoso-migration-scale.md) | 尝试过组合迁移后，Contoso 准备大规模整体迁移到 Azure。 | 可用

在本文中，Contoso 设置完成所有迁移方案所需的所有基础结构元素。 







### <a name="demo-apps"></a>演示应用

这些文章使用两种演示应用 - SmartHotel360 和 osTicket。

- **SmartHotel360**：此应用由 Microsoft 开发，作为在使用 Azure 时可使用的测试应用。 它作为开放源代码提供，可从 [GitHub](https://github.com/Microsoft/SmartHotel360) 下载。 它是一种连接到 SQL Server 数据库的 ASP.NET 应用。 当前，该应用位于运行 Windows Server 2008 R2 和 SQL Server 2008 R2 的两个 VMware VM 上。 应用 VM 在本地托管，由 vCenter Server 管理。
- **osTicket**：一种在 Linux 上运行的开放源代码服务支持票证应用。 可以从 [GitHub](https://github.com/osTicket/osTicket) 下载它。 当前，该应用位于使用 Apache 2、PHP 7.0 和 MySQL 5.7 运行 Ubuntu 16.04 LTS 的两个 VMware VM 上
    

## <a name="next-steps"></a>后续步骤

[了解](contoso-migration-infrastructure.md) Contoso 如何设置本地和 Azure 基础结构以准备迁移。



