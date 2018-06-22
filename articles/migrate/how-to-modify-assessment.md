---
title: 自定义 Azure 迁移评估设置 | Microsoft Docs
description: 介绍如何使用 Azure Migration Planner 设置和运行将 VMware VM 迁移到 Azure 的评估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/31/2018
ms.author: raynew
ms.openlocfilehash: 73dab9c7eca53ecce44d43a9607fcc7426f9de8d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715500"
---
# <a name="customize-an-assessment"></a>自定义评估

[Azure Migrate](migrate-overview.md) 使用默认属性创建评估。 创建评估后，可使用本文中的说明修改默认属性。


## <a name="edit-assessment-properties"></a>编辑评估属性

1. 在迁移项目“评估”页面上，选择评估，然后单击“编辑属性”。
2. 根据下表修改属性：

    **设置** | **详细信息** | **默认**
    --- | --- | ---
    **目标位置** | 要迁移到的 Azure 位置。<br/><br/> Azure Migrate 当前支持 30 个区域，包括澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、印度中部、美国中部、中国东部、中国北部、东亚、美国东部、德国中部、德国东北部、美国东部 2、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、东南亚、印度南部、英国南部、英国西部、US Gov 亚利桑那州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、美国中西部、西欧、印度西部、美国西部和美国西部 2。 |  美国西部 2 是默认位置。
    **存储类型** | 可以指定要在 Azure 中分配的磁盘的类型。 当大小调整条件为“按本地大小调整”时，此属性适用。 可以将目标磁盘类型指定为“高级托管磁盘”或“标准托管磁盘”。 对于基于性能的大小调整，将根据 VM 的性能数据自动提供磁盘建议。 请注意，Azure Migrate 仅支持使用托管磁盘进行迁移评估。 | 默认值是高级托管磁盘（大小调整条件为“按本地大小调整”）。
    **“大小调整”条件** | Azure Migrate 用来对 Azure 的 VM 进行大小调整的条件。 可执行基于性能的大小调整或将 VM 的大小设置为本地大小，而不用考虑性能历史记录。 | 默认选项为基于性能的大小调整。
    **性能历史记录** | 评估 VM 性能时要考虑的时长。 此属性仅在“大小调整”条件为基于性能调整大小时才适用。 | 默认时长为一天。
    **百分位使用率** | 进行适当大小调整时要考虑的性能样本集的百分位值。 此属性仅在“大小调整”条件为基于性能调整大小时才适用。  | 默认值为第 95 个百分位。
    **VM 系列** | 可以指定想要考虑进行适当大小调整的 VM 系列。 例如，如果你不打算将生产环境迁移到 Azure 中的 A 系列 VM，可以从列表或系列中排除 A 系列，则将只在所选系列中完成适当大小调整。 | 默认情况下，选择所有 VM 系列。
    **定价层** | 可指定目标 Azure VM 的[定价层（基本/标准）](../virtual-machines/windows/sizes-general.md)。 例如，如果打算迁移生产环境，则可考虑“标准”层，其提供的 VM 延迟较低但成本可能较高。 而如果使用开发-测试环境，则可考虑“基本”层，其 VM 延迟较高，但成本较低。 | 默认使用[标准](../virtual-machines/windows/sizes-general.md)层。
    **舒适因子** | Azure Migrate 在评估期间会考虑到缓冲（舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 | 默认设置是1.3x。
    **产品** | 你注册到的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)是默认设置。
    **货币** | 计费货币。 | 默认为美元。
    **折扣 (%)** | 基于 Azure 优惠获得的任何特定订阅的折扣。 | 默认设置是 0%。
    **Azure 混合权益** | 指定你是否具有软件保证以及是否有资格享受 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 | 默认值为“是”。

3. 单击“保存”更新评估。


## <a name="next-steps"></a>后续步骤

[详细了解](concepts-assessment-calculation.md)如何计算评估。
