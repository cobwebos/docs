---
title: "Azure 安全中心与 Azure SQL 数据库服务 | Microsoft 文档"
description: "本文演示安全中心如何能够帮助保护 Azure SQL 数据库中的数据库。"
services: sql-database
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: c10a35e4828e8da427f324ef3c3892e7e24cc72d
ms.openlocfilehash: ba8fd7ea8979c8c2afbace60a5675b411438d6b4


---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure 安全中心与 Azure SQL 数据库服务
[Azure 安全中心](https://azure.microsoft.com/documentation/services/security-center/)可帮助防范、检测和应对威胁。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

本文演示安全中心如何能够帮助保护 Azure SQL 数据库中的数据库。

## <a name="why-use-security-center"></a>为什么要使用安全中心？
安全中心通过为你的所有服务器和数据库提供安全性的可见性来帮助保护 SQL 数据库中的数据。 通过安全中心，可以：

* 定义 SQL 数据库加密和审核的策略。
* 跨所有订阅监视 SQL 数据库资源的安全性。
* 快速识别和修复安全问题。
* 集成来自 [Azure SQL 数据库威胁检测](../sql-database/sql-database-threat-detection-get-started.md)的警报。

除帮助保护 SQL 数据库资源外，安全中心还可安全监视和管理 Azure 虚拟机、云服务、应用程序服务和虚拟网络等。 在[此处](security-center-intro.md)了解有关安全中心的详细信息。

## <a name="prerequisites"></a>先决条件
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 使用你的订阅启用安全中心的免费层。 了解有关安全中心免费层和标准层的详细信息，请参阅[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)。

安全中心支持基于角色的访问。 若要了解 Azure 中有关基于角色的访问控制 (RBAC) 的详细信息，请参阅 [Azure Active Directory 基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。 安全中心常见问题提供有关[如何在安全中心处理权限](security-center-faq.md#permissions)的信息。

## <a name="access-security-center"></a>访问安全中心
从 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)访问安全中心。 [登录到门户](https://portal.azure.com/)，然后选择**安全中心选项**。

![安全中心选项][1]

这将打开“**安全中心**”边栏选项卡。
![“安全中心”边栏选项卡][2]

## <a name="set-security-policy"></a>设置安全策略
安全策略用于定义一组控制，这些控制是针对指定订阅或资源组中的资源建议的。 在安全中心，根据公司的安全需求、应用程序的类型或每个订阅中数据的敏感度，为你的订阅或资源组定义策略。

可以设置介绍 SQL 审核与 SQL 透明数据加密 (TDE) 建议的策略。

* 打开 **SQL 审核与威胁检测**时，安全中心建议允许审核对 Azure 数据库的访问权限，确保合规性并方便进行高级检测和调查。
* 打开 **SQL 透明数据加密**时，安全中心建议为 Azure SQL 数据库、关联的备份和事务日志文件启用静态加密。

若要设置安全策略，请选择“安全中心”边栏选项卡上的“**策略**”磁贴。 在“**安全策略**”边栏选项卡上，选择想要启用安全策略的订阅。 选择“**保护策略**”，并**开启**需要用于此订阅的安全建议。
![安全策略][3]

若要了解详细信息，请参阅[设置安全策略](security-center-policies.md)。

## <a name="manage-security-recommendation"></a>管理安全建议
安全中心定期分析 Azure 资源的安全状态。 安全中心识别到潜在的安全漏洞时，会创建建议。 此建议将指导你完成配置所需控件的过程。

设置安全策略之后，安全中心将分析你的资源的安全状态，以识别潜在的漏洞。 建议将以表格形式显示，其中每一行都表示一个特定的建议。 使用下表作为参考，以帮助你理解 Azure SQL 数据库的可用建议，以及应用每个建议可实现的功能。 选择一个建议会将你转到介绍如何在安全中心实现建议的文章。

| 建议 | 说明 |
| --- | --- |
| [在 SQL 服务器上启用审核与威胁检测](security-center-enable-auditing-on-sql-servers.md) |建议开启 SQL 数据库服务器的审核与威胁检测。 （仅限 SQL 数据库服务。 不包括在虚拟机上运行的 Microsoft SQL 服务器。） |
| [在 SQL 数据库上启用审核与威胁检测](security-center-enable-auditing-on-sql-databases.md) |建议在 SQL 数据库数据库上开启审核与威胁检测。 （仅限 SQL 数据库服务。 不包括在虚拟机上运行的 Microsoft SQL 服务器。） |
| [启用透明数据加密](security-center-enable-transparent-data-encryption.md) |建议启用 SQL 数据库加密。 （仅限 SQL 数据库服务。） |

若要查看 Azure 资源的建议，请选择“安全中心”边栏选项卡上的“**建议**”磁贴。 在“**建议**”边栏选项卡上选择某个建议，以查看详细信息。 在此示例中，我们选择**在 SQL 服务器上启用审核与威胁检测**。

![建议][4]

如下所示，安全中心将显示未启用审核与威胁检测的 SQL 服务器。 启用审核后，可以配置威胁检测设置和电子邮件设置，以接收安全警报。 威胁检测会在检测到异常的数据库活动时向你发出警报，指出数据库有潜在的安全威胁。 警报在安全中心仪表板中显示。
![审核和威胁检测][5]

请按照 [SQL 数据库威胁检测入门](../sql-database/sql-database-threat-detection-get-started.md)中的步骤启用和配置威胁检测，并配置在检测到异常活动时接收安全警报的电子邮件列表。

若要详细了解各项建议，请参阅[管理安全建议](security-center-recommendations.md)。

## <a name="monitor-security-health"></a>监视安全运行状况
用户为订阅的资源启用[安全策略](security-center-policies.md)以后，安全中心将分析相关资源的安全性，确定可能的漏洞。  在“**资源安全运行状况**”磁贴中，可以查看资源的安全状态。 单击“**资源安全运行状况**”磁贴中的“**数据**”即可打开“**数据资源**”边栏选项卡，其中包含对各种问题（例如：审核以及透明数据加密未启用）的 SQL 建议。 此外还有针对数据库常规运行状况的建议。
![资源安全运行状况][6]

若要了解详细信息，请参阅[安全运行状况监视](security-center-monitoring.md)。

## <a name="manage-and-respond-to-security-alerts"></a>管理和响应安全警报
安全中心自动收集、分析和集成来自 [Azure SQL 威胁检测](../sql-database/sql-database-threat-detection-get-started.md)的日志数据，以及其他 Azure 资源，以检测真实威胁和减少误报。 安全中心显示了一系列安全警报（按严重程度排序），并显示了快速调查问题所需的信息以及修复攻击的建议。

若要查看警报，请选择“安全中心”边栏选项卡上的“**安全警报**”磁贴。 在“**安全警报**”边栏选项卡上，选择某个安全警报，了解触发该警报的事件详细信息以及修复攻击所需的步骤（如有）。 在此示例中，选择“**潜在 SQL 注入**”。
![安全警报][7]

如下所示，安全中心提供额外的详细信息，以深入了解是什么触发了该警报、其目标资源、源 IP 地址（如果适用）以及修复建议。
![潜在 SQL 注入][8]

有关详细信息，请参阅[管理和响应安全警报](security-center-managing-and-responding-alerts.md)。

## <a name="next-steps"></a>后续步骤
* [安全中心常见问题](security-center-faq.md) - 查找有关如何使用服务的常见问题。
* [安全中心规划和操作指南](security-center-planning-and-operations-guide.md) - 通过一系列步骤和任务，根据组织的安全要求和云管理模型优化安全中心的使用。
* [安全中心数据安全](security-center-data-security.md) - 了解安全中心如何收集和处理有关 Azure 资源的数据，包括配置信息、元数据、事件日志、故障转储文件等。
* [处理安全事件](security-center-incident.md) - 了解如何在安全中心使用安全警报功能，帮助你处理安全事件。

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png



<!--HONumber=Dec16_HO3-->


