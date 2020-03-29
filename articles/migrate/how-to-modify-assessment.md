---
title: 自定义 Azure 迁移服务器评估评估 |微软文档
description: 描述如何自定义使用 Azure 迁移服务器评估创建的评估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234275"
---
# <a name="customize-an-assessment"></a>自定义评估

本文介绍如何自定义由 Azure 迁移服务器评估创建的评估。

[Azure Migrate](migrate-services-overview.md) 提供一个中心用于跟踪本地应用、工作负荷与私有云/公有云 VM 的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

可以使用 Azure 迁移服务器评估工具为本地 VMware VM 和超 VM 创建评估，为迁移到 Azure 做准备。

## <a name="about-assessments"></a>关于评估

使用 Azure Migrate 服务器评估可以运行两种类型的评估。

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据的评估 | **建议的 VM 大小**：基于 CPU 和内存利用率数据。<br/><br/> **推荐的磁盘类型（标准磁盘或高级托管磁盘）：** 基于 IOPS 和本地磁盘的吞吐量。
**本地** | 基于本地大小的评估。 | **建议的 VM 大小**：基于本地 VM 大小<br/><br> **建议的磁盘类型**：根据为评估选择的存储类型设置。


## <a name="how-is-an-assessment-done"></a>如何进行评估？

在 Azure 迁移服务器评估中执行的评估分为三个阶段。 评估的第一步是适用性分析，第二步是大小估计，最后一步是每月成本估计。 如果一台计算机通过了前一个阶段，则只能按顺序进入下一个阶段。 例如，如果一台计算机未通过 Azure 适用性检查，将被标记为不适合迁移到 Azure，并且不会进行大小调整和成本估算。 [了解详细信息。](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>评估内容有哪些？

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 位置。<br/> 服务器评估目前支持这些目标区域：澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、印度中部、美国中部、中国东部、中国北部、东亚、美国东部、美国东部、德国中部、德国东北部、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、东南亚、南印度、英国南部、英国西部、美国亚利桑那州政府、美国得克萨斯州政府、美国弗吉尼亚州西部、美国中西部、西欧、西印度、美国西部和美国西部2。
**存储类型** | 可以使用此属性在 Azure 中指定要移动到的磁盘类型。<br/><br/> 对于本地部署大小调整，可以将目标存储类型指定为高级托管磁盘、标准 SSD 托管磁盘或标准 HDD 管理磁盘。 对于基于性能的尺寸调整，可以将目标磁盘类型指定为自动磁盘、高级管理磁盘、标准硬盘管理磁盘或标准 SSD 管理磁盘。<br/><br/> 将存储类型指定为自动时，将根据磁盘的性能数据（IOPS 和吞吐量）完成磁盘建议。 如果将存储类型指定为高级/标准，评估将建议在所选存储类型内使用磁盘 SKU。 如果要实现 99.9% 的单实例 VM SLA，则可能需要将存储类型指定为高级托管磁盘。 这可确保推荐评估中的所有磁盘作为高级托管磁盘。 Azure
**保留实例 （RI）** | 此属性可帮助您指定 Azure 中是否有[预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，然后评估中的成本估计将采用 RI 折扣。 目前仅支持 Azure Migrate 中的“即用即付”产品/服务。
**“大小调整”条件** | 用于对 Azure 的 VM 大小调整的条件。 您可以执行*基于性能*的大小调整，也可以将 VM*大小调整为本地*，而无需考虑性能历史记录。
**性能历史记录** | 用于评估计算机性能数据的持续时间。 此属性仅在大小调整条件基于*性能*时适用。
**百分位使用率** | 进行适当大小调整时要考虑的性能样本集的百分位值。 此属性仅在大小调整基于*性能*时适用。
**VM 系列** |     可以指定想要考虑进行适当大小调整的 VM 系列。 例如，如果你不打算将生产环境迁移到 Azure 中的 A 系列 VM，可以从列表或系列中排除 A 系列，则只在所选系列中完成适当大小调整。
**舒适因子** | Azure 迁移服务器评估会考虑评估期间的缓冲区（舒适系数）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。
**提供** | 加入的 [Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。
**货币** | 计费货币。
**折扣 (%)** | 基于 Azure 套餐获得的任何特定订阅的折扣。<br/> 默认设置是 0%。
**VM 运行时间** | 如果 VM 不会在 Azure 中全天候运行，则可指定运行持续时间（每月的天数和每天的小时数），然后系统就会进行相应的成本估算。<br/> 默认值为“每月 31 天和每天 24 小时”。
**Azure 混合权益** | 指定您是否具有软件保证并符合 Azure[混合权益的条件](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 默认值为“是”。


## <a name="edit-assessment-properties"></a>编辑评估属性

要在创建评估后编辑评估属性，请执行以下操作：

1. 在 Azure 迁移项目中，单击 **"服务器**"。
2. 在**Azure 迁移：服务器评估**中，单击评估计数。
3. 在**评估**中，单击>**编辑属性**的相关评估。
5. 根据上表自定义评估属性。
6. 单击“保存”**** 更新评估。


您还可以在创建评估时编辑评估属性。


## <a name="next-steps"></a>后续步骤

[详细了解](concepts-assessment-calculation.md)如何计算评估。
