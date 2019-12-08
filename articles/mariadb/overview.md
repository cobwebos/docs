---
title: 概述 - Azure Database for MariaDB
description: 了解 Azure Database for MariaDB 服务，这是 Microsoft 云中基于 MySQL 社区版的关系数据库服务。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: f2dc6ec2fb99addc6a2c050c072e0221fd2c8e0b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769075"
---
# <a name="what-is-azure-database-for-mariadb"></a>什么是 Azure Database for MariaDB？

在 Microsoft 云中，Azure Database for MariaDB 是关系数据库服务。 Azure Database for MariaDB 基于 [MariaDB 社区版](https://mariadb.org/download/)（可在 GPLv2 许可证下使用）数据库引擎，版本为 10.2 和 10.3。

Azure Database for MariaDB 的优点包括：

- 没有额外费用的内置高可用性。
- 使用非独占即用即付定价，实现可预测性能。
- 根据需要在数秒内缩放。
- 保护静态和动态敏感数据的安全。
- 长达 35 天的自动备份和时间点还原。
- 企业级安全性和符合性。

这些功能几乎都不需要进行任何管理。 它们是在不另外收费的情况下提供的。 Azure Database for MariaDB 可以帮助你快速开发应用，加快面市时间。 你不需要投入宝贵的时间和资源来管理虚拟机与基础结构。 你也可以使用所选的开源工具和平台继续开发应用程序。 以业务需要的速度和效率进行交付，不需学习新技能。

若要了解 Azure Database for MariaDB 中的核心概念和功能，包括性能、可伸缩性和可管理性，请参阅以下快速入门：

- [使用 Azure 门户创建 Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [使用 Azure CLI 创建 Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>几秒钟内调整性能和规模

Azure Database for MariaDB 服务提供多个服务层级：“基本”、“常规用途”和“内存优化”。 每个层提供不同的性能和功能，以支持从轻型到重型的数据库工作负荷。 可以在一个月内花费很少的费用基于小型数据库构建第一个应用，然后根据解决方案的需求调整缩放。 动态可伸缩性有助于数据库以透明方式对不断变化的资源需求做出响应。 只需在需要资源时为所需的资源付费。 有关详细信息，请参阅 [定价层](concepts-pricing-tiers.md)。

## <a name="monitoring-and-alerting"></a>监视和警报

如何决定何时进行纵向扩展或缩减？ 可以将 Azure Database for MariaDB 的内置性能监视和警报功能与基于 vCore 的性能等级组合使用。 使用这些工具，可以根据当前的或预计的性能需求，快速评估调高或调低 vCore 数产生的影响。 有关详细信息，请参阅[警报](howto-alert-metric.md)。

## <a name="keep-your-app-and-business-running"></a>使应用和业务持续运转

Azure 行业领先的 99.99% 可用性 SLA 由 Microsoft 管理的数据中心的全球网络提供支持。 此网络可以让应用全天候运行。 你可以受益于 Azure Database for MariaDB 中的内置安全性、容错和数据保护功能。 使用 Azure Database for MariaDB，可以使用时间点还原将服务器还原到以前的状态，最多可还原到 35 天前。

## <a name="secure-your-data"></a>保护数据

Azure 数据库服务沿袭了 Azure Database for MariaDB 的若干数据安全性功能。 Azure Database for MariaDB 提供的功能可以限制访问、保护静态数据和移动数据，以及帮助监视活动。 有关 Azure 平台安全性的信息，请访问 [Azure 信任中心](https://www.microsoft.com/trustcenter/security)。 有关 Azure Database for MariaDB 安全功能的详细信息，请参阅[安全概述](concepts-security.md)。

## <a name="contacts"></a>联系人

如果有 Azure Database for MariaDB 使用方面的问题或建议，可以将其发送到 [Azure Database for MariaDB 团队](mailto:AskAzureDBforMariaDB@service.microsoft.com)（不是技术支持团队的别名）。

还可以使用以下联系点：
- 若要联系 Azure 支持部门，请在 Azure 门户中[提出支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- 若要修复帐户问题，请在 Azure 门户中[提出支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
- 若要提供反馈或请求新功能，请在 [Azure 反馈论坛](https://feedback.azure.com/forums/915439-azure-database-for-mariadb)中创建一个条目。

## <a name="next-steps"></a>后续步骤

阅读完 Azure Database for MariaDB 简介后，即可：
- 有关成本比较和计算器，请参阅[定价](https://azure.microsoft.com/pricing/details/mariadb/)页。 
- 从[创建第一个服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)开始。

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
