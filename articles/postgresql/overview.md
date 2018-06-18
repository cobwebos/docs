---
title: Azure Database for PostgreSQL 关系数据库服务概述
description: 概述了用于 PostgreSQL 关系数据库服务的 Azure 数据库。
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 06/02/2018
ms.openlocfilehash: fab3aa7d0b1fe0f4fafd4b2fdca24ad0e8a3d03c
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737225"
---
# <a name="what-is-azure-database-for-postgresql"></a>什么是用于 PostgreSQL 的 Azure 数据库？

Azure Database for PostgreSQL 是 Microsoft 云中基于开源 [PostgreSQL](https://www.postgresql.org/) 数据库引擎的社区版本、为开发人员构建的关系数据库服务。 用于 PostgreSQL 的 Azure 数据库提供以下功能：

- 没有额外费用的内置高可用性
- 使用非独占即用即付定价，实现可预测性能
- 根据需要在数秒内缩放
- 保护静态和动态敏感数据的安全
- 长达 35 天的自动备份和时间点还原
- 企业级安全性和符合性

所有这些功能几乎都不需要进行任何管理，并且都是在不另外收费的情况下提供的。 借助这些功能，用户可将注意力集中在如何快速进行应用程序开发、加快推向市场，而不需要投入宝贵的时间和资源来管理虚拟机与基础结构。 此外，可以继续使用所选的开源工具和平台来开发应用程序，并提供业务所需的速度和效率，并且不需要学习新技能。 

本文介绍用于 PostgreSQL 的 Azure 数据库在性能、伸缩性和易管理性方面的核心概念与功能。 请参阅这些快速入门，以便尽快入门：

- [使用 Azure 门户创建用于 PostgreSQL 的 Azure 数据库](quickstart-create-server-database-portal.md)
- [使用 Azure CLI 创建用于 PostgreSQL 的 Azure 数据库](quickstart-create-server-database-azure-cli.md)

如需一组 Azure CLI 示例，请参阅：

- [用于 PostgreSQL 的 Azure 数据库的 Azure CLI 示例](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>几秒钟内调整性能和规模
Azure Database for PostgreSQL 服务提供三个定价层：“基本”、“常规用途”和“内存优化”。 每个层提供不同的资源功能以支持数据库工作负荷。 可以在一个月内花费很少的费用基于小型数据库构建第一个应用，然后根据解决方案的需求调整缩放。 动态可伸缩性使得数据库能够以透明方式对不断变化的资源需求做出响应。 只需在需要资源时为所需的资源付费。 有关详细信息，请参阅[定价层](concepts-pricing-tiers.md)。

## <a name="monitoring-and-alerting"></a>监视和警报
如何决定何时调高或调低？ 使用内置的 Azure 监视和警报功能。 使用这些工具，可以根据当前的或预计的性能或存储需求，快速评估调高或调低产生的影响。 有关详细信息，请参阅[警报](howto-alert-on-metric.md)。

## <a name="keep-your-app-and-business-running"></a>使应用和业务持续运转
Azure 行业领先的 99.99% 可用性服务级别协议 (SLA)（由 Microsoft 管理的数据中心的全球网络提供支持），有助于保持应用全天候运行。 对于每个用于 PostgreSQL 服务器的 Azure 数据库，可以利用内置的安全性、容错和数据保护功能。使用其他产品时可能需要另外购买或设计、构建并管理这些功能。 对于 Azure Database for PostgreSQL，每个定价层都提供了一组全面的业务连续性功能和选项，可以使用这些功能和选项快速进入工作状态并保持运行。 可以使用[时间点还原](howto-restore-server-portal.md)将数据库还原到以前的状态，最长可还原到 35 天前。 此外，如果托管数据库的数据中心发生服务中断，可以通过最新备份的异地冗余副本还原数据库。

## <a name="secure-your-data"></a>保护数据
Azure 数据库沿袭了 Azure Database for PostgreSQL 的若干数据安全性功能，包括限制访问、保护静止和移动数据以及帮助监视活动等。 有关 Azure 平台安全性的信息，请访问 [Azure 信任中心](https://www.microsoft.com/en-us/trustcenter/security)。

用于 PostgreSQL 的 Azure 数据库服务对静态数据使用存储加密。 包括备份在内的数据都在磁盘中加密（运行查询时由引擎创建的临时文件除外）。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终处于启用状态，无法禁用。

默认情况下，用于 PostgreSQL 的 Azure 数据库服务被配置为要求整个网络中的动态数据具备 [SSL 连接安全性](./concepts-ssl-connection-security.md)。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 （可选）如果客户端应用程序不支持 SSL 连接，则可以禁止要求 SSL 连接到数据库服务。

## <a name="contacts"></a>联系人
如果你有任何关于使用 Azure Database for MySQL 的问题或建议，请发送电子邮件至 Azure Database for MySQL 团队 ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com))。 请注意，这不是技术支持别名。

另外，请酌情考虑以下联系点：
- 若要联系 Azure 支持，请[从 Azure 门户提交票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要修复帐户问题，请在 Azure 门户中提交[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
- 若要提供反馈或请求新功能，请通过 [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) 创建条目。

## <a name="next-steps"></a>后续步骤
- 有关成本比较和计算器，请参阅[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。
- 请从[创建第一个用于 PostgreSQL 的 Azure 数据库](./quickstart-create-server-database-portal.md)开始。
- 使用 Python、PHP、Ruby、C\#、Java、Node.js 构建第一个应用：[连接库](./concepts-connection-libraries.md)
