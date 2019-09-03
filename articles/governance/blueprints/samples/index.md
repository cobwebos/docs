---
title: 蓝图示例的索引
description: 使用 Azure 蓝图进行部署的符合性和标准环境示例的索引。
author: DCtheGeek
manager: carmonm
ms.service: blueprints
ms.topic: sample
ms.date: 08/21/2019
ms.author: dacoulte
ms.custom: fasttrack-edit
ms.openlocfilehash: 58deb4fff4cee21acbf99d3c4035a9941697bed4
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231836"
---
# <a name="azure-blueprints-samples"></a>Azure 蓝图示例

下表包含 Azure 蓝图示例的链接。 每个示例都可投入实际生产运行，并且现在已可供部署，以便帮助满足你的各种符合性需求。

## <a name="standards-based-blueprint-samples"></a>基于标准的蓝图示例

|  |  |
|---------|---------|
| [加拿大联邦 PBMM](./canada-federal-pbmm/control-mapping.md) | 提供防护措施，以便符合加拿大联邦受保护的 B、中等完整性、中等可用性 (PBMM)。 |
| [CIS Microsoft Azure 基础基准](./cis-azure-1.1.0/index.md)| 提供一组策略以帮助符合 CIS Microsoft Azure 基础基准建议。 |
| [IRS 1075](./irs-1075/index.md) | 提供用于符合 IRS 1075 的规范措施。|
| [ISO 27001](./iso27001/index.md) | 提供用于符合 ISO 27001 的规范措施。 |
| [ISO 27001 共享服务](./iso27001-shared/index.md) | 提供了一组符合标准的基础结构模式和策略防护机制，以便帮助通过 ISO 27001 认证。 |
| [ISO 27001 应用服务环境/SQL 数据库工作负荷](./iso27001-ase-sql-workload/index.md) | 为 [ISO 27001 共享服务](./iso27001-shared/index.md)蓝图示例提供了其他基础结构。 |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | 提供用于符合 NIST SP 800-53 R4 的规范措施。 |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | 提供一组策略以帮助用户符合 PCI-DSS v3.2.1。 |
| [英国官方和英国 NHS 监管](./ukofficial/index.md) | 提供了一组符合标准的基础结构模式和策略防护措施，以便帮助用户通过英国官方和英国 NHS 认证。 |
| [CAF 基础](./caf-foundation/index.md) | 提供了一组控制措施，以便帮助你管理云资产，使其与[适用于 Azure 的 Microsoft 云采用框架 (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index) 相符合。 |
| [CAF 迁移登陆区域](./caf-migrate-landing-zone/index.md) | 提供了一组控制措施，以便帮助你安排迁移你的第一个工作负荷并管理你的云资产，使其与[适用于 Azure 的 Microsoft 云采用框架 (CAF)](/azure/architecture/cloud-adoption/migrate/index) 相符合。 |

## <a name="samples-strategy"></a>示例策略

![蓝图示例策略](../media/blueprint-samples-strategy.png)

CAF 基础和 CAF 迁移登陆区域蓝图假定客户正在准备一个现有的干净单一订阅，以便将本地资产/工作负荷迁移到 Azure。
（上图中的区域 A 和 B）。  

可以基于示例蓝图进行迭代，并查找客户正在应用的自定义模式。 此外，还可以主动处理特定于行业（如金融服务和电子商务）的蓝图（区域 B 的顶端）。 同样，我们设想针对复杂的架构考虑因素（如多个订阅、高可用性、跨区域资源以及对现有订阅和资源实施控制的客户）构建蓝图（区域 C 和 D）。

有一些示例蓝图可满足符合性要求较高、体系结构复杂性较高的客户场景需求（上图中的区域 E）。 上面的区域 F 表示将由客户和合作伙伴处理的一种示例蓝图，客户和合作伙伴利用这些示例蓝图并根据自己的独特需求对其进行自定义。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。