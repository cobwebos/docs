---
title: 选择适当的部署类型 - Azure Database for MySQL
description: 本文介绍将 Azure Database for MySQL 部署为基础结构即服务 (IaaS) 或平台即服务 (PaaS) 之前应考虑的因素。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: a1b66528bee63fb123271e4277e122603ced2e75
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906505"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>在 Azure 中选择适当的 MySQL Server 选项

在 Azure 中，MySQL 服务器工作负荷可以在托管的虚拟机基础结构即服务 (IaaS) 中运行，或者作为托管的平台即服务 (PaaS) 运行。 PaaS 提供多个部署选项，每个部署选项中有多个服务层级。 在 IaaS 和 PaaS 之间选择时，必须决定是要管理数据库、应用修补程序并进行备份，还是要将这些操作委托给 Azure。

做出决策时，请考虑以下选项：

- **Azure Database for MySQL**。 此选项是基于稳定 MySQL 社区版的完全托管式 MySQL 数据库引擎。 此关系数据库即服务 (DBaaS) 托管在 Azure 云平台中，属于 PaaS 行业类别。

  使用 Azure 上的 MySQL 托管实例，你可以使用内置功能即自动修补、高可用性、自动备份、弹性缩放、企业级安全性、合规性和监管、监视和警报，否则在 MySQL 服务器位于本地或 Azure VM 时需要进行大量配置。 使用 MySQL 即用即付时，可以选择纵向扩展或横向扩展，以实现更好的控制，无需中断。 
  
  [Azure Database for MySQL](overview.md)，由 MySQL 社区版提供支持的两种部署模式：
    - [单服务器](single-server-overview.md) 是一种完全托管的数据库服务，对数据库自定义的要求最低。 单一服务器平台旨在以最少的用户配置和控制来处理大多数数据库管理功能，例如修补、备份、高可用性、安全性。 此体系结构经过优化，可在单个可用性区域提供 99.99% 的可用性。 单一服务器最适合用于云原生应用程序，这些应用程序旨在处理自动修补，而无需对修补计划和自定义 MySQL 配置设置进行精细控制。 
    
    - [灵活的服务器 (预览版) ](flexible-server/overview.md) 是一种完全托管的数据库服务，旨在提供对数据库管理功能和配置设置的更精细的控制和灵活性。 通常，该服务根据用户要求与单一服务器部署相比，提供更多的灵活性和服务器配置自定义。 灵活的服务器体系结构允许用户在单个可用性区域内以及跨多个可用性区域选择高可用性。 灵活的服务器还提供了可启动/停止服务器和可突增 Sku 的更好的成本优化控制功能，适用于不需要持续进行完全计算容量的工作负载。 
    灵活的服务器最适用于：
     
      - 需要更好地控制和自定义 MySQL 引擎的应用程序开发。
      - 区域冗余高可用性
      - 托管维护时段

- **Azure VM 上的 MySQL**。 此选项属于 IaaS 行业类别。 使用此服务，你可以在 Azure 云平台上的托管虚拟机中运行 MySQL Server。 所有最新版本的 MySQL 都可以安装在虚拟机中。

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>比较 Azure 中的 MySQL 部署选项

下表列出了这些选项之间的主要差别：


| 属性          | Azure Database for MySQL<br/>单台服务器 |Azure Database for MySQL<br/>灵活服务器  |Azure VM 上的 MySQL                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| MySQL 版本支持 | 5.6，5.7 & 8。0| 5.7 | 任何版本|
| 计算缩放 | 不支持从和到基本层的 (缩放) | 支持 | 支持|
| 存储大小 | 5 GiB 到 16 TiB| 5 GiB 到 16 TiB | 32 GiB 到 32767 GiB|
| 在线存储缩放 | 支持| 支持| 不支持|
| 自动存储缩放 | 支持| 预览中不支持| 不支持|
| 网络连接 | -具有服务器防火墙的公共终结点。<br/> -支持专用链接的专用访问。|-具有服务器防火墙的公共终结点。<br/> -使用虚拟网络集成的专用访问。| -具有服务器防火墙的公共终结点。<br/> -支持专用链接的专用访问。|
| 服务级别协议 (SLA) | 99.99% 的可用性 SLA |预览版中没有 SLA| 使用可用性区域为99.99%|
| 操作系统修补| 自动  | 自定义维护窗口控制自动 | 由最终用户管理 |
| MySQL 修补     | 自动  | 自定义维护窗口控制自动 | 由最终用户管理 |
| 高可用性 | 单个可用性区域内的内置 HA| 内部和跨可用性区域的 HA | 使用聚类分析的自定义托管，复制等|
| 区域冗余 | 不支持 | 支持 | 支持|
| 混合场景 | 支持 [数据传入复制](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)| 不适用于预览版 | 由最终用户管理 |
| 只读副本 | 支持| 支持 | 由最终用户管理 |
| 备份 | 自动保留7-35 天 | 自动保留1-35 天 | 由最终用户管理 |
| 监视数据库操作 | 支持 | 支持 | 由最终用户管理 |
| 灾难恢复 | 支持异地冗余备份存储和跨区域读取副本 | 预览中不支持| 自定义管理和复制技术 |
| 查询性能见解 | 支持 | 不适用于预览版| 由最终用户管理 |
| 预留实例定价 | 支持 | 不适用于预览版 | 支持 |
| Azure AD 身份验证 | 支持 | 不适用于预览版 | 不支持|
| 静态数据加密 | 支持客户托管密钥 | 支持服务托管密钥 | 不支持|
| SSL/TLS | 默认情况下启用，支持 TLS v1.0，1.1 和1。0 | 使用 TLS 1。2 | 支持 TLS 1.2、1.1 和1。0 | 
| 汽油管理 | 支持 Azure CLI、PowerShell、REST 和 Azure 资源管理器 | 支持 Azure CLI、PowerShell、REST 和 Azure 资源管理器  | 支持 Azure CLI、PowerShell、REST 和 Azure 资源管理器的 Vm |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>选择 PaaS 或 IaaS 的业务动机

有多个因素可能会影响你决定选择 PaaS 或 IaaS 来托管 MySQL 数据库。

### <a name="cost"></a>成本

成本降低通常是确定用于托管数据库的最佳解决方案的主要考虑因素。 无论你是现金不足的创业公司，或是在预算严格受限的情况下运作现有公司的团队，都存在这种情况。 本部分介绍 Azure 中适用于 Azure Database for MySQL 和 Azure VM 上的 MySQL 的计费与许可基础知识。

#### <a name="billing"></a>计费

Azure Database for MySQL 目前在多个层级中以服务的形式提供，它资源价格各不相同。 所有资源都按固定费率按小时计费。 有关当前支持的服务层、计算大小和存储量的最新信息，请参阅 [定价页](https://azure.microsoft.com/pricing/details/mysql/)。 可以动态调整服务层级和计算大小，以满足应用程序的不同吞吐量需求。 你需要按一般的[数据传输费率](https://azure.microsoft.com/pricing/details/data-transfers/)支付 Internet 流量传出费用。

在 Azure Database for MySQL 中，Microsoft 自动配置、修补和升级数据库软件。 这些自动化操作可以降低管理成本。 此外，Azure Database for MySQL 具有 [自动备份](https://docs.microsoft.com/azure/mysql/concepts-backup) 功能。 这些功能可帮助你大幅节省成本，尤其是存在大量的数据库时。 相比之下，对于 Azure VM 上的 MySQL，可以选择并运行任何 MySQL 版本。 无论使用何种 MySQL 版本，都需要支付预配的 VM、与数据、备份、监视数据和日志存储相关联的存储成本，以及在任何)  (使用的特定 MySQL 许可证类型的费用。

Azure Database for MySQL 针对任何类型的节点级中断提供内置高可用性，同时仍可为服务维护 99.99% 的 SLA 保证。 但是，对于 Vm 内的数据库高可用性，你可以使用 mysql 数据库中可用的高可用性选项，如 [mysql 复制](https://dev.mysql.com/doc/refman/8.0/en/replication.html) 。 使用支持的高可用性选项不会提供额外的 SLA。 但是，它可以让你凭借额外的成本和管理开销实现 99.99% 以上的数据库可用性。

有关定价的详细信息，请参阅以下文章：
* [Azure Database for MySQL 定价](https://azure.microsoft.com/pricing/details/mysql/)
* [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>管理

对许多企业来说，决定过渡到到云服务的关键在于降低管理复杂度，因为这涉及到成本。 

借助 IaaS，Microsoft 可以实现以下目的：

- 管理底层基础结构。
- 为底层硬件和操作系统提供自动修补。
  
借助 PaaS，Microsoft 可以实现以下目的：

- 管理底层基础结构。
- 为基础硬件、OS 和数据库引擎提供自动修补。
- 管理数据库的高可用性。
- 自动执行备份并复制所有数据以提供灾难恢复。
- 默认加密静态数据和动态数据。
- 监视服务器并提供查询性能见解和性能建议的功能

以下列表描述了每个选项的管理注意事项：

* 使用 Azure Database for MySQL 可以持续管理数据库。 但是，不再需要管理数据库引擎、操作系统或硬件。 可以持续管理的项的示例包括：

  - 数据库
  - 登录
  - 索引优化
  - 查询优化
  - 审核
  - 安全性

  此外，在另一个数据中心配置高可用性只需极少量的配置或管理，或者根本无需配置或管理。

* 使用 Azure VM 上的 MySQL，可以完全掌控操作系统和 MySQL 服务器实例配置。 使用 VM，可以决定何时更新或升级操作系统和数据库软件，以及应用哪些补丁。 还可以决定何时安装任何其他软件，例如防病毒应用程序。 提供的某些自动化功能可以大大简化修补、备份和高可用性。 可以控制 VM 的大小、磁盘数目及其存储配置。 有关详细信息，请参阅 [Azure 的虚拟机和云服务大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。

### <a name="time-to-move-to-azure"></a>迁移到 Azure 的时机

* 当开发人员工作效率和新解决方案的快速面市时间至关重要时，Azure Database for MySQL 是面向云的应用程序的适当解决方案。 该服务提供类似于 DBA 的编程功能，非常适合云架构师和开发人员，因为它能降低管理底层操作系统和数据库的需求。

* 如果你想要避免获取新的本地硬件的时间和开销，Azure Vm 上的 MySQL 是适用于需要对服务不支持的 MySQL 引擎进行精细控制和自定义的应用程序的正确解决方案，或者需要访问基础操作系统。 如果 Azure Database for MySQL 不合适，则此解决方案也很适合将现有的本地应用程序和数据库按原样迁移到 Azure。

由于无需更改呈现层、应用层和数据层，重新架构现有解决方案时可以节省时间和预算。 你可以专注于将所有解决方案迁移到 Azure，并执行 Azure 平台可能需要的某些性能优化。

## <a name="next-steps"></a>后续步骤

* 参阅 [Azure Database for MySQL 定价](https://azure.microsoft.com/pricing/details/MySQL/)。
* 从[创建第一个服务器](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal)开始。
