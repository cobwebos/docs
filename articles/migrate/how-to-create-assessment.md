---
title: 使用 Azure Migrate Server 评估创建评估 |Microsoft Docs
description: 介绍如何使用 Azure Migrate Server 评估工具创建评估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229097"
---
# <a name="create-an-assessment"></a>创建评估

本文介绍如何使用 Azure Migrate 为本地 VMware Vm 或 Hyper-v Vm 创建评估:服务器评估。

[Azure Migrate](migrate-services-overview.md)可帮助你迁移到 Azure。 Azure Migrate 提供了一个集中式中心来跟踪将本地基础结构、应用程序和数据迁移到 Azure 的发现、评估和迁移。 该中心提供用于评估和迁移的 Azure 工具, 以及第三方独立软件供应商 (ISV) 产品。 

## <a name="before-you-start"></a>开始之前

- 请确保已[创建](how-to-add-tool-first-time.md)Azure Migrate 项目。
- 如果已创建项目, 请确保已[添加](how-to-assess.md)Azure Migrate:服务器评估工具。
- 若要创建评估, 需要为[VMware](how-to-set-up-appliance-vmware.md)或[hyper-v](how-to-set-up-appliance-hyper-v.md)设置 Azure Migrate 设备。 设备将发现本地计算机, 并将元数据和性能数据发送到 Azure Migrate:服务器评估。 [了解详细信息](migrate-appliance.md)。


## <a name="assessment-overview"></a>评估概述
使用 Azure Migrate 可以创建两种类型的评估:服务器评估。

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据的评估 | **建议的 VM 大小**:基于 CPU 和内存利用率数据。<br/><br/> **建议的磁盘类型 (标准或高级托管磁盘)** :基于本地磁盘的 IOPS 和吞吐量。
**作为本地** | 基于本地大小的评估。 | **建议的 VM 大小**:基于本地 VM 大小<br/><br> **建议的磁盘类型**:根据为评估选择的存储类型设置。

[了解](concepts-assessment-calculation.md)有关评估的详细信息。

## <a name="run-an-assessment"></a>运行评估

运行评估, 如下所示:

1. 查看创建评估的[最佳实践](best-practices-assessment.md)。
2. 在 "**服务器**" 选项卡**的 Azure Migrate:服务器评估**磁贴, 单击 "**评估**"。

    ![评估](./media/how-to-create-assessment/assess.png)

2. 在 "**评估服务器**" 中, 指定评估的名称。
3. 单击“全部查看”查看评估属性。

    ![评估属性](./media/how-to-create-assessment//view-all.png)

3. 在 "**选择或创建组**" 中, 选择 "**新建**", 然后指定组名称。 组收集一个或多个 Vm 以进行评估。
4. 在 "**将计算机添加到组**" 中, 选择要添加到组中的 vm。
5. 单击 "**创建评估**" 以创建组, 然后运行评估。

    ![创建评估](./media/how-to-create-assessment//assessment-create.png)

6. 创建评估后, 请在 "**服务器** >  **" 中查看 Azure Migrate:服务器评估** > **评估**。
7. 单击“导出评估”，将评估下载为 Excel 文件。



## <a name="review-an-assessment"></a>查看评估

评估介绍:

- **Azure 就绪**:Vm 是否适合迁移到 Azure。
- **每月成本估算**:在 Azure 中运行 Vm 的估计每月计算和存储成本。
- **每月存储成本估算**:迁移后磁盘存储的预估成本。

### <a name="view-an-assessment"></a>查看评估

1. 在 "**迁移目标** >  **服务器**" 中, **单击 "Azure Migrate 中的**评估**:服务器评估**。
2. 在 "**评估**" 中, 单击 "评估" 以将其打开。

    ![评估摘要](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>查看 Azure 就绪情况

1. 在**Azure 准备情况**下, 验证 vm 是否已准备好迁移到 Azure。
2. 查看 VM 状态:
    - **Azure 准备就绪**:Azure Migrate 建议评估中 vm 的 VM 大小和成本估计。
    - **准备好的条件**:显示问题和建议的修正。
    - **Azure 未准备就绪**:显示问题和建议的修正。
    - **准备情况未知**:由于数据可用性问题 Azure Migrate 无法评估准备情况时使用。

2. 单击**Azure 就绪**状态。 可以查看 VM 就绪情况详细信息, 并向下钻取以查看 VM 详细信息, 包括计算、存储和网络设置。



### <a name="review-cost-details"></a>查看成本详细信息

此视图显示在 Azure 中运行 Vm 的估计计算和存储成本。

1. 查看每月计算和存储成本。 评估组中所有 Vm 的成本都是聚合的。

    - 成本估算基于计算机的大小建议及其磁盘和属性。
    - 显示计算和存储的每月预估成本。
    - 成本估算适用于将本地 Vm 作为 IaaS Vm 运行。 Azure Migrate Server 评估不会考虑 PaaS 或 SaaS 成本。

2. 你可以查看每月存储成本估算。 此视图显示评估组的聚合存储成本, 在不同类型的存储磁盘上剥离。
3. 可以向下钻取以查看特定 Vm 的详细信息。


### <a name="review-confidence-rating"></a>查看置信度分级

运行基于性能的评估时, 会将置信度评级分配给评估。

![置信度分级](./media/how-to-create-assessment/confidence-rating.png)

- 从1星级 (最低) 到5星 (最高) 的评级为奖励。
- 置信度评级有助于你估计评估提供的大小建议的可靠性。
- 置信度评级以计算评估所需的数据点的可用性为基础。

评估的置信度评级如下所示。

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星




## <a name="next-steps"></a>后续步骤

- 了解如何使用[依赖关系映射](how-to-create-group-machine-dependencies.md)创建高可信度组。
- [详细了解](concepts-assessment-calculation.md)如何计算评估。
