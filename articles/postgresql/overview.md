---
title: "用于 PostgreSQL 关系数据库服务的 Azure 数据库的概述 | Microsoft Docs"
description: "概述了用于 PostgreSQL 关系数据库服务的 Azure 数据库。"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 08/01/2017
ms.openlocfilehash: 0dba4db0ae62a4f7e8440e80e830e5f64f5b49e0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="what-is-azure-database-for-postgresql"></a>什么是用于 PostgreSQL 的 Azure 数据库？

用于 PostgreSQL 的 Azure 数据库是 Microsoft 云中基于开源 [PostgreSQL](https://www.postgresql.org/) 数据库引擎的社区版本、为开发人员构建的关系数据库服务。 此服务目前以公共预览版提供。 用于 PostgreSQL 的 Azure 数据库提供以下功能：
- 在多个服务级别提供可预测的性能
- 在不使应用程序停机的情况下提供动态可伸缩性
- 内置的高可用性
- 数据保护

所有这些功能几乎都不需要进行任何管理，并且都是在不另外收费的情况下提供的。 借助这些功能，用户可将注意力集中在如何快速进行应用程序开发、加快推向市场，而不需要投入宝贵的时间和资源来管理虚拟机与基础结构。 此外，可以继续使用选择的开源工具和平台来开发应用程序，并提供业务所需的速度和效率，并且不需要学习新技能。 

本文介绍用于 PostgreSQL 的 Azure 数据库在性能、伸缩性和易管理性方面的核心概念与功能。 请参阅这些快速入门，以便尽快入门：

- [使用 Azure 门户创建用于 PostgreSQL 的 Azure 数据库](quickstart-create-server-database-portal.md)
- [使用 Azure CLI 创建用于 PostgreSQL 的 Azure 数据库](quickstart-create-server-database-azure-cli.md)

如需一组 Azure CLI 和 PowerShell 的示例，请参阅：

- [用于 PostgreSQL 的 Azure 数据库的 Azure CLI 示例](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>无需停机即可调整性能和规模

用于 PostgreSQL 的 Azure 数据库服务当前提供了两个服务层：基本和标准。 每个服务层提供[不同级别的性能、IOPS 保证和功能](concepts-service-tiers.md)，以支持从轻型到重型的数据库工作负荷。 可以在小型服务器中构建第一个应用，每个月只需花费少量的资金。然后可以根据解决方案的需要，随时手动或以编程方式在服务层内[更改性能级别](scripts/sample-scale-server-up-or-down.md)。 这不会给应用程序或客户造成任何停机。 动态可伸缩性可让数据库以透明方式响应快速变化的资源要求，使用户只需为用到的资源付费。

## <a name="monitoring-and-alerting"></a>监视和警报
如何决定何时调高和调低？ 将内置的性能监视和警报功能与基于计算单位的性能等级组合使用。 使用这些工具，可以根据当前的或预计的性能需求，快速评估调高或调低计算单位产生的影响。 有关详细信息，请参阅[用于 PostgreSQL 的 Azure 数据库的选项和性能：了解每个服务层提供的功能](./concepts-service-tiers.md)。

## <a name="keep-your-app-and-business-running"></a>使应用和业务持续运转
Azure 行业领先的 99.99% 可用性（在预览版中不可用）服务级别协议 (SLA)（由 Microsoft 管理的数据中心的全球网络提供支持），有助于保持应用全天候运行。 对于每个用于 PostgreSQL 服务器的 Azure 数据库，可以利用内置的安全性、容错和数据保护功能。使用其他产品时可能需要另外购买或设计、构建并管理这些功能。 对于用于 PostgreSQL 的 Azure 数据库，每个服务层都提供了一组全面的业务连续性功能和选项，可以使用这些功能和选项快速进入工作状态并保持运行。 可以使用[时间点还原](howto-restore-server-portal.md)将数据库还原到以前的状态，最长可还原到 35 天前。 此外，如果托管数据库的数据中心发生服务中断，可以通过最新备份的异地冗余副本还原数据库。

## <a name="secure-your-data"></a>保护数据
Azure 数据库服务具有传统 PostgreSQL 的 Azure 数据库保持具有限制的访问、 保护静态的数据和、 移动和帮助你监视活动的功能的数据安全。 有关 Azure 平台安全性的信息，请访问 [Azure 信任中心](https://www.microsoft.com/TrustCenter/Security/default.aspx)。

用于 PostgreSQL 的 Azure 数据库服务对静态数据使用存储加密。 会在磁盘上加密包括备份在内的数据（运行查询时引擎所创建的临时文件除外）。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终处于启用状态，无法禁用。

默认情况下，用于 PostgreSQL 的 Azure 数据库服务被配置为要求整个网络中的动态数据具备 [SSL 连接安全性](./concepts-ssl-connection-security.md)。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。  （可选）如果客户端应用程序不支持 SSL 连接，则可以禁止要求 SSL 连接到数据库服务。

## <a name="next-steps"></a>后续步骤
- 有关成本比较和计算器，请参阅[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。
- 请从[创建第一个用于 PostgreSQL 的 Azure 数据库](./quickstart-create-server-database-portal.md)开始。
- 使用 Python、PHP、Ruby、C\#、Java、Node.js 构建第一个应用：[连接库](./concepts-connection-libraries.md)
