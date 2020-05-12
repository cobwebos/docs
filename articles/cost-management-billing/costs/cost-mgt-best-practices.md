---
title: 使用 Azure 成本管理优化云投资
description: 本文有助于最大程度利用云投资、减少成本以及对资金使用情况进行评估。
author: bandersmsft
ms.author: banders
ms.date: 05/04/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 759c69544c083e95cbd5198eecf9f7bb0e882aa8
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791606"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>如何通过 Azure 成本管理优化云投资

借助 Azure 成本管理所提供的工具，用户可以规划、分析和减少开支，最大程度利用云投资。 本文档提供成本管理的系统方法，重点介绍可用于解决组织成本难题的工具。 借助 Azure，可以轻松构建和部署云解决方案。 但是，重要的一点是，这些解决方案需要经过优化，以最大程度减少组织所承担的费用。 遵循本文档所述的原则并使用我们的工具，有助于确保组织已做好成功实施的准备。

## <a name="methodology"></a>方法

成本管理是一个组织问题，并且应在进行云资源投资前持续实施。 要成功实施成本管理并优化成本，组织必须：

- 准备好成功实施所需的恰当工具
- 对成本负责
- 采取适当措施优化支出

以下所述的三个关键团队，必须与组织达成一致，以确保成功管理成本。

- **财务** - 财务人员负责根据云支出预测批准整个组织内的预算请求。 他们支付相应的账单，并将成本分摊到各个团队以促进问责制。
- **经理** - 组织的业务决策制定者需要了解云支出，发现最佳支出结果。
- **应用团队** - 工程师每日管理云资源、开发服务以满足组织需要。 这些团队需要在指定的预算内交付最大价值的灵活性。

### <a name="key-principles"></a>关键原则

使用下面所述的原则，确保组织在云成本管理方面取得成功。

若要了解详细信息，请观看 [Cost Management setting up for success](https://www.youtube.com/watch?v=dVuwITdSAZ4)（通过成本管理设置来确保成功）视频。 若要观看其他视频，请访问[成本管理 YouTube 频道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/dVuwITdSAZ4]

#### <a name="planning"></a>规划

通过全面的预先规划，你可以根据特定的业务要求定制云使用。 问问你自己：

- 我需要解决哪些业务问题？
- 我所需的资源使用模式是什么？

你的回答有助于你选择适合自己的产品/服务。 你的回答决定要使用什么基础结构，以及如何使用该基础结构来最大程度提高 Azure 效率。

#### <a name="visibility"></a>可见性

如果成本管理的结构合理，这将有助于员工了解其所负责的 Azure 成本以及资金使用情况。 Azure 提供的相关服务可让你了解资金使用方向  。 请充分利用这些工具。 它们可以帮助你找到未充分利用的资源、消除浪费并最大程度利用节省成本的机会。

#### <a name="accountability"></a>问责制

在组织中进行成本归因可以确保责任人对其团队的支出负责。 若要全面了解组织的 Azure 支出，应组织相关资源，以最大限度了解成本归因。 良好的组织有助于管理和减少成本，并确保员工对组织中的高效支出负责。

#### <a name="optimization"></a>Optimization

行动起来，减少支出。 通过规划和提高成本可见性，根据收集的发现实现最大程度利用。 你需要考虑购买和许可优化，以及基础结构部署变化（本文档将在后续部分对此作详细介绍）。

#### <a name="iteration"></a>迭代

组织中的每个人必须加入到成本管理生命周期中。 他们需要对优化成本保持持续关注。 对此迭代过程应持严谨态度，将其作为组织中负责任的云监管的关键原则。

![显示可见性、问责制和优化的关键原则图](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>在计划时考虑成本

在部署云资源之前，需要评估以下各项：

- 最能满足你需要的 Azure 套餐
- 计划使用的资源
- 它们的成本可能是多少

Azure 提供了有助于评估过程的工具。 借助这些工具，可以充分了解启用工作负载所需的投资。 这样便可以根据自己的情况，选择最佳配置。

### <a name="azure-onboarding-options"></a>Azure 入门选项

最大程度利用成本管理的第一步是调查和确定最适合你的 Azure 套餐。 思考一下你将来计划如何使用 Azure。 此外，思考一下你希望如何配置计费模型。 制定决策时，请思考以下问题：

- 我计划使用 Azure 多长时间？ 我是否测试或计划构建长期基础结构？
- 我希望如何支付 Azure 费用？ 我应该预付以获得价格优惠，还是应该在月末转账付款？

要详细了解各种选项，请访问[如何购买 Azure](https://azure.microsoft.com/pricing/purchase-options/)。 以下列出了几种最常见的计费模型。

#### <a name="free"></a>[免费](https://azure.microsoft.com/free/)

- 12 个月的热门免费服务
- 200 美元额度，用于在 30 天内探索各种服务
- 超过 25 种服务永久免费

#### <a name="pay-as-you-go"></a>[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p)

- 无最低限额或承诺使用量
- 有竞争力的定价
- 只需为使用的服务付费
- 随时可以取消

#### <a name="enterprise-agreement"></a>[企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)

- 可选择预先货币承诺付款
- 可享受 Azure 价格优惠

#### <a name="azure-in-csp"></a>[Azure in CSP](https://azure.microsoft.com/offers/ms-azr-0145p/)

- 云解决方案提供商合作伙伴是负责解决客户需求的第一个联系点，也是客户关系中心
- 云解决方案提供商合作伙伴可以预配新客户、订购订阅、管理订阅，以及代表其客户执行管理任务
- 云解决方案提供商合作伙伴会将服务与独特的解决方案捆绑在一起或转售 Azure，同时还可以控制定价、条款和计费

## <a name="estimate-the-cost-of-your-solution"></a>估算解决方案的成本

在部署任何基础结构之前，评估解决方案的成本。 评估将帮助你为组织的工作负载预先创建预算。 然后可以长期使用预算来检验初始估算的有效性。 可以将其与已部署解决方案的实际成本相比较。

### <a name="azure-pricing-calculator"></a>Azure 定价计算器

借助 Azure 定价计算器，可以混合和匹配 Azure 服务的不同组合，以了解成本估算。 可以在 Azure 中采用不同方式实施解决方案 - 每种方式可能会影响整体支出。 预先考虑云部署的所有基础结构需求将有助于最有效地使用该工具。 这可以帮助你获得 Azure 中估算支出的可靠预估。

有关详细详细，请参阅 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator)。

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate 服务可以评估本地数据中心中的组织当前工作负载。 你可以深入了解你对 Azure 替代解决方案的需求。 首先，Migrate 会分析本地计算机，确定迁移是否可行。 然后，它会建议 Azure 中的 VM 大小，以最大程度优化性能。 最后，它还会创建基于 Azure 的解决方案的成本估算值。

有关详细信息，请参阅 [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview)。

## <a name="analyze-and-manage-your-costs"></a>分析和管理成本

及时了解组织的成本变化情况。 使用以下方法以正确理解和管理支出。

### <a name="organize-and-tag-your-resources"></a>组织和标记资源

组织资源时考虑成本。 在创建订阅和资源组时，考虑各团队所负责的相关成本。 请确保在报告中考虑组织要求。 订阅和资源组提供了组织和归因组织支出的良好存储桶。 标记是归因成本的好方法。 可将标记用作筛选器。 也可在分析数据和调查成本时，将其作为分组依据。 企业协议客户还可以创建部门并将订阅放置在部门下。 Azure 中的基于成本的组织有助于组织的相关人员对减少团队支出负责。

请观看视频[如何使用 Azure 成本管理查看标记策略](https://www.youtube.com/watch?v=nHQYcYGKuyw)，了解在组织中强制实施可缩放资源标记时可用的工具。 若要观看其他视频，请访问[成本管理 YouTube 频道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/nHQYcYGKuyw]


### <a name="use-cost-analysis"></a>使用成本分析

借助成本分析，可以使用标准资源属性对成本进行交叉分析，从而深入分析组织成本。 请思考以下常见问题，将其作为分析指南。 定期回答这些问题有助于你更加及时了解相关信息，并制定成本合理化决策。

- **当前月的估算成本** - 这个月已经产生的费用是多少？ 在预算范围内吗？
- **调查异常值** - 执行定期检查，确保成本在正常使用的合理范围内。 其趋势如何？ 是否有离群值？
- **发票对账** - 最近的已开票成本是否高于上个月？ 月支出习惯的环比变化是什么？
- **内部退款** - 我已经了解我的费用，那这些费用如何在组织中分配？

有关详细信息，请参阅[成本分析](quick-acm-cost-analysis.md)。

### <a name="export-billing-data-on-a-schedule"></a>按计划导出账单数据

是否需要将账单数据导入外部系统，例如仪表板或财务系统？ 可设置为自动导出到 Azure 存储，避免每月手动下载文件。 然后便可轻松地设置与其他系统的自动集成，使帐单数据保持同步。

要详细了解如何导出账单数据，请参阅[创建和管理导出的数据](tutorial-export-acm-data.md)。

### <a name="create-budgets"></a>创建预算

已确定并分析支出模式后，请务必开始为你和你的团队设置限制。 Azure 预算使你能够设置基于成本或使用情况的预算，其中包含许多阈值和警报。 请务必定期查看创建的预算，了解预算燃尽进度，根据需要进行更改。 通过 Azure 预算，还可以配置达到给定预算阈值时的自动化触发器。 例如，可以配置服务以关闭 VM。 或者，可以将基础结构移动到其他定价层以响应预算触发器。

有关详细信息，请参阅 [Azure 预算](tutorial-acm-create-budgets.md)。

有关基于预算的自动化的详细信息，请参阅[基于预算的自动化](../manage/cost-management-budget-scenario.md)。

## <a name="act-to-optimize"></a>行动起来，进行优化
使用以下方式来优化支出。

### <a name="cut-out-waste"></a>避免浪费

在 Azure 中部署基础结构后，请务必确保其正在使用中。 立即开始节省的最简单方法是查看资源并移除没有使用的资源。 由此，应该确定资源是否正在被尽可能高效地使用。

#### <a name="azure-advisor"></a>Azure 顾问

Azure 顾问服务可以识别在 CPU 或网络使用方面利用率低的虚拟机。 由此，可以确定是关闭还是根据估算成本调整计算机大小以继续运行计算机。 顾问还提供了用于预留实例购买的建议。 这些建议基于你过去 30 天的虚拟机使用情况。 一旦采纳，这些建议有助于减少支出。

有关详细信息，请参阅 [Azure 顾问](../../advisor/advisor-overview.md)。

### <a name="size-your-vms-properly"></a>正确调整 VM 的大小

VM 大小对于 Azure 总成本具有重大影响。 Azure 中所需的 VM 数量可能与本地数据中心中当前已部署的 VM 数量不相等。 请确保为计划运行的工作负载选择恰当大小。

有关详细信息，请参阅 [Azure IaaS：恰当大小和成本](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/)。

### <a name="use-purchase-discounts"></a>使用购买折扣

Azure 提供许多折扣，组织应利用这些折扣节省成本。

#### <a name="azure-reservations"></a>Azure 预留

通过 Azure 预留，可以预付一年或三年的虚拟机或 SQL 数据库计算容量。 通过预付，能够以折扣价购买所用资源。 Azure 预留可大幅度减少虚拟机或 SQL 数据库计算成本，最多可以降低 72% 的即用即付价格，但需提前一年或三年预留。 预留提供计费折扣，且不影响虚拟机或 SQL 数据库的运行时状态。

有关详细信息，请参阅[什么是 Azure 预留](../reservations/save-compute-costs-reservations.md)。

#### <a name="use-azure-hybrid-benefit"></a>使用 Azure 混合权益

如果本地部署中已有 Windows Server 或 SQL Server 许可证，可以使用 Azure 混合权益计划节省 Azure 费用。 如果享有 Windows Server 权益，每个许可证涵盖 OS（最多两台虚拟机）成本，仅需支付基础计算成本。 可以使用现有 SQL Server 许可证，在基于 vCore 的 SQL 数据库选项方面节省多达 55% 的费用。 选项包括 Azure 虚拟机中的 SQL Server 和 SQL Server Integration Services。

有关详细信息，请参阅 [Azure 混合权益节省计算器](https://azure.microsoft.com/pricing/hybrid-benefit/)。

### <a name="other-resources"></a>其他资源

Azure 还提供一项服务，借助该服务，可以构建利用 Azure 中的富余容量（以优惠费率计费）的服务。 有关详细信息，请参阅[在 Batch 中使用低优先级 VM](../../batch/batch-low-pri-vms.md)。

## <a name="next-steps"></a>后续步骤
- 如果不熟悉成本管理，请参阅[什么是 Azure 成本管理？](../cost-management-billing-overview.md)，了解成本管理如何监视和控制 Azure 支出并优化资源使用。
