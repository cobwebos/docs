---
title: 自定义 Azure 迁移评估设置 | Microsoft Docs
description: 介绍如何使用 Azure Migration Planner 设置和运行将 VMware VM 迁移到 Azure 的评估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 8419d7e7a91e4cbfd0eebfe00d35bf498cf5998c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129817"
---
# <a name="customize-an-assessment"></a>自定义评估

[Azure Migrate](migrate-overview.md) 使用默认属性创建评估。 创建评估后，可使用本文中的说明修改默认属性。


## <a name="edit-assessment-properties"></a>编辑评估属性

1. 在迁移项目“评估”页面上，选择评估，然后单击“编辑属性”   。
2. 根据以下详细信息自定义评估属性：

    **设置** | **详细信息** | **默认**
    --- | --- | ---
    **目标位置** | 要迁移到的 Azure 位置。<br/><br/> Azure Migrate 当前支持 30 个区域，包括澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、印度中部、美国中部、中国东部、中国北部、东亚、美国东部、德国中部、德国东北部、美国东部 2、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、东南亚、印度南部、英国南部、英国西部、US Gov 亚利桑那州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、美国中西部、西欧、印度西部、美国西部和美国西部 2。 |  美国西部 2 是默认位置。
    **存储类型** | 可以使用该属性指定要在 Azure 中移动的磁盘的类型。 对于按本地大小调整，可将目标磁盘类型指定为“高级托管磁盘”或“标准托管磁盘”。 对于基于性能的大小调整，可将目标磁盘类型指定为“自动”、“高级托管磁盘”或“标准托管磁盘”。 将存储类型指定为自动时，将根据磁盘的性能数据（IOPS 和吞吐量）完成磁盘建议。 例如，如果要实现 [99.9% 的单实例 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)，则可能需要将存储类型指定为高级托管磁盘。 这可确保推荐评估中的所有磁盘作为高级托管磁盘。 请注意，Azure Migrate 仅支持使用托管磁盘进行迁移评估。 | 默认值是高级托管磁盘（大小调整条件为“按本地大小调整”）  。
    **预留实例** |  也可指定自己在 Azure 中是否有[保留的实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，然后 Azure Migrate 就会进行相应的成本估算。 目前仅支持 Azure Migrate 中的“即用即付”产品/服务。 | 此属性的默认值是 3 年预留实例。
    **“大小调整”条件** | Azure Migrate 用来对 Azure 的 VM 进行大小调整的条件。 可执行基于性能的大小调整或将 VM 的大小设置为本地大小，而不用考虑性能历史记录   。 | 默认选项为基于性能的大小调整。
    **性能历史记录** | 评估 VM 性能时要考虑的时长。 此属性仅在“大小调整”条件为基于性能调整大小时才适用  。 | 默认时长为一天。
    **百分位使用率** | 进行适当大小调整时要考虑的性能样本集的百分位值。 此属性仅在“大小调整”条件为基于性能调整大小时才适用  。  | 默认值为第 95 个百分位。
    **VM 系列** | 可以指定想要考虑进行适当大小调整的 VM 系列。 例如，如果你不打算将生产环境迁移到 Azure 中的 A 系列 VM，可以从列表或系列中排除 A 系列，则只在所选系列中完成适当大小调整。 | 默认情况下，选择所有 VM 系列。
    **舒适因子** | Azure Migrate 在评估期间会考虑到缓冲（舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 | 默认设置是1.3x。
    **产品** | 你注册到的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)是默认设置。
    **货币** | 计费货币。 | 默认为美元。
    **折扣 (%)** | 基于 Azure 套餐获得的任何特定订阅的折扣。 | 默认设置是 0%。
    **VM 运行时间** | 如果 VM 不会在 Azure 中全天候运行，则可指定运行持续时间（每月的天数和每天的小时数），然后系统就会进行相应的成本估算。 | 默认值为“每月 31 天和每天 24 小时”。
    **Azure 混合权益** | 指定你是否具有软件保证以及是否有资格享受 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 | 默认值为“是”。

3. 单击“保存”  更新评估。

## <a name="faqs-on-assessment-properties"></a>评估属性常见问题解答

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>按本地大小调整和基于性能的大小调整之间有何区别？

将大小调整标准指定为本地大小调整时，Azure Migrate 不会考虑 VM 的性能数据，也不会根据内部部署配置调整 VM 的大小。 如果大小调整标准基于性能，则根据使用率数据完成大小调整。 例如，如果某个本地 VM 具有 4 个核心和 8-GB 内存，CPU 利用率为 50% 且内存利用率为 50%。 如果大小调整标准与本地的大小调整标准相同，则会建议采用具有 4 个核心和 8-GB 内存的 Azure VM SKU；但是，如果大小调整标准是基于性能的，则会建议采用具有 2 个核心和 4 GB 内存的 VM SKU，因为在提出大小建议时会考虑利用率百分比。

同样，对于磁盘，磁盘大小调整取决于两个评估属性 - 大小调整标准和存储类型。 如果大小调整标准是基于性能的并且存储类型是自动，则会考虑磁盘的 IOPS 和吞吐量值来确定目标磁盘类型（标准或高级）。 如果大小调整标准是基于性能的并且存储类型是高级，则会建议采用高级磁盘，并且会基于本地磁盘的大小选择 Azure 中的高级磁盘 SKU。 当大小调整标准与本地大小调整标准相同并且存储类型为标准或高级时，将使用同一逻辑来执行磁盘大小调整。

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>性能历史记录和利用百分率对大小建议有什么影响？

这些属性仅适用于基于性能的大小调整。 Azure Migrate 收集本地计算机的性能历史记录并使用它来针对 Azure 中的 VM 大小和磁盘类型提出建议。

- 收集器设备持续分析本地环境，每 20 秒收集一次实时利用率数据。
- 设备对时长 20 秒的样本进行汇总，每隔 15 分钟创建单个数据点。 为了创建单个数据点，设备从所有时长 20 秒的样本中选择峰值并将其发送到 Azure。
- 当你在 Azure 中创建评估时，Azure Migrate 会根据性能持续时间和性能历史记录百分点值计算有效利用率值并使用它进行大小调整。

例如，如果你将性能持续时间设置为 1 天，将百分点值设置为 95%，则 Azure Migrate 将使用收集器在上一天发送的 15 分钟样本点，按升序对其进行排序，并选取第 95 个百分点值作为有效利用率。 第 95 个百分点值可以确保忽略任何离群值，如果选取第 99 个百分点值，可能会出现离群值。 如果希望选取期间的峰值利用率并且不希望错过任何离群值，则应当选择第 99 个百分点值。

## <a name="next-steps"></a>后续步骤

[详细了解](concepts-assessment-calculation.md)如何计算评估。
