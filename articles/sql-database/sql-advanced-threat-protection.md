---
title: 高级威胁防护 - Azure SQL 数据库 | Microsoft Docs
description: 了解有关发现敏感数据并分类、管理数据库漏洞以及检测可能对 Azure SQL 数据库造成威胁的异常活动的功能。
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 5/17/2018
ms.author: ronitr
ms.reviewer: carlrab
ms.openlocfilehash: da21a0b66d86b4cc3e2dc59bdd972d4e24d7e5ec
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305587"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL 数据库的高级威胁防护

SQL 高级威胁防护是高级 SQL 安全功能的统一程序包。 它包括发现敏感数据并分类、管理数据库漏洞以及检测可能对数据库造成威胁的异常活动的功能。 它提供用于启用和管理这些功能的一个转到位置。 

## <a name="overview"></a>概述

SQL 高级威胁防护 (ATP) 提供一组高级 SQL 安全功能，包括数据发现和分类、漏洞评估和威胁检测。 

- [数据发现和分类](sql-database-data-discovery-and-classification.md)（当前为预览版）提供内置于 Azure SQL 数据库的功能，可用于发现、分类、标记和保护数据库中的敏感数据。 它可用于直观查看数据库分类状态，以及跟踪对数据库内和其边界外的敏感数据的访问。
- [漏洞评估](sql-vulnerability-assessment.md)是一项易于配置的服务，可以发现、跟踪并帮助修正潜在的数据库漏洞。 它可直观查看安全状态，包括解决安全问题的可操作步骤，并可加强数据库的防御工事。
- [威胁检测](sql-database-threat-detection.md)会检测异常活动，这些活动表示异常和可能有害的数据库访问或使用尝试。 它不断监视数据库的可疑活动，并针对潜在漏洞、SQL 注入攻击和异常数据库访问模式提供即时的安全警报。 威胁检测警报提供可疑活动的详细信息，以及如何调查和缓解威胁的推荐操作。

一旦启用 SQL ATP，其包含的所有功能都会启用。 仅需点击一下，便可在整个数据库服务器上启用 ATP，并应用到服务器上的全部数据库。 

ATP 定价遵循 Azure 安全中心标准层定价，按每节点每月 15 美元计费，其中每个受保护的 SQL 数据库服务器视为一个节点。 启用后的前 60 天被视为免费试用期，不会收费。 有关详细信息，请参阅 [Azure 安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="getting-started-with-atp"></a>ATP 入门 
以下步骤帮助你开始使用 ATP。 

## <a name="1-enable-atp"></a>1.启用 ATP

导航到 Azure SQL 数据库窗格“安全”标题下的“高级威胁防护”，以启用 ATP。 若要为服务器上的所有数据库启用 ATP，请单击“在服务器上启用高级威胁防护”。

![启用 ATP](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> ATP 的费用为每节点每月 15 美元，其中整个 SQL 逻辑服务器为一个节点。 因此，仅需支付一次，即可使用 ATP 保护服务器上的所有数据库。 前 60 天被视为免费试用期。

## <a name="2-configure-vulnerability-assessment"></a>2.配置漏洞评估

若要开始使用漏洞评估，需要配置保存扫描结果的存储帐户。 为此，请单击“漏洞评估”卡。

![配置 VA](./media/sql-advanced-protection/configure_va.png) 

选择或创建用于保存扫描结果的存储帐户。 还可以打开定期扫描来配置漏洞评估，以每周运行一次自动扫描。 扫描结果摘要会发送到你提供的电子邮件地址。

![VA 设置](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3.开始对数据分类、跟踪漏洞和调查威胁警报

单击“数据发现和分类”卡，查看建议进行分类的敏感列，并使用持久性敏感标签对数据分类。 单击“漏洞评估”卡，查看和管理漏洞扫描和报告，并跟踪安全状况。 如果收到安全警报，请单击“威胁检测”卡，查看警报的详细信息，并通过 Azure 安全中心安全警报页面查看 Azure 订阅中所有警报的综合报告。

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4.管理 SQL Server 上的 ATP 设置

若要查看和管理高级威胁防护设置，请导航到 SQL Server 窗格中“安全”标题下的“高级威胁防护”。 在此页上，可以启用或禁用 ATP，并修改整个 SQL Server 的威胁检测设置。

![服务器设置](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5.管理 SQL 数据库的 ATP 设置

若要重写特定数据库的 ATP 威胁检测设置，请勾选“在数据库级别启用高级威胁防护”复选框。 仅当有接收单个数据的单独威胁检测警报这一特殊要求时才使用此选项，以代替接收服务器上所有数据库的警报或在此基础上额外接收单独的威胁检测警报。 

一旦选择此复选框，请单击“此数据库的威胁检测设置”，然后配置此数据库的相关设置。

![数据库和威胁检测设置](./media/sql-advanced-protection/database_threat_detection_settings.png) 

也可以从 ATP 数据库窗格访问服务器的高级威胁防护设置。 单击主 ATP 窗格中的“设置”，然后单击“查看高级威胁防护服务器设置”。 

![数据库设置](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>后续步骤 

- 详细了解[数据发现和分类](sql-database-data-discovery-and-classification.md) 
- 详细了解[漏洞评估](sql-vulnerability-assessment.md) 
- 详细了解[威胁检测](sql-database-threat-detection.md)
- 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息
