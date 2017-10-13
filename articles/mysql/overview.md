---
title: "用于 MySQL 关系数据库服务的 Azure 数据库的概述 | Microsoft Docs"
description: "用于 MySQL 关系数据库服务的 Azure 数据库的概述。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.custom: mvc
ms.openlocfilehash: 3eff97a9e7568566cd01a4eb3c7ee66806eac4cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-database-for-mysql-service-introduction"></a>什么是用于 MySQL 的 Azure 数据库？ 服务简介
用于 MySQL 的 Azure 数据库是 Microsoft 云中基于 [MySQL 社区版](https://www.mysql.com/products/community/)数据库引擎的一种关系数据库服务。  用于 MySQL 的 Azure 数据库提供以下功能：

- 在多个服务级别提供可预测的性能。
- 在不使应用程序停机的情况下提供动态可伸缩性。
- 内置的高可用性。
- 数据保护。

这些功能几乎都不需要进行任何管理，并且都是在不另外收费的情况下提供的。 借助这些功能，用户可将注意力集中在如何快速进行应用开发、加快推向市场，而不需要投入宝贵的时间和资源来管理虚拟机与基础结构。 此外，可以继续使用选择的开源工具和平台来开发应用程序，以提供业务所需的速度和效率，这些都不需要学习新技能。

![用于 MySQL 的 Azure 数据库概念图](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

本文介绍了用于 MySQL 的 Azure 数据库在性能、伸缩性和易管理性方面的核心概念与功能，并提供了可进一步了解详细信息的链接。 请参阅这些快速入门，以便尽快入门：
- [使用 Azure 门户创建用于 MySQL 服务器的 Azure 数据库](quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 创建用于 MySQL 服务器的 Azure 数据库](quickstart-create-mysql-server-database-using-azure-cli.md)

如需一组 Azure CLI 示例，请参阅：
- [用于 MySQL 的 Azure 数据库的 Azure CLI 示例](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>无需停机即可调整性能和规模
用于 MySQL 的 Azure 数据库服务提供了两个服务层：基本和标准。 每个层提供不同的性能和功能，以支持从轻型到重型的数据库工作负荷。 可以在一个月内花费很少的费用基于小型数据库构建第一个应用，然后可以在不停机的情况下根据解决方案的需求更改服务层来进行缩放。 动态可伸缩性使得数据库能够以透明方式对不断变化的资源需求做出响应。 只需在需要资源时为所需的资源付费。

## <a name="monitoring-and-alerting"></a>监视和警报
当调高和调低性能时，如何知道该在何处停止？ 将内置的性能监视和警报功能与基于计算单位的性能等级组合使用。 使用这些功能，可以根据当前需求或项目性能的需求，快速评估调高或调低性能产生的影响。 有关详细信息，请参阅[概念：服务层](concepts-service-tiers.md)。

## <a name="keep-your-app-and-business-running"></a>使应用和业务持续运转
Azure 行业领先的 99.99% 可用性服务级别协议 (SLA)（由 Microsoft 管理的数据中心的全球网络提供支持），有助于保持应用全天候运行。 对于每个用于 MySQL 服务器的 Azure 数据库，可以利用内置的安全性、容错和数据保护功能。使用其他产品时可能需要另外购买或设计、构建并管理这些功能。 使用用于 MySQL 的 Azure 数据库，可以使用时间点还原将服务器还原到以前的状态，最多可还原到 35 天前。

## <a name="secure-your-data"></a>保护数据
Azure 数据库沿袭了 Azure Database for MySQL 的若干数据安全性功能，包括限制访问、保护静止和移动数据，以及帮助监视活动等。 有关 Azure 平台安全性的信息，请访问 [Azure 信任中心](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx)。

用于 MySQL 的 Azure 数据库服务为静态数据使用存储加密。 包括备份在内的数据都在磁盘中加密（运行查询时由引擎创建的临时文件除外）。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终可用，并且不能禁用。

默认情况下，将用于 MySQL 的 Azure 数据库服务配置为要求网络中传输的数据具备 [SSL 连接安全性](./concepts-ssl-connection-security.md)。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。  （可选）如果客户端应用程序不支持 SSL 连接，则可以禁止要求 SSL 连接到数据库服务。

## <a name="next-steps"></a>后续步骤
现在，已阅读了用于 MySQL 的 Azure 数据库的简介并回答了“什么是用于 MySQL 的 Azure 数据库？”这个问题，因此可以继续完成以下内容：
- 有关成本比较和计算器，请参阅定价页。 [定价](https://azure.microsoft.com/pricing/details/mysql/)
- 从创建第一个服务器开始。 [使用 Azure 门户创建用于 MySQL 服务器的 Azure 数据库](quickstart-create-mysql-server-database-using-azure-portal.md)
- 使用 Python、PHP、Ruby、C\#、Java、Node.js 构建第一个应用：[用来连接到用于 MySQL 的 Azure 数据库的连接库](concepts-connection-libraries.md)
