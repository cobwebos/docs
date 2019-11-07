---
title: Azure Database for PostgreSQL 关系数据库服务概述
description: 概述了用于 PostgreSQL 关系数据库服务的 Azure 数据库。
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 9d4389ed2c0cc5d7b632380fba691ae2e9a6b257
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466749"
---
# <a name="what-is-azure-database-for-postgresql"></a>什么是用于 PostgreSQL 的 Azure 数据库？
Azure Database for PostgreSQL 是 Microsoft 云中为开发人员构建的关系型数据库服务。 它基于开源 [PostgreSQL](https://www.postgresql.org/) 数据库引擎的社区版本，提供两部署选项：单一服务器和超大规模 (Citus)。

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 单一服务器
“单一服务器”部署选项提供：

- 没有额外费用的内置高可用性 (99.99% SLA)
- 使用非独占即用即付定价，实现可预测性能
- 根据需要在数秒内进行垂直缩放
- 通过监视和警报功能快速评估缩放的影响
- 保护静态和动态敏感数据的安全
- 长达 35 天的自动备份和时间点还原
- 企业级安全性和符合性

所有这些功能几乎都不需要进行任何管理，并且都是在不另外收费的情况下提供的。 借助这些功能，用户可将注意力集中在如何快速进行应用程序开发、加快推向市场，而不需要投入宝贵的时间和资源来管理虚拟机与基础结构。 可以使用所选的开源工具和平台继续开发应用程序，不需学习新技能。

“单一服务器”部署选项提供三个定价层：“基本”、“常规用途”和“内存优化”。 每个层提供不同的资源功能以支持数据库工作负荷。 可以在一个月内花费很少的费用基于小型数据库构建第一个应用，然后根据解决方案的需求调整缩放。 动态可伸缩性使得数据库能够以透明方式对不断变化的资源需求做出响应。 只需在需要资源时为所需的资源付费。 有关详细信息，请参阅 [定价层](concepts-pricing-tiers.md)。

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - 超大规模 (Citus)
“超大规模(Citus)”选项可以使用分片跨多个计算机来水平缩放查询。 其查询引擎会将这些服务器的传入 SQL 查询并行化，加快大型数据集上的响应。 它为需要更大规模和更高性能的应用程序提供服务，通常情况下工作负荷接近或超过 100 GB 的数据。

“超大规模(Citus)”部署选项提供：

- 使用分片跨多个计算机进行水平缩放
- 将这些服务器的查询并行化，加快大型数据集上的响应
- 为多租户应用程序提供卓越的支持，并支持实时运营分析以及高吞吐量事务工作负荷

针对 PostgreSQL 构建的应用程序可以在超大规模 (Citus) 部署的基础上运行分布式查询，使用标准的[连接库](./concepts-connection-libraries.md)，基本不需要什么更改。

## <a name="data-security"></a>数据安全
Azure Database for PostgreSQL 沿袭了 Azure 数据库服务的数据安全传统。 其功能包括限制访问、保护静态数据和移动数据，以及帮助监视活动。 有关 Azure 平台安全性的信息，请访问 [Azure 信任中心](https://azure.microsoft.com/overview/trusted-cloud/)。

Azure Database for PostgreSQL 服务使用 FIPS 140-2 验证的加密模块对静态数据进行存储加密。 数据（包括备份）在磁盘上加密，运行查询时创建的临时文件除外。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终处于启用状态，无法禁用。 默认情况下，Azure Database for PostgreSQL 服务需要建立安全的连接才能在网络中以及在数据库和客户端应用程序之间移动数据。

## <a name="contacts"></a>联系人
如果有任何关于使用 Azure Database for PostgreSQL 的问题或建议，请向 Azure Database for PostgreSQL 团队 ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)) 发送电子邮件。 该地址处理常规问题，不处理支持票证。

另外，可以根据需要考虑以下联系点：
- 若要联系 Azure 支持部门或修复帐户问题，请[通过 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要提供反馈或请求新功能，请通过 [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) 创建条目。

## <a name="next-steps"></a>后续步骤
- 有关成本比较和计算器，请参阅[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。
- 开始时，请创建第一个 Azure Database for PostgreSQL [单一服务器](./quickstart-create-server-database-portal.md)或[超大规模 (Citus)](./quickstart-create-hyperscale-portal.md)
- 使用 Python、PHP、Ruby、C\#、Java、Node.js 构建第一个应用：[连接库](./concepts-connection-libraries.md)
