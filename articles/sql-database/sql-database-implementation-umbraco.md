---
title: "Azure SQL 数据库 Azure 案例研究 - Umbraco | Microsoft 文档"
description: "了解 Umbraco 如何使用 SQL 数据库为云中数千个租户快速预配和缩放服务"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5243d31e-3241-4cb0-9470-ad488ff28572
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: c25a66daa87da96d4e77c9021a1ceb4366d7a224
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco 使用 Azure SQL 数据库为云中数千个租户快速预配和缩放服务
![Umbraco 徽标](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco 是一个流行的开源内容管理系统 (CMS)，从小型营销活动或产品目录站点，到适用于财富 500 强公司以及全球媒体网站的复杂应用程序，全部都能运行。 

> “我们有一个相当大的开发人员社区在使用该系统，论坛中的 100,000 多名开发人员和 350,000 个直播站点都在运行 Umbraco。”
> 
> — Morten Christensen，Umbraco 技术主管
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

为了简化客户部署，Umbraco 添加了 Umbraco 即服务 (UaaS)：一个软件即服务 (SaaS) 产品，它无需本地部署，提供内置缩放，使开发人员能够专注于产品创新而不是解决方案管理，节省了管理开销。 Umbraco 依赖于 Microsoft Azure 提供的弹性平台即服务 (PaaS) 模型提供所有这些优势。

UaaS 使 SaaS 客户能够使用他们以前未曾接触过的 Umbraco CMS 功能。 为这些客户预配了一个包含生产数据库的有效 CMS 环境。 客户可以根据要求，最多添加两个额外的数据库用于开发和过渡环境。 请求新环境时，自动化过程自动为该客户分配数据库。 新数据库在几秒内就能准备就绪，因为 Umbraco 事先已从 Azure 的可用数据库弹性池预配了数据库（请参阅图 1）。

![Umbraco 预配生命周期](./media/sql-database-implementation-umbraco/figure1.png)

图 1. Umbraco 即服务 (UaaS) 的预配生命周期

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Azure 弹性池和自动化简化了部署
使用 Azure SQL 数据库和其他 Azure 服务，Umbraco 客户可自行预配其环境，Umbraco 可以通过直观的工作流轻松监视和管理数据库：

1. 预配
   
   Umbraco 维护 200 个可用数据库的容量，这些数据库是弹性池中预先预配的数据库。 当有新客户注册 UaaS 时，Umbraco 通过从可用性池向该客户分配数据库，以近乎实时的方式为客户提供新的 CMS 环境。
   
   当可用性池达到其阈值时，系统将创建新弹性池，并预先预配新数据库，以便根据需要分配给客户。
   
   项目实施可以使用 C# 管理库和 Azure 服务总线队列完全自动化。
2. 利用
   
   客户使用一到三个环境（用于生产、过渡和/或开发），每个环境都有自身的数据库。 客户数据库位于弹性池中，使得 Umbraco 无需过度预配即可提供有效的缩放。
   
   ![Umbraco 项目概览](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Umbraco 项目详细信息](./media/sql-database-implementation-umbraco/figure3.png)
   
   图 2。 显示项目概览和详细信息的 Umbraco 即服务 (UaaS) 客户网站
   
   Azure SQL 数据库使用数据库事务单位 (DTU) 来代表真实世界数据库事务所需的相对能力。 对于 UaaS 客户而言，数据库通常以大约 10 个 DTU 运行，但每个数据库都可以按需求弹性缩放。 这意味着，UaaS 可以确保客户始终拥有必要的资源，甚至是在高峰时间也是如此。 例如，在最近某个星期日的夜间体育赛事期间，一个 UaaS 客户的数据库在比赛的持续时间经历了高达 100 个 DTU 的高峰。 Azure 弹性池使得 Umbraco 既能支持这种高需求，同时又不会导致性能降低。
3. 监视
   
   Umbraco 使用 Azure 门户中的仪表板以及自定义电子邮件警报来监视数据库活动。
4. 灾难恢复
   
   Azure 提供两个灾难恢复 (DR) 选项：活动异地复制和异地还原。 公司应该选择的 DR 选项取决于其[业务连续性目标](sql-database-business-continuity.md)。
   
   活动异地复制可在发生停机时提供最快级别的响应。 通过活动异地复制，可以在不同区域的服务器上最多创建四个可读的辅助数据库，并可以在发生故障时故障转移到其中任何一个辅助数据库。
   
   Umbraco 不需要异地复制，但它利用了 Azure 异地还原来帮助确保发生服务中断时会停机时间缩到最短。 异地还原依赖于异地冗余 Azure 存储中的数据库备份。 这样，用户便可以在主要区域发生中断时，从备份副本还原。
5. 取消预配
   
   删除项目环境时，会在 Azure 服务总线队列清理期间删除所有关联的数据库（开发、过渡或实时）。 此自动化过程将未使用的数据库还原到 Umbraco 的弹性数据库可用性池，这样既可充分利用这些数据库，又可将这些数据库用于将来的预配。

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>弹性池可让 UaaS 轻松缩放
利用 Azure 弹性池，Umbraco 可为其客户优化性能，无需担心过度预配或预配不足。 Umbraco 目前拥有 3,000 个遍布在 19 个弹性池中的数据库，无论是现有 325,000 个客户中的任何一个客户，还是已准备好要在云中部署 CMS 的新客户，Umbraco 都能根据需要轻松缩放，满足客户的需求。

Umbraco 技术主管 Morten Christensen 指出，“UaaS 现在大约以每天 30 个新客户的速度成长。 我们的客户很高兴能够在几秒内就能方便地预配好新项目、使用‘单键部署’功能从开发环境立即将更新发布到直播站点，以同样快速的方式在发现错误时予以更改。”

如果客户不再需要第二个和/或第三个环境，只需删除这些环境即可。 这样会释放资源，构成 Umbraco 弹性数据库可用性池的一部分，供其他客户使用。

![Umbraco 部署体系结构](./media/sql-database-implementation-umbraco/figure4.png)

图 3。 Microsoft Azure 上的 UaaS 部署体系结构

## <a name="the-path-from-datacenter-to-cloud"></a>从数据中心到云的路径
当 Umbraco 开发人员一开始确定转移到 SaaS 模型时，即已知道需要使用一种符合成本效益且可缩放的方式来构建服务。

> “弹性池是最适合我们 SaaS 产品的选项，因为我们可以根据需要调大和调小容量。 预配相当简单，再配合我们的设置，可以发挥它的最大作用。”
> 
> — Morten Christensen，Umbraco 技术主管
> 
> 

“我们想要把时间花在解决客户的问题上，而不是花在管理基础结构上。 我们想要让客户轻轻松松就能够获取最大价值，”Umbraco 创始人 Niels Hartvig 说道。 “一开始，我们考虑过自行托管服务器，但容量规划是一个梦魇。” 碰巧的是，Umbraco 并未雇用任何数据库管理员，这就突显了 UaaS 的重要价值主张。

Umbraco 开发人员的重要目标之一是，为 UaaS 客户提供一种快速且没有容量限制的环境预配方式。 但是在 Umbraco 数据中心内提供专用的托管服务需要大量的额外容量来应对处理需求喷发。 这意味着要添加大量一般不会充分利用的计算基础结构。

此外，Umbraco 开发团队想要一个能让他们尽量重复使用现有代码的解决方案。 Umbraco 开发人员 Mikkel Madsen 指出，“我们很乐意使用我们非常熟悉的 Microsoft 开发工具，例如 Microsoft SQL Server、Microsoft Azure SQL 数据库、ASP.net 和 Internet Information Services (IIS)。 在投资 IaaS 或 PaaS 云解决方案之前，我们希望确保它可以支持 Microsoft 工具与平台，这样我们才不需要对基础代码进行大规模更改。”

为了符合所有标准，Umbraco 曾经寻找过符合以下资格的云合作伙伴：

* 有足够的容量与可靠性
* 支持 Microsoft 开发工具，使 Umbraco 工程师无需从头重建其开发环境
* 在 UaaS 参与竞争的所有地理市场中都有运营点（企业需要确保它们可以快速访问其数据，并且其数据存储在符合其区域法规要求的位置）

## <a name="why-umbraco-chose-azure-for-uaas"></a>Umbraco 为何选择将 Azure 用于 UaaS
Morten Christensen 指出，“在考虑所有选项之后，我们选择了 Azure，因为它符合我们从管理性、伸缩性再到熟悉度及符合成本效益方面的所有标准。 我们在 Azure VM 上设置环境，每个环境都有自身的 Azure SQL 数据库实例，而所有实例都在弹性池中。 通过将开发、过渡与实时环境之间的数据库隔离，我们可以为客户提供与规模匹配的强大性能隔离，这是一个极大的优势。”

Morten 补充道，“以前，我们必须手动预配 Web 数据库的服务器。 现在，我们无需考虑这项工作。 从预配到清理的所有操作都是自动化的。”

Morten 也很满意 Azure 提供的缩放功能。 “弹性池是最适合我们 SaaS 产品的选项，因为我们可以根据需要调大和调小容量。 预配相当简单，再配合我们的设置，可以发挥它的最大作用。” Morten 表示，“弹性池的简单性，再加上基于服务层的 DTU 保证，使我们能够根据需要预配新资源池。 最近，我们一个较大客户的实时环境高峰达到 100 个 DTU。 使用 Azure，弹性池为客户的数据库提供了所需的实时资源，而无需预测 DTU 需求。 简单地说，我们的客户达到了预期的周转时间，而我们则达到了性能服务级别协议的要求。”

Mikkel Madsen 总结，“除了将 Azure 服务总线与 Azure SQL 数据库配合使用的基础技术以外，我们还采用了强大的 Azure 算法，将常见的 SaaS 方案（大规模实时加入新客户）与应用程序模式（预先预配开发数据库和实时数据库）相连接。”

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>使用 Azure 后，UaaS 的表现超出客户期望
自从选择 Azure 作为云合作伙伴后，Umbraco 无需像自我托管解决方案那样需要投资 IT 资源，就能够为 UaaS 客户提供优化的内容管理性能。 就像 Morten 所说，“我们相当满意 Azure 为开发人员提供的便利性和伸缩性，我们的客户也为获得的功能和可靠性振奋不已。 整体而言，它是我们的一大收获！”

## <a name="more-information"></a>详细信息
* 有关 Azure 弹性池的详细信息，请参阅[弹性池](sql-database-elastic-pool.md)。
* 有关 Azure 服务总线的详细信息，请参阅 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)。
* 有关虚拟网络的详细信息，请参阅[虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)。    
* 有关备份与恢复的详细信息，请参阅[业务连续性](sql-database-business-continuity.md)。    
* 有关监视池的详细信息，请参阅[监视池](sql-database-elastic-pool-manage-portal.md)。    
* 有关 Umbraco 的详细信息，请参阅 [Umbraco](https://umbraco.com/cloud)。

