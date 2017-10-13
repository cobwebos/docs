---
title: "Azure SQL 数据库 Azure 案例研究-Daxko/CSI | Microsoft 文档"
description: "了解 Daxko/CSI 如何使用 SQL 数据库加速其开发周期和增强其客户服务与性能"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 3e43494721f03ce8e56cb069638ad24df7222386
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI 使用 Azure 来加速其开发周期和增强其客户服务与性能
![Daxko/CSI 徽标](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI Software 面临的挑战：归功于其全方位企业软件解决方案的成功，健身与休闲中心的客户群体快速发展，但 IT 基础结构要跟上不断发展的客户群体，给该公司的 IT 人员带来了考验。 该公司逐渐受到运营开销不断提高的约束，特别是在管理日渐庞大的数据库方面。 更糟的是，这些运营开销已占用新举措（例如公司软件的新移动功能）的部分开发资源。

Daxko/CSI 产品开发主任 David Molina 指出，Azure 为 CSI Software 提供了所需的平台即服务 (PaaS) 模型，它可以简化数据库管理、提高伸缩性以及释放资源，让客户专注于软件，而不是运营事务。 “Azure SQL 数据库是很适合我们的选择。 不需要操心维护 SQL Server、故障转移群集，以及所有其他基础结构需求，对我们而言相当理想。”

自从迁移到 Azure 后，CSI Software 只需要两位操作人员来管理 600 多个客户数据库。 该公司使用 Azure SQL 数据库弹性池，根据大小和需求来移动客户数据库。

Molina 补充道，“我们的客户立即感受到改变。 在使用弹性池之前，他们在高峰期有时会遇到超时和其他问题。 使用 Azure 弹性池后，他们可以根据需要激增用量，使用软件时也不会出现任何问题。”

除了改善客户的性能之外，Azure 弹性池还释放了 CSI Software 资源，使他们可以专注于开发新服务和功能，而不是应对运营和管理。 这些 IT 资源已帮助 CSI Software 改进其企业软件产品 SpectrumNG，提高健身房会员的参与度，提升员工效率，使员工和会员能够通过移动设备访问交互式任务和实时通知。

此外，Azure 还通过启用自动化选项，帮助 CSI Software 加速和改进开发与质量保证 (QA) 周期。 该公司实施 Azure 后，构建管理人员只需单击一下按钮，就能将组件打包。 如 Molina 所述，“现在，QA 能够在发布周期内部署到 Azure 中的测试环境，精确模拟生产堆栈。 我们可以将内部版本立即部署到开发环境以检查更改。 这对我们而言是一大优点，因为之前我们没有类似的环境可用于测试。”

## <a name="offloading-to-the-cloud"></a>卸载到云
在转移到云之前，CSI Software 已在其休斯敦当地的数据中心成功创建自己的多租户基础结构。 随着公司的扩张，它在采购、预配和维护用来支持其客户所需的所有软硬件方面，面临着越来越多的难题。 增加 IT 人员来处理运营成为另一个瓶颈，导致拖慢预配新资源以及向客户推出新服务的速度。

为了消除这种开销，CSI Software 研究了云选项，以便能够将重心放在代码上而不是运营上。 该公司发现许多顶尖的云提供商都只提供基础结构即服务 (IaaS) 解决方案，而这种解决方案仍然需要大量 IT 人员来管理 IaaS 堆栈。 最后，CSI Software 认定 Azure PaaS 解决方案是最符合其需求的选项。 Molina 解释道，“Azure 为我们排除了硬件和系统软件的障碍，使我们可以专注于软件产品，同时也减少了 IT 开销。”

## <a name="making-the-transition-to-azure"></a>过渡到 Azure
在选择 Azure 作为 PaaS 解决方案之后，CSI Software 开始将其后端基础结构和数据库迁移到云中。 在使用 Azure 之前，SpectrumNG 客户必须安装客户端应用程序才能与后端中的 Windows Communication Foundation (WCF) 服务通信。 Molina 表示，“尽管有些客户将所有项目都托管在自己的数据中心内，但我们是将产品构建成多租户产品。 我们使用 SQL Server 作为数据存储，将所有内容都托管在休斯敦的数据中心内。

“我们的产品还包含一个面向会员的门户（以 ASP.net 编写），旨在以白标签方式匹配客户的 Web 空间；另外还包含一个 SOAP API，用于支持在线网页和任何第三方集成。”

将体系结构迁移到云并未花费很长时间。 Molina 表示，“大部分精力花费在修改读取配置文件信息的方式、集中化连接字符串修改，以及自动化打包、上传和部署版本上。”

为了开发构建自动化，CSI Software 工程师使用了 Azure PowerShell 和 REST API 来创建包并将其上传到过渡环境以便在每天晚上发布。
过渡到基于 Azure 云的部署的整个过程对 CSI Software IT 团队而言相当快速顺利。 Molina 解释道，“总而言之，我们在承接项目后的三到四周内，云中就有了一个测试环境。 这种收获让我们感到意外。”

在配置并测试环境之后，CSI Software 便开始迁移客户。 对于已在使用 CSI Software 托管服务的客户，这种过渡几乎是无缝的。 对于从本地部署迁移的客户，迁移到云需要多花一些时间，但就客户和 CSI Software 而言，基本上也没有造成太大的困扰。

针对新客户，CSI Software 的 IT 人员使用以下过程将他们加入 Azure：

1. 使用 Azure PowerShell 脚本为客户启动新数据库；所有客户都是从高级层开始，确保有足够的初始吞吐量来实现过渡。
2. 在可能的情况下，CSI Software 使用 Azure SQL 迁移向导将现有数据转移到 Azure SQL 数据库实例。
3. 最后，使用 Microsoft SQL Server Integration Services (SSIS) 来协调数据中任何不一致的情况，或根据需要执行任何数据清理。

如今，大约 99% 的 CSI Software 客户都托管在 Azure 中，跨越四个区域数据中心（中北部、中南部、东部和西部）。 由于每个客户所在的地理区域都有数据中心，延迟降到了最低。

## <a name="azure-elastic-pools-free-up-it-resources"></a>Azure 弹性池释放 IT 资源
Azure 的多个功能已帮助 CSI Software 从基础结构和运营为焦点，转化为以功能和开发为焦点。 最大的收益或许来自弹性池。

CSI Software 目前为客户提供大约 550 个数据库。 在使用弹性池之前，很难在一个分层结构内管理这么多数据库。 运营经理必须根据客户的高峰需求分配性能层，造成了严重的 IT 资源开销。 通过弹性池，经理可以根据情况为租户分配高级或标准池，并根据大小和需求移动客户。 客户几乎立即可以感受到弹性池的作用；在使用弹性池之前，客户在高峰使用期间遇到了超时和其他问题，但使用弹性池之后，客户可以根据需要体验活动喷发情况，并继续使用 SpectrumNG 而不会出现任何问题。

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure 活动异地复制加速报告
有多个 CSI Software 客户还利用了 Azure 活动异地复制。 使用活动异地复制时，可在相同或不同的数据中心区域中最多配置 4 个可读的辅助数据库。 CSI Software 以两种方式使用活动异地复制：首先，在数据中心中断服务或在无法连接到主数据库时可以使用辅助数据库；其次，辅助数据库是可读取的，并且可用于卸载只读工作负荷，例如报告作业。 某些 CSI Software 客户使用这种优点来加速报告工作流。

## <a name="csi-software-application-logic-and-architecture"></a>CSI Software 应用程序逻辑与体系结构
SpectrumNG 使用 Web 角色。 由于应用程序是多租户的，因此使用了 WCF 服务处理来自客户的初始连接请求。 Molina 指出，“请求识别每个客户，使我们能够构建出一个可连到其数据库的连接字符串，执行所需的任何操作。”

针对此服务的 Web 层，CSI Software 利用了基于日期与时间的 Azure 自动缩放。 根据每个区域数据中心的时区，在营业时间内，可用资源会自动增加以配合较高的使用量。 此外，资源设置为在周末客户需求降低时进行缩减。

![Daxko/CSI 体系结构](./media/sql-database-implementation-daxko/figure1.png)

图 1. 云服务辅助角色从 Azure SQL 数据库中抽取结构化数据，从表存储中抽取半结构化数据。 SpectrumNG 用户通过云服务 Web 角色与该数据交互。

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>将 Web 应用和 Web 计划层用于移动应用
使用 Azure SQL 数据库释放资源使 CSI Software 能够实现新的举措，包括基于 Azure Web 应用中托管的自定义 API 设计完整的移动平台。 该平台可让健身房会员与员工使用移动设备来查看计划、预约课程和接收消息。

该平台使用面向服务的体系结构 (SOA) 融入单个组件（例如销售网点系统 (POS) 或销售系统），快速将它转移到另一个 Web 计划，然后启动服务来支持该组件，同时让所有其他项目保留在原有的 Web 计划中。 此功能为 CSI Software 提供了极大的灵活性，并且有助于削减成本。

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure 使 CSI Software 开发人员可以专注于应用和服务
Azure SQL 数据库不仅为 SpectrumNG 客户带来了好处，让他们享有快速、可靠的服务，同时也为 CSI Software 的 IT 人员和开发人员带来了丰硕的成果。 通过将运营任务卸载到云中的 Azure，CSI Software 不仅降低了其资源和基础结构的开销、大大加速了开发周期，而且不再需要对数据库进行微观管理，以便优化租户的性能。

## <a name="more-information"></a>详细信息
* 有关 Azure 弹性池的详细信息，请参阅[弹性池](sql-database-elastic-pool.md)。
* 有关数据库工具和弹性缩放的详细信息，请参阅[弹性数据库工具和弹性缩放](sql-database-elastic-scale-get-started.md)。
* 有关迁移 SQL Server 数据库的详细信息，请参阅[将 Azure SQL 数据库迁移到 Azure](sql-database-cloud-migrate.md)。
* 若要深入了解活动异地复制，请参阅[活动异地复制](sql-database-geo-replication-overview.md)。
* 有关 Web 角色和辅助角色的详细信息，请参阅[辅助角色](../fundamentals-introduction-to-azure.md#compute)。    
* 有关 Azure 服务总线的详细信息，请参阅 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)。
* 有关自动缩放的详细信息，请参阅[缩放云服务](../cloud-services/cloud-services-how-to-scale.md)。

