---
title: "Azure SQL 数据库 Azure 案例研究 - Snelstart | Microsoft 文档"
description: "了解 SnelStart 如何使用 SQL 数据库以每月 1,000 个新 Azure SQL 数据库的速度快速扩展其业务服务"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: fab506b2-439d-4f1a-bdc5-d1d25c80d267
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 3861814974b95f0c65879158cb02ec727a25c99f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>借助 Azure，SnelStart 以每月 1,000 个新 Azure SQL 数据库的速度快速扩展其业务服务
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart 为荷兰的中小企业 (SMB) 编写流行的财务和业务管理软件。 它的 55,000 家客户由 110 名员工提供服务，其中包括 35 名 IT 人员。 通过从桌面软件转移到 Azure 上的服务型软件 (SaaS) 产品，SnelStart 不仅能够使用 C# 中的熟悉环境将管理自动化，而且还能使用弹性池避免过度预配或预配不足，优化性能与伸缩性，从而充分利用内置服务。 Azure 使得 SnelStart 能够在本地环境与云之间灵活移动客户。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>为何 SnelStart 将服务从桌面扩展到云中
> “使用 Azure 意味着可以缩短软件交付时间、快速响应客户需求，在需求增大时缩放解决方案。”
> 
> — Henry Been，软件架构师
> 
> 

SnelStart 使用以下传统开发模型成功经营软件业务多年：编写代码、打包、交付、重复。 随着时间的推移，变革的步伐越来越快。 法规经常变动，而客户需要更简单的方式来处理财务记录，与会计师和政府合作才能跟上这些变化。

“将软件交付给客户的成本很高且限制很多，”软件架构师 Henry Been 表示。 “生产、打包和交付成本限制了我们发布软件的频率。 我们必须为定期交付件打包更新，但这样很难满足客户不断变化的需求。 我们永远无法确保客户升级到最新的产品版本。 我们必须支持多个版本，因此很难做好支持工作。”

Been 补充道，“我们想要一种能够以更快的步伐安排和发布更新的方法，以便能够以更快的速度创新并为客户创建新服务。 我们还想要一个能够将更多过程自动化的方法，以便简化客户的业务管理需求。”

对 SnelStart 而言，解决方案就是通过成为基于云的 SaaS 提供程序来扩展其服务。 Azure SQL 数据库平台帮助 SnelStart 实现了该目标，且没有产生基础结构即服务 (IaaS) 解决方案往往会产生的重大 IT 开销。

云模型还使 SnelStart 能够快速修复 bug 以及提供新功能，而不需要客户下载和升级软件。 Beeen 表示，“使用 Azure 云服务使我们能够快速应对来自第三方的多变要求。 我们无需将新版本交付给数千个客户，而可以将发送自桌面应用程序的信息改编为第三方要求的新格式。”

## <a name="a-modern-company-with-traditional-roots"></a>具有传统渊源的现代化公司
SnelStart 是一家现代化的敏捷型高科技企业，其渊源可追溯到 1964 年，其创始人当时是要将这家公司打造为乐器零件制造商。 SnelStart 软件的核心事业实际上在二十世纪八十年代（也就是个人计算机开始普及的年代）才开始起步。 该公司需要一个能够替代当年会计软件的更好方案，因此它将这些任务揽在了自己手中。 1982 年，该公司建立了后来 SnelStart 会计软件的基础。 一开始，该软件因其简单性和处理速度而受到青睐，该公司最终改变了重心，将自己重新定位为一家软件公司。

1995 年，SnelStart 发布了它的第一款适用于 Windows 的簿记应用程序。 该应用程序是在 Microsoft Visual Basic 1.0 和 Microsoft Access 数据库基础上构建的应用程序，可帮助将客户群体扩大到超过 5,000 个用户。

现今，SnelStart 是主要的业务线 (LOB) 及业务管理应用程序提供商，目标用户是荷兰的中小企业和创业者。 IT 架构师 Carlo Kuip 说道，“我们的目标是要为客户提供 100% 的业务管理服务自动化。”

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>通过弹性池优化性能和成本
SnelStart 是弹性池的早期大规模采用者。 弹性池可帮助该公司控制成本，更有效地管理性能要求。 Been 表示，“使用弹性池，我们可以根据客户的需求优化性能，而不会发生过度预配的情况。 如果我们必须根据高峰负载预配，费用将相当高昂。 在多个低使用量数据库之间共享资源的做法，使我们能够创建既能顺畅运行又能符合成本效益的解决方案。”

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Azure SQL 数据库帮助容器化数据，实现隔离和安全性
Azure SQL 数据库使得 SnelStart 能够以轻松通透的方式将客户的本地业务管理数据转移到 Azure。 Azure SQL 数据库是一个便利的容器，提供隔离、身份验证和授权边界，以及轻松备份与还原功能。 数据库为业务管理提供了一个适当的概念模型。 IT 架构师 Carlo Kuip 表示，“此容器边界内的项目包含对企业到关重要的敏感数据，将这些项目存储在隔离的数据库中可为它们提供严密的防护。 我们可以在数据库级别管理授权，甚至将管理自动化以及横向扩展这些数据库，而无需雇用数据库管理员 (DBA)。”

Azure SQL 数据仓库在 SnelStart 安全与管理方案中帮助公司收集遥测数据（例如入侵检测、用户活动记录和连接），因此也扮演着相当重要的角色。

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure 消除了开销，使开发人员可以更专注于创造价值
Azure 平台模型消除了基础结构开销，使 SnelStart 能够使用 C# 管理库将部署自动化。 Kuip 指出，“我们能够以很少的人员来拓展当前的运营，同时能为客户增强伸缩性、速度及灾难恢复选项。 过渡到服务开发释放了资源，让我们专注于新服务和功能，而不只是更新现有的代码来跟上新法规或税务条例的要求。” 他补充道，“通过将管理自动化和使用 SaaS 产品，我们能够为客户提供更大价值，无需在操作人员上大量投资。” 例如，使用 Azure 和弹性池，SnelStart 可以添加各种新功能，包括与银行的更健全客户数据集成、新帐务服务、小型企业背景检查，以及电子邮件服务。

> “仅仅是 2016 年的头几个月，我们便将 Azure SQL 数据库部署从大约 5,500 个扩展到超过 12,000 个，而我们目前正以每月约 1,000 个数据库的速度持续增加。”
> 
> — Henry Been，软件架构师
> 
> 

使用弹性作业功能可将数据库管理进一步自动化。 Kuip 说道，“非常感谢 SQL 数据库 [服务器] 实例上的数据库自动发现功能。” 此功能可让 SnelStart 对其动态成长的客户群体执行管理操作，且不会产生额外的开销。

此外，SnelStart 正在开发 API，作为客户数据与第三方软件合作伙伴所构建的应用程序之间的代理。 Kuip 指出，“此 API 可让其他供应商将功能添加到我们的软件中，例如消除发票和其他文档的数据输入。” 客户不再需要在小型企业会计软件中键入发票；SnelStart 软件将直接交换所需的信息。 因此，客户可将其业务管理任务与从行业中数字转换生成的信息生态系统相结合。  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>Azure 服务如何为 SnelStart 启用 SaaS
使用 Azure，SnelStart 可以在云中以无缝的方式为其客户及会计师提供服务。 举例而言，客户和会计师可以直接访问托管在 Azure 中的 SnelStart 客户端 API 来共享信息。 Kuip 指出，“这些可重复使用的服务提供给面向客户的 Web 应用，它们提供通用基础结构和功能来允许访问客户的业务管理系统，以及访问客户使用的第三方软件。 例子包括提供产品配置功能、管理防火墙规则，以及管理长时间运行的进程，如备份。”

> “我们的目标是要为客户提供 100% 的业务管理服务自动化。” 
> 
> — Carlo Kuip，IT 架构师
> 
> 

此外，SnelStart Web 服务还使得客户和会计师能够轻松访问 Azure SQL 数据库弹性池中的数据。 此 SaaS 模型结合数据库弹性与 Azure Resource Manager，可为 SnelStart 提供伸缩性功能来为每项 Azure 部署提供辅助。 项目实施可以使用 C# 管理库完全自动化。

![SnelStart 体系结构](./media/sql-database-implementation-snelstart/figure1.png)

图 1. 截至 2016 年 6 月，SnelStart 拥有超过 11,000 个数据库，以及超过 50 个弹性池

## <a name="simplicity-from-the-cloud"></a>云的简便性
自从转移到基于 Azure 云的解决方案之后，SnelStart 能够为快速增长的客户提供支持，同时提供创新的功能和服务。 Been 指出，“通过 Azure，我们可以为客户提供几乎持续不断的更新，而无需扩大操作人员编制。 我们顺便还获得了其他所有的优异 Azure 功能，例如伸缩性和灾难恢复。”

> “当我们远在雷德蒙德时... 荷兰一位开发人员打来电话，告诉我一个具体的问题。 然后，我们让 Microsoft 在 48 小时内在客户生产环境中提供了一项更改，从而解决了问题。”
> 
> — Henry Been，软件架构师
> 
> 

SnelStart 也很赞赏与 Microsoft Azure SQL 数据库团队发展出的密切合作关系。 Kuip 指出，“我们在功能及如何使用技术方面展开了讨论，双方都有点惺惺相惜。”
SnelStart 当前的目标是不断拓展客户群体并使他们感到满意。 Been 说道，“消除了过去作为 ISV 的技术与资源限制，我们的发展便没有束缚。”

## <a name="more-information"></a>详细信息
* 有关 Azure 弹性池的详细信息，请参阅[弹性池](sql-database-elastic-pool.md)。
* 有关 Web 角色和辅助角色的详细信息，请参阅[辅助角色](../fundamentals-introduction-to-azure.md#compute)。    
* 有关 Azure SQL 数据仓库的详细信息，请参阅 [SQL 数据仓库](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* 有关 SnelStart 的详细信息，请参阅 [SnelStart](http://www.snelstart.nl)。

