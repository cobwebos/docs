---
title: Azure Migrate 创建迁移计划 |Microsoft Docs
description: 提供有关 Azure Migrate 创建迁移计划的指南。
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: a7d22a6a2f7100826c865beec0d1cff09fc39660
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043234"
---
# <a name="build-migration-plan-with-azure-migrate"></a>使用 Azure Migrate 构建迁移计划

本文提供了一个快速指南，帮助你使用[Azure Migrate](migrate-services-overview.md)在 Azure 上构建迁移计划。如果你有其他问题，请查看以下资源：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)
- 关于[Azure Migrate 设备](common-questions-appliance.md)的问题
- 有关[服务器迁移](common-questions-server-migration.md)的问题
- 在[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中获取问题答案

## <a name="define-the-goals-of-cloud-migration"></a>定义云迁移的目标

构建迁移计划之前，请务必了解并评估要迁移到云的[动机](https://docs.microsoft.com/azure/cloud-adoption-framework/strategy/motivations)，从而帮助生成更成功的业务成果。 作为[Azure 的云采用框架](https://docs.microsoft.com/azure/cloud-adoption-framework)说明，可能存在适用于你的业务的不同触发器和迁移方法：  

**关键业务事件** | **迁移结果**
--- | ---
数据中心退出 | 成本节约
合并、收购或 divestiture | 减少供应商或降低技术复杂性
资本支出降低 | 优化内部操作
终止对任务关键型技术的支持 | 提高业务灵活性
响应法规遵从性更改 | 为新的技术能力做准备
新数据主权要求 | 根据市场需求进行缩放
降低 IT 稳定性的中断和改善 | 根据地理需求进行缩放

迁移激励还可以帮助你通过迁移到 Azure 来反映你要实现的战略目标和结果。 下一步是确定和计划针对工作负荷定制的 Azure 迁移路径。 Azure Migrate：服务器评估工具可帮助你评估本地工作负荷，并提供指导和工具来帮助你进行迁移。

## <a name="understand-your-digital-estate"></a>了解你的数字场地

首先了解你的本地基础结构、应用程序和依赖项，以帮助确定你想要迁移到 Azure 的工作负荷并获得优化的成本预测。 服务器评估工具将帮助你回答以下问题：

### <a name="what-workloads-are-in-use"></a>正在使用哪些工作负荷？

使用轻型 Azure Migrate 设备执行本地 VMware Vm、Hyper-v Vm 和物理服务器的无代理发现。 持续发现将收集计算机配置和性能元数据，还可用于获取安装的应用程序的清单，以及在本地计算机上运行的角色/功能。 Azure Migrate 设备将收集：

- 计算机、磁盘和 Nic 的元数据详细信息

- 安装的应用程序（包括应用程序和角色/功能）  

- 性能数据，包括 CPU 和内存利用率、磁盘 IOPS 和吞吐量

接下来，将 "应用程序清单" 列表导出，以找出工作负荷上运行的所有 SQL Server 实例，并使用 Azure Migrate：数据库评估工具了解其就绪性。

 ![门户上的应用程序清单](./media/concepts-migration-planning/application-inventory-portal.png)

 ![应用程序清单导出](./media/concepts-migration-planning/application-inventory-export.png)

除了服务器评估工具的发现数据之外，还可以使用现有的 CMDB 数据来构建我们的服务器和数据库所在的视图，并了解跨业务部门、应用程序所有者、地理位置等的服务器分发，这进而有助于确定要迁移的工作负荷的优先级。

### <a name="what-dependencies-exist-between-workloads"></a>工作负荷之间存在哪些依赖项？

发现服务器后，请使用无代理依赖项映射来直观显示和标识跨服务器依赖项，以及用于将相互依赖的服务器移动到 Azure 的优化策略。 可视化效果有助于了解某些计算机是否正在使用，或者是否可以将其取消，而不是迁移。  请确保分析依赖关系，以确保不会遗留任何内容，并避免在迁移过程中出现意外中断。 执行应用程序清单和依赖项映射后，可以创建高可信度组并开始评估服务器。

 ![依赖项映射](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>它们是否经过优化并能正确调整规模？

由于 Azure 提供了一段时间内调整云容量大小的灵活性，因此，迁移是优化分配给服务器的 CPU 和内存资源的机会。 在之前标识的组上创建评估，以了解工作负荷的性能历史记录，这些工作负荷在 Azure 上的 rightsized VM Sku 和磁盘建议中非常重要。

## <a name="assess-your-readiness-for-migration"></a>评估迁移的准备情况

### <a name="readiness-and-suitability-analysis-for-azure"></a>Azure 的就绪性和适用性分析
导出 Azure VM 评估报表并按以下准备情况类别进行筛选，以了解 Azure 的 VM 准备情况：

- **适用于 azure**：你可以将这些计算机原样迁移到 Azure，而无需进行任何更改  

- **Azure 有条件的就绪**：你可以将这些计算机迁移到 azure，但你需要在这些服务器上进行少量更改，如评估

- **未准备好进行 azure**：你不能按原样将这些计算机迁移到 Azure，并且需要在迁移前按照修正指南解决问题

- **准备情况未知**：由于元数据不足，Azure Migrate 无法确定计算机的就绪性

使用数据库评估，可以评估将 SQL Server 数据空间迁移到 Azure SQL 数据库或 Azure SQL 托管实例的准备情况。 可以查看每个 SQL server 实例的迁移准备状态百分比。 此外，对于每个实例，你都可以在 Azure 中看到推荐的目标、可能的迁移阻止程序、重大更改计数、Azure SQL DB/Azure SQL VM 的准备情况和兼容性级别。 你可以更深入地了解迁移阻止程序的影响和修复方法的建议。

 ![数据库评估](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>大小建议

将计算机标记为 "就绪" 后，服务器评估会根据 Vm 的大小建议确定 Azure VM 和磁盘 SKU。 你可以根据性能历史记录选择查看大小建议（以便在迁移时优化资源），或基于其本地配置，而无需考虑性能历史记录。 对于数据库，可在数据库评估中查看有关数据库 SKU、定价层和计算级别的建议。  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>计算评估以获取在 Azure 中运行工作负荷的估计成本

评估中的*基于性能*的大小调整选项可让你为 Azure 优化工作负荷。 除了合理精简以外，还有其他一些途径可帮助节省成本：

- **保留实例**：对于保留实例，你可以通过 Windows 和 Linux （vm）上1年或3年的即用即付定价显著降低成本。

- **Azure 混合权益**：你可以将具有软件保障的 Windows Server 本地许可证带到 Azure，并将其与保留实例选项组合

- **企业协议产品/服务（EA）**：企业协议提供适用于你的订阅的内置节省

- **产品/服务**：有多个 Azure 产品/服务，例如即用即付开发/测试和 Enterprise 开发/测试，为开发或测试 vm 提供较低的费率

- **VM 运行时间**：你可以在运行 Azure vm 以减少成本（不适用于 RI）时，提及每个月的每月和小时的持续时间。

- **目标区域**：你可以在不同区域中创建多个评估，以比较在地理位置迁移到某个区域是否可能更具成本效益

- **基于性能的建议**：作为最佳做法，请尝试使用 RIGHTSIZED Azure VM 建议，这些建议可帮助你节省云成本

### <a name="visualize-data"></a>可视化数据

你可以在门户中查看具有 Azure 就绪性和每月成本分布的服务器评估报表，还可以导出评估以对发现和评估数据应用更多可视化效果，使迁移计划更丰富。 您可以为不同的属性组合创建多个评估，并选择最适合您的企业的一组属性。  

 ![“评估”概述](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>评估差距和潜在拦截

确定要迁移的应用程序和底层基础结构时，请确定这些应用程序的停机时间限制，并在应用程序和底层基础结构之间查找任何操作依赖关系。 此分析有助于规划满足恢复时间目标（RTO）的迁移，并确保最小到零数据丢失。 在迁移之前，我们建议你查看并缓解任何兼容性问题或不受支持的功能，这些问题可能会阻止迁移你的服务器和 SQL 数据库和服务器评估报表，并 Azure Migrate：数据库评估建议。

### <a name="first-workloads-to-target-and-approach"></a>目标和方法的第一个工作负荷

现在，你已拥有所有关键信息来做出迁移决策，你应该首先确定应该迁移哪些应用程序和工作负荷。 开发一个 "应用并了解" 方法，以系统且可控的方式迁移你的目标应用程序，以便可以在着手进行全规模迁移之前，消除此策略中的任何缺陷。 你还可以使用战略性因素（如复杂性和时间）迁移、业务紧急性、生产/非生产环境、符合性和安全要求、应用程序知识等，确定要迁移的应用程序组的优先级。

以下是一些建议的迁移策略：

- **确定快速 wins 的优先级**：你可以使用评估报告来确定低挂起的水果，包括完全准备就绪的服务器和数据库，并需要最少的工作量迁移到 Azure：
    - Azure 迁移就绪：导出评估报告并筛选 "适用于 Azure 的所有计算机"。 这可以是你的第一组计算机，你可以通过 Azure Migrate：服务器迁移工具来抬起和切换。
    - 操作系统支持终止：导出你的评估报告并筛选运行 Windows Server 2008 和 Windows Server 2008 R2 操作系统的所有计算机。 这些 Sku 已结束支持，只有 Azure 会在你将其迁移到 Azure 时提供免费的3年安全更新。 当您组合、Azure 混合权益和使用预订实例时，保存可能会高得多。
    - SQL Server 迁移：对于 Azure SQL 数据库，请使用数据库评估建议为 Azure SQL Azure Migrate 数据库迁移准备就绪的数据库，并使用 Azure Migrate： Server 迁移为 Azure SQL VM 准备好使用 Azure SQL VM。
    - 软件最终支持：导出应用程序清单，并筛选出可能会到达支持结束的任何软件/扩展。 应设置这些应用程序的优先级。
    - 过度预配的 Vm：导出评估报告并筛选出 CPU 使用率较低的计算机（%）和内存使用率（%）。  可以使用此机会迁移到 Azure 中的 rightsized VM，并保存已为未充分利用的资源付费。
    - 容量限制：导出评估报告并筛选出 CPU 使用率高的计算机（%）和内存使用率（%）。  可以通过将 overstrained Vm 迁移到 Azure 并利用自动缩放功能满足需求，来防止这些 Vm 中断和提高性能。 你还可以通过分析磁盘 IOPS 和吞吐量来了解你的存储约束，并找到最适合你的需求的建议磁盘类型。

- 从小到**大**一点：开始移动应用程序和工作负荷，这是最小的风险和不太复杂的工作负载，以便在迁移策略中建立信心。 你还可以将你的 Azure Migrate 评估建议与组织的 CMDB 存储库交叉，以便在试点迁移中查找和迁移开发/测试环境工作负荷。 在迁移生产工作负荷时，可以使用这些试验中的知识。  

- **遵守你的法规/行业要求**： Azure 在产品/服务的广度和深度方面维持业内最大的合规性组合。 使用此过程可以确定迁移到 Azure 的优先级，并遵守您的国家、地区和行业特定的标准和法律。 对于处理业务关键或保存敏感信息的组织，或在受管控行业（其中的标准和法规在某些情况下可能会发生变化，abound 和在某些情况下可能发生变化），尤其如此。  

## <a name="finalize-the-migration-planandprepare-formigration"></a>完成迁移计划并准备迁移

在完成迁移计划之前，请确保这些关键迁移注意事项不会障碍迁移规划：

- 评估网络带宽和延迟的限制，这可能会导致不可预测的延迟并中断迁移复制速度。

- 缓冲时间以对已迁移的应用程序进行性能和用户验收测试，或执行任何迁移后应用程序调整，如更新数据库连接字符串和 web 服务器配置、执行转换和清理等。

- 查看建议的 Azure 权限以及你的服务器/数据库访问角色和迁移所需的权限模型。

- 准备你的组织，确保员工与数字转换保持一致。 坚实的培训基础对于成功的组织变化非常重要。 查看 [Microsoft Learn](https://docs.microsoft.com/learn/azure/?ocid=CM_Discovery_Checklist_PDF)上提供的免费培训，其中包括有关 Azure 基础、解决方案体系结构和安全性的课程。 鼓励你的团队也可以探索 [Azure 认证](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)   。  

- 如果需要，获取实现支持。 许多组织选择外部帮助来支持云迁移。 若要使用个性化协助快速、自信地迁移到 Azure，请考虑使用 [Azure 专家托管服务提供商](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   或 [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF)。  

创建一个有效的云迁移计划，其中包括有关要迁移的应用程序的列表/组的详细信息、应用程序和数据库的可用性以及停机限制以及所需的迁移里程碑。 你的迁移计划还应考虑数据复制将采用的时间，并包含用于迁移后测试和转换活动的深思熟虑后得出缓冲区。 迁移后测试必须包括功能、集成、安全和性能测试用例，以确保已迁移的应用程序按预期运行，并且所有数据库对象和数据关系均已成功传输到云。 迁移后测试必须包括功能、集成、安全和性能测试用例，以确保已迁移的应用程序按预期运行，并且所有数据库对象和数据关系均已成功传输到云。  

使用此分析构建迁移路线图，并声明一个维护时段，将应用程序和数据库迁移到最少停机时间，并在迁移期间限制潜在的操作/业务影响。  

建议你始终先测试并继续使用 Azure Migrate 的 *测试迁移*功能，然后开始例行将完全规模迁移到 Azure。 此真实数据将帮助你估计所涉及的实际时间，并对迁移计划做出必要的调整。 还可以通过测试迁移来发现迁移计划的任何潜在问题，并在实际迁移发生之前修复这些问题。  

准备好迁移后，请使用 Azure Migrate 的*服务器迁移工具*和 Azure Migrate 的*数据迁移服务*，以实现与端到端跟踪的无缝集成的无缝迁移体验。 服务器迁移工具支持在客户的数据中心或任何其他私有或公有云（包括 AWS、GCP 等）中迁移本地托管的 Vm 和服务器。 Azure 数据库迁移服务是一项完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，并且最小化停机时间。  

> [!NOTE]
> 对于 VMware Vm，服务器评估使用在 vCenter Server 中为 VM 指定的操作系统来处理来宾操作系统分析。 对于在 VMware 上运行的 Linux Vm，它当前不识别来宾操作系统的确切内核版本。

## <a name="next-steps"></a>后续步骤

- 在 Azure 云采用框架中调查 [云迁移旅程](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)   。
- Azure Migrate[入门](https://youtu.be/wFfq3YPxYHE)。
- 为[VMware vm](tutorial-assess-vmware.md)或[hyper-v vm](tutorial-assess-hyper-v.md)创建评估。
