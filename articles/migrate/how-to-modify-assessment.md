---
title: 自定义评估 Azure 迁移 Server 评估 |Microsoft Docs
description: 介绍如何自定义创建使用 Azure Migrate Server 评估评估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 8b200ce3d6e73a575b1b89d82a9323d58f435a48
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807925"
---
# <a name="customize-an-assessment"></a>自定义评估

本文介绍如何自定义创建的 Azure Migrate Server 评估的评估。

[Azure Migrate](migrate-services-overview.md)提供中心，以跟踪发现、 评估和迁移的本地应用和工作负荷，及私钥/公钥云 Vm 到 Azure。 中心提供了评估和迁移，以及第三方独立软件供应商 (ISV) 产品/服务的 Azure 迁移工具。

Azure Migrate Server 评估工具可用于为创建评估本地 VMware Vm 和 HYPER-V Vm，在准备迁移到 Azure。 

## <a name="about-assessments"></a>有关评估

有两种类型的可以使用 Azure Migrate Server 评估运行的评估。

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能的** | 评估基于收集的性能数据 | **建议的 VM 大小**:基于 CPU 和内存利用率数据。<br/><br/> **建议的磁盘类型 （标准或高级托管磁盘）** :基于 IOPS 和吞吐量的本地磁盘。
**作为本地** | 评估基于本地大小调整。 | **建议的 VM 大小**:根据在本地 VM 大小<br/><br> **建议的磁盘类型**:基于评估您选择的存储类型设置。


## <a name="how-is-an-assessment-done"></a>如何进行评估？

Azure Migrate 评估包含三个阶段。 评估的第一步是适用性分析，第二步是大小估计，最后一步是每月成本估计。 如果一台计算机通过了前一个阶段，则只能按顺序进入下一个阶段。 例如，如果一台计算机未通过 Azure 适用性检查，将被标记为不适合迁移到 Azure，并且不会进行大小调整和成本估算。

## <a name="whats-in-an-assessment"></a>评估内容有哪些？

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 位置。<br/> Azure Migrate 当前支持这些目标区域：澳大利亚东部、 澳大利亚东南部、 巴西南部、 加拿大中部、 加拿大东部、 印度中部、 美国中部、 中国东部、 中国北部、 亚洲东部、 美国东部、 美国东部 2、 德国中部、 德国东北部日本东部、 日本西部、 韩国中部、 韩国南部、 北部美国中部、 欧洲北部、 美国中南部、 亚洲东南部、 印度南部、 英国南部、 英国西部、 美国亚利桑那州的政府、 美国弗吉尼亚德克萨斯州、 美国弗吉尼亚州政府、 美国中西部、 西欧、 印度西部、 美国西部和美国西部 2 区。<br/> 默认情况下，目标区域设置为“美国西部 2”。
**存储类型** | 标准 HHD 标准磁盘/SSD 磁盘/高级。<br/> 如果指定的存储类型为自动评估中，磁盘建议基于磁盘 （IOPS 和吞吐量） 的性能数据。<br/> 如果您指定为高级/标准存储类型，选择评估建议的磁盘 SKU 中的存储类型。<br/> 如果你想要实现 99.9%的单实例 VM SLA，你可以将存储类型为高级托管磁盘。 然后会为高级托管磁盘，建议在评估中的所有磁盘。 <br/> Azure Migrate 仅支持使用托管磁盘进行迁移评估。<br/> 
**保留的实例 (RI)** | 如果你具有保留 Azure 中的实例，请指定此属性。 在评估中的成本的估计将 RI 折扣考虑在内。 保留的实例当前都是仅对即用即付 Azure Migrate 中提供了受支持。
**“大小调整”条件** | 用于映射到适当大小的 Vm。 可以基于性能的大小调整或**作为本地**，而无需考虑性能历史记录。
**性能历史记录** | 要考虑评估 VM 性能的持续时间。 基于性能的大小调整时，此属性才适用。
**百分位使用率** | 用于调整 Vm 的性能样本百分位值。 基于性能的大小调整时，此属性才适用。
**VM 系列** | 用于大小评估的 VM 系列。 例如，如果不打算将生产环境迁移到 Azure 中的 A 系列 VM，可以从列表或系列中排除 A 系列。 大小调整仅取决于所选系列。
**舒适因子** | 在评估期间，azure Migrate Server 评估会考虑到缓冲 （舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。
**产品** | 加入的 [Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。
**货币** | 计费货币。 
**折扣 (%)** | 基于 Azure 套餐获得的任何特定订阅的折扣。<br/> 默认设置是 0%。
**VM 运行时间** | 如果 VM 不会在 Azure 中全天候运行，则可指定运行持续时间（每月的天数和每天的小时数），然后系统就会进行相应的成本估算。<br/> 默认值为“每月 31 天和每天 24 小时”。
**Azure 混合权益** | 指定是否具有软件保障和符合条件[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 | 默认值为“是”。


## <a name="edit-assessment-properties"></a>编辑评估属性

若要创建评估后编辑评估属性，请执行以下操作：

1. 在 Azure Migrate 项目中，单击**服务器**。
2. 在**Azure 迁移：服务器评估**，单击评估计数。
3. 在中**评估**，单击相关评估 >**编辑属性**。
5. 自定义评估属性根据上表。
6. 单击“保存”  更新评估。


创建评估时，还可以编辑评估属性。


## <a name="next-steps"></a>后续步骤

[详细了解](concepts-assessment-calculation.md)如何计算评估。
