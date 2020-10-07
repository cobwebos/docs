---
title: 概述 - Azure Database for MySQL
description: 了解 Azure Database for MySQL 服务，这是 Microsoft 云中基于 MySQL 社区版的关系数据库服务。
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 51c194ca9b091bc685f293320750da55925ad49d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91565609"
---
# <a name="what-is-azure-database-for-mysql"></a>什么是用于 MySQL 的 Azure 数据库？

Azure Database for MySQL 是 Microsoft 云中的关系数据库服务，基于 [MySQL Community Edition](https://www.mysql.com/products/community/)（在 GPLv2 许可证下提供）数据库引擎 5.6、5.7 和 8.0 版。 用于 MySQL 的 Azure 数据库提供以下功能：

- 内置的高可用性。
- 使用自动备份和时间点还原对数据进行长达 35 天的保护。
- 自动维护基础硬件、操作系统和数据库引擎，使服务保持安全和最新状态。
- 使用非独占即用即付定价，实现可预测性能。
- 在几秒钟内实现弹性缩放。
- 通过停止/启动服务器，实现成本优化控制。 
- 具有企业级安全性和行业领先的符合性，可保护静态和动态敏感数据。
- 具有监视和自动化功能，可简化大规模部署的管理和监视。
- 行业领先的支持体验。

这些功能几乎都不需要进行任何管理，并且都是在不另外收费的情况下提供的。 借助这些功能，用户可将注意力集中在如何快速进行应用开发、加快推向市场，而不需要投入宝贵的时间和资源来管理虚拟机与基础结构。 此外，可以继续使用选择的开源工具和平台来开发应用程序，以提供业务所需的速度和效率，这些都不需要学习新技能。

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="用于 MySQL 的 Azure 数据库概念图":::

## <a name="deployment-models"></a>部署模型

由 MySQL 社区版提供支持的 Azure Database for MySQL 提供两种部署模式：
- 单台服务器 
- 灵活服务器（预览版）
  
### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 单一服务器

Azure Database for MySQL 单一服务器是一项完全托管的数据库服务，对数据库自定义的要求最低。 单一服务器平台旨在以最少的用户配置和控制来处理大多数数据库管理功能，例如修补、备份、高可用性、安全性。 此体系结构已进行优化，提供内置的高可用性，在单个可用性区域的可用性为 99.99%。 它支持 MySQL 社区版 5.6、5.7 和 8.0。 目前，该服务已在各种 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/)中正式发布。

“单一服务器”部署选项提供三个定价层：“基本”、“常规用途”和“内存优化”。 每个层提供不同的资源功能以支持数据库工作负荷。 可以在一个月内花费很少的费用基于小型数据库构建第一个应用，然后根据解决方案的需求调整缩放。 动态可伸缩性使得数据库能够以透明方式对不断变化的资源需求做出响应。 只需在需要资源时为所需的资源付费。 有关详细信息，请参阅[定价层](concepts-pricing-tiers.md)。

单一服务器最适合用于云原生应用程序，这些应用程序旨在处理自动修补，而无需对修补计划和自定义 MySQL 配置设置进行精细控制。 

有关单一服务器部署模式的详细概述，请参阅[单一服务器概述](single-server-overview.md)。

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活服务器（预览版）

Azure Database for MySQL 灵活服务器是一种完全托管的数据库服务，旨在针对数据库管理功能和配置设置提供更精细的控制和更大的灵活性。 通常，该服务会根据用户要求提供更多的灵活性和自定义。 灵活服务器体系结构允许用户选择单个可用性区域内以及跨多个可用性区域的高可用性。 灵活服务器提供了更好的成本优化控制，能够停止/启动服务器和可突增计算层，非常适合不需要连续的全部计算容量的工作负载。 该服务当前支持 MySQL 社区版 5.7，并计划很快添加更新版本。 此服务目前处于公共预览状态，现已在各种 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/)中提供。

灵活服务器最适合用于 
- 需要更好的控制和自定义的应用程序开发。
- 区域冗余高可用性
- 托管维护时段

有关灵活服务器部署模式的详细概述，请参阅[灵活服务器概述](flexible-server/overview.md)。

## <a name="contacts"></a>联系人
如果有任何关于使用 Azure Database for MySQL 的问题或建议，请发送电子邮件至 Azure Database for MySQL 团队 ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))。 此电子邮件地址不是技术支持别名。

另外，请酌情考虑以下联系点：

- 若要联系 Azure 支持，请[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要修复帐户问题，请在 Azure 门户中提交[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
- 若要提供反馈或请求新功能，请通过 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) 创建条目。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Database for MySQL 的两种部署模式，并根据需要选择合适的选项。

- [单一服务器](single-server/index.yml)
- [灵活服务器](flexible-server/index.yml)
- [为工作负载选择正确的 MySQL 部署选项](select-right-deployment-type.md)
