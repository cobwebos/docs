---
title: "Azure SQL 数据库 Azure 案例研究 - GEP | Microsoft 文档"
description: "了解 GEP 如何使用 SQL 数据库扩大全球客户群体，实现效率提升"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ae8bcb10-c251-4bac-b666-10a253918583
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 2d77e95a4e99679686839959bf6330f1e29f870b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure 实现 GEP 全球覆盖并提高效率
![GEP 徽标](./media/sql-database-implementation-gep/geplogo.png)

GEP 提供软件和服务，可让世界各地的采购领导充分影响其业务运营、策略及财务绩效。 除了咨询和托管的服务，该公司还提供 SMART by GEP® 基于云的全方位采购软件平台。 但是，GEP 在尝试使用自己的本地数据中心来支持 SMART by GEP 时遇到一些限制：所需的投资过高，而其他国家/地区的法规要求则令人在必要的投资上有所顾虑。 通过转移到云，GEP 释放了 IT 资源，可以节省 IT 营运的精力，更专注于为全球客户开发新的价值来源。

## <a name="expanding-services-and-growth-by-using-azure"></a>使用 Azure 扩展服务范围，提高增长幅度
SMART by GEP 客户喜欢此平台的功能和易用性；客户可以随时随地通过任何设备（笔记本电脑、平板电脑或手机）管理他们的流程。 通过转移到 Microsoft Azure，GEP 已经能够应对其快速增长及扩展到新市场的可能性。 GEP 技术副总裁 Dhananjay Nagalkar 表示，“Microsoft Azure 不仅让我们能够快速灵活地缩放服务，还提供区域数据中心帮助我们符合全球客户的法规要求，因此在 GEP 的成功上扮演了关键角色。”

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>自备数据中心的限制
2013 年，GEP 领导人认识到他们需要通过某种方式来确保在发展客户群体时能够保持可缩放性与性能。 Nagalkar 解释，“若要以现有的数据中心满足该需求，我们必须大幅扩展基础结构和 IT 资源。 此投资与时间的耗费都相当庞大。” 本地的物理机和虚拟机需要大量的配置、管理、缩放、备份及修补，其花费对 GEP 而言成本过高。 另一方面，云解决方案则提供简单性和便利性，使 GEP 能够更专注于开发而不是管理越来越复杂的 IT 运营。 Nagalkar 知道 GEP 可以通过迁移到云来降低其基础结构的采购、配置和管理开销。

GEP 还需要通过某种方式克服它无法进入部分全球市场的法规障碍。 对于 GEP 的许多潜在欧洲客户而言，法规遵循要求他们必须将数据存储在当地的地理区域。 但对于 GEP 而言，构建多个数据中心的做法并不切实际。 “广泛的基础结构投资和 IT 人力成本严重影响利润”，Nagalkar 说。 “因此，我们实际上被迫放弃需要当地存储数据的潜在客户。”

Nagalkar 知道云解决方案可能是此窘境的解答。 如果 GEP 能够转移到在全球都有运营网点的云提供商，就能将数据存储在更靠近客户的实际位置，更加能够符合客户的法规和性能要求。

## <a name="finding-smooth-skies-in-the-cloud"></a>在云中自由翱翔
Nagalkar 及其团队调查了多个云选项，但大多数都是基于基础结构即服务 (IaaS) 的解决方案，需要 GEP 大量投入 IT 资源来配置和管理服务。 最后证明 Azure 平台即服务 (PaaS) 解决方案是更合适的选项。

“使用 Azure，GEP 就不需要处理数据库管理、虚拟机配置、修补或其他基础结构管理任务”，Nagalkar 说。 “取而代之的是，我们可以集中资源来发挥所长：使用采购方面的专长来编写真正为客户带来成效的软件。” 

事实上，转移到 Azure 后，GEP 既可以缩减其 IT 操作人员的规模，同时也能为客户提供更完美的功能。

使用全球各地的 Azure 数据中心，GEP 可以轻松将触角扩展到欧洲和亚洲。 这些全球数据中心除了让 GEP 能够灵活缩放以外，还能符合客户的当地存储数据要求，在减少延迟的同时可以满足法规要求。

## <a name="smart-by-gep-architecture"></a>SMART by GEP 体系结构
GEP 在 Azure 上从头开始构建了 SMART by GEP。 GEP 的关键设计动机是，与 GEP 在本地环境所能实现的效果相比，使用 Azure SQL 数据库可以体验到更好的可缩放性、更短的停机时间以及更低的维护成本。 但是，在转移到云之后，GEP 在云中发现了能以更佳方式响应客户要求的新开发机会（例如快速的原型设计和精简工程）。 在 Azure 中开发可让 GEP 的开发人员摆脱在本地环境中可能需要面对的软件授权问题。 SMART by GEP 的核心是 Azure SQL 数据库，不过 GEP 也使用了其他许多 Azure 服务来轻松快速地持续改进 SMART by GEP。

![SMART by GEP 体系结构](./media/sql-database-implementation-gep/figure1.png)图 1。 SMART by GEP 体系结构

## <a name="structured-data"></a>结构化数据
SMART by GEP 应用程序的核心是为企业采购管理解决方案提供技术支持的 Azure SQL 数据库实例。 GEP 设计 SMART by GEP 时，便已了解 Azure SQL 数据库是其体系结构的最合适选项，既可让公司实现最高程度的数据保护，又能符合法规要求。 GEP 使用 Azure SQL 数据库提供的多个数据保护层，包括：

* 通过透明数据加密来加密静态数据。
* 将 Azure SQL 数据库与 Azure Active Directory 集成来保护身份验证。
* 使用行级别安全性将访问对象限制为适当的数据子集。
* 通过策略实时屏蔽数据。
* 通过 Azure SQL 数据库审核跟踪数据库事件。

> “我们可以使用所有这些选项而不进行任何重大的代码更改，并将性能的影响降到最低”，Nagalkar 说。
> 
> 

使用 Azure SQL 数据库与将同样的花费用在本地环境中进行工程相比，GEP 可自动拥有更强大的灾难恢复功能，因为 Azure SQL 数据库内置容错功能。 GEP 使用 Azure SQL 数据库中的活动异地复制功能，结合了不同地理区域中的多个活动、可读取的联机辅助副本（Always On 可用性组），构成高可用性对。 在各个区域复制 SMART by GEP 数据意味着如果发生区域范围的灾难，GEP 只需根据最低恢复点目标 (RPO) 和恢复时间目标 (RTO)，即可轻松恢复客户数据。

每个 SMART by GEP 客户有两个 Azure SQL 数据库实例：一个用于联机事务处理 (OLTP)，一个用于分析（例如客户开支和报表分析）。 Azure SQL 数据库弹性池可让 GEP 轻松管理全球数千个数据库，处理无法预测的数据库资源需求。 弹性池为 GEP 提供一种方式，既能确保客户数据库可根据需要缩放，而不会出现过度预配或预配不足的情况，同时使 GEP 能够控制成本。 此外，由于这是一项 PaaS 服务，GEP 可在自动升级的过程中获取所有新的 Azure SQL 数据库功能。

## <a name="unstructured-and-semi-structured-data"></a>非结构化和半结构化数据
但是，有些 SMART by GEP 客户数据需要不太严格的结构化存储。 针对此类数据，GEP 采用 Azure Blob 存储、Azure 表存储和 Azure Redis 缓存。 Azure Blob 存储容装 SMART by GEP 用户上载到应用程序中的所有附件。 它也是 SMART by GEP 存储静态内容（例如级联样式表 (CSS) 和 JavaScript 文件）的位置。

GEP 将不面向客户的数据（例如 SMART by GEP 日志数据）存储在 Azure 表存储中，有效利用高性价比的无限制存储和加速检索速度，无需担心如何为数据设置架构。 GEP 使用 Azure Redis 缓存作为主要缓存。

## <a name="authentication-and-routing"></a>身份验证和路由
Azure 访问控制服务 (ACS) 为 SMART by GEP 用户提供各种不同的选项来登录软件。 Azure ACS 可与使用安全断言标记语言 (SAML) 交换身份验证数据的任何标识提供者（例如，Active Directory 域服务、Ping Identity、OneLogin 或 SiteMinder）联合。 这可以帮助 GEP 为客户实现单一登录 (SSO)，无需担心如何存储用户凭据和维护客户密码策略。

登录后，客户便可以访问 SMART by GEP 中的适当业务资源。 GEP 使用 Azure 流量管理器针对来自客户移动设备和浏览器会话的请求进行重定向和负载均衡。

## <a name="other-azure-services"></a>其他 Azure 服务
GEP 采用了其他一些 Azure 服务，使 SMART by GEP 能够响应客户需求。 GEP 使用 Azure 云服务（Web 角色和辅助角色）托管应用程序呈现和受保护的业务逻辑服务。 云服务不仅让开发人员能够管理基础结构即代码 (IAC)，而且部署新 SMART by GEP 应用程序所花的时间也比在本地数据中心内部署所需的时间短很多，这些任务全都不需要 IT 人员的介入。 GEP 开发人员可以使用云服务过渡环境来测试新版本，而不影响当前的生产部署。 测试之后，GEP 可在一分钟内使用 Azure 云服务的 VIP 交换功能，将过渡代码转移到生产槽，以此缩短部署停机时间。

为了降低应用程序延迟，GEP 使用 Azure 内容交付网络 (CDN) 将存储在 Azure Blob 存储中的静态内容（例如 CSS 和 JavaScript 文件）放置在靠近用户的边缘服务器上。 GEP 使用 Azure 服务总线来支持应用程序体系结构模式，包括发布-订阅到部分命令查询响应分离 (CQRS) 及包含松散耦合与异步通信的分层体系结构。 GEP 使用 Azure 媒体服务改善其客户支持服务。 GEP 发现，可以轻松将用户支持视频发布到 Azure 媒体服务。 这些视频解答了常见用户问题，除了减轻 GEP 客户支持人员的部分支持负担以外，还有助于提升 SMART by GEP 用户的满意度。

为了发送 SMART by GEP 每天生成的数千封事务邮件，公司使用 SendGrid .NET API 来与 Azure 集成。 对 GEP 开发人员而言，此过程十分简单 — Azure Marketplace 中就有适用于 Azure 的 SendGrid 加载项。 GEP 开发人员可以直接在 Microsoft Visual Studio 中使用 SendGrid NuGet 包来配置 SMART by GEP；GEP IT 可以直接从 Azure 监视软件的 SendGrid 电子邮件流量。

最后，SMART by GEP 使用 Azure 虚拟机（Azure IaaS 服务）来托管工程设计时还没有成形的应用程序和服务，取代软件即服务 (SaaS) 或 PaaS 解决方案。 例如，GEP 将集成 API 服务托管在虚拟机中，以便与客户本地企业资源规划 (ERP) 系统（例如 SAP、Oracle、PeopleSoft、JD Edwards、Microsoft Dynamics GP 和 Lawson）以及客户 SaaS 解决方案进行企业到企业 (B2B) 集成，有效交换采购文档（例如发票）。

> “在 Microsoft Azure 云中构建 SMART by GEP 完全免除了对本地 IT 的需求，不仅对 GEP 而言是如此，对我们客户的采购运营也是如此。” 
> 
> — Dhananjay Nagalkar，技术解决方案副总裁
> 
> 

## <a name="expand-customer-satisfaction-without-expanding-it"></a>无需扩展 IT 资源即可提升客户满意度
从本地数据中心迁移到 Azure，并且在 Azure 平台上从头开始构建 SMART by GEP 之后，GEP 无需扩展其基础结构或 IT 人员编制，就提高了可缩放性与灵活性。 事实上，该公司已有四年多未添置 IT 资源。 Azure 便利的 PaaS 模型让 GEP 降低了供应商支持和运营管理方面的开支。 因此，GEP 能够将资源集中在软件开发上；在云中开发使得 GEP 开发人员能够快速调研新的创意，无需花费时间与 IT 部门协调，或担心本地授权要求。 借助 Azure SQL 数据库，GEP 更有信心确保其客户始终享有卓越的服务与性能。

## <a name="more-information"></a>详细信息
* GEP 主页：[GEP](http://www.gep.com)
* Smart by GEP：[Smart by GEP](http://www.smartbygep.com)

## <a name="gep-contributors"></a>GEP 参与者
* Huzaifa Matawala，GEP 助理架构主任
* Sathyan Narasingh，GEP 工程经理
* Deepa Velukutty，GEP 数据库架构师


