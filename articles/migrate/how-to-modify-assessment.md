---
title: 自定义 Azure 迁移评估设置 | Microsoft Docs
description: 介绍如何使用 Azure Migration Planner 设置和运行将 VMware VM 迁移到 Azure 的评估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 02/26/2018
ms.author: raynew
ms.openlocfilehash: 459a29012ec879d4d4989e51b5688b9042adc1a1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="customize-an-assessment"></a>自定义评估

[Azure Migrate](migrate-overview.md) 使用默认属性创建评估。 创建评估后，可使用本文中的说明修改默认属性。


## <a name="edit-assessment-properties"></a>编辑评估属性

1. 在迁移项目“评估”页面上，选择评估，然后单击“编辑属性”。
2. 根据下表修改属性：

    **设置** | **详细信息** | **默认**
    --- | --- | ---
    **目标位置** | 要迁移到的 Azure 位置。<br/><br/> Azure Migrate 当前支持 30 个区域，包括澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、印度中部、美国中部、中国东部、中国北部、亚洲东部、美国东部、德国中部、德国东北部、美国东部 2、日本东部、日本西部、韩国中部、韩国南部、美国中北部、欧洲北部、美国中南部、东南亚、印度南部、英国南部、英国西部、美国中西部、西欧、印度西部、美国西部和美国西部 2。 |  美国西部 2 是默认位置。
    **存储冗余** | Azure VM 要在迁移后使用的存储冗余类型。 | 默认值为[本地冗余存储 (LRS)](../storage/common/storage-redundancy-lrs.md)。 Azure Migrate 仅支持基于托管磁盘的评估，而托管磁盘仅支持 LRS，因此该属性目前只有 LRS 选项。 
    **“大小调整”条件** | Azure Migrate 用来对 Azure 的 VM 进行大小调整的条件。 可执行基于性能的大小调整或将 VM 的大小设置为本地大小，而不用考虑性能历史记录。 | 默认选项为基于性能的大小调整。
    **性能历史记录** | 评估 VM 性能时要考虑的时长。 此属性仅在“大小调整”条件为基于性能调整大小时才适用。 | 默认时长为一天。
    **百分位使用率** | 进行适当大小调整时要考虑的性能样本集的百分位值。 此属性仅在“大小调整”条件为基于性能调整大小时才适用。  | 默认值为第 95 个百分位。
    **定价层** | 可指定目标 Azure VM 的[定价层（基本/标准）](../virtual-machines/windows/sizes-general.md)。 例如，如果打算迁移生产环境，则可考虑“标准”层，其提供的 VM 延迟较低但成本可能较高。 而如果使用开发-测试环境，则可考虑“基本”层，其 VM 延迟较高，但成本较低。 | 默认使用[标准](../virtual-machines/windows/sizes-general.md)层。
    **舒适因子** | Azure Migrate 在评估期间会考虑到缓冲（舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 | 默认设置是1.3x。
    **产品** | 你注册到的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)是默认设置。
    **货币** | 计费货币。 | 默认为美元。
    **折扣 (%)** | 基于 Azure 优惠获得的任何特定订阅的折扣。 | 默认设置是 0%。
    **Azure 混合权益** | 指定你是否具有软件保证以及是否有资格享受 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 | 默认值为“是”。

3. 单击“保存”更新评估。


## <a name="next-steps"></a>后续步骤

[详细了解](concepts-assessment-calculation.md)如何计算评估。
