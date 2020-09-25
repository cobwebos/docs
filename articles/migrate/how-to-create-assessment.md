---
title: 使用 Azure Migrate Server 评估创建 Azure VM 评估 |Microsoft Docs
description: 介绍如何使用 Azure Migrate Server 评估工具创建 Azure VM 评估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: c4132ca675af136d7fd50b8ddd02277919a5ed28
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361086"
---
# <a name="create-an-azure-vm-assessment"></a>创建 Azure VM 评估

本文介绍如何使用 Azure Migrate：服务器评估为本地 VMware Vm 或 Hyper-v Vm 创建 Azure VM 评估。

[Azure Migrate](migrate-services-overview.md) 可帮助你迁移到 Azure。 Azure Migrate 提供了一个中心，用于跟踪 Azure 的本地基础结构、应用程序和数据的发现、评估以及迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。 

## <a name="before-you-start"></a>准备工作

- 请确保已 [创建](how-to-add-tool-first-time.md) Azure Migrate 项目。
- 如果已创建项目，请确保已 [添加](how-to-assess.md) Azure Migrate： Server 评估工具。
- 若要创建评估，需要为 [VMware](how-to-set-up-appliance-vmware.md) 或 [hyper-v](how-to-set-up-appliance-hyper-v.md)设置 Azure Migrate 设备。 设备将发现本地计算机，并将元数据和性能数据发送到 Azure Migrate：服务器评估。 [了解详细信息](migrate-appliance.md)。


## <a name="azure-vm-assessment-overview"></a>Azure VM 评估概述
可以使用两种类型的大小调整条件通过 Azure Migrate：服务器评估来创建 Azure VM 评估。

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据的评估 | **建议的 VM 大小**：基于 CPU 和内存利用率数据。<br/><br/> **建议的磁盘类型(标准或高级托管磁盘)** ：基于本地磁盘的 IOPS 和吞吐量。
**本地** | 基于本地大小的评估。 | **建议的 VM 大小**：基于本地 VM 大小<br/><br> **建议的磁盘类型**：基于为评估选择的存储类型设置。

[详细了解](concepts-assessment-calculation.md)评估。

## <a name="run-an-assessment"></a>运行评估

按如下述运行评估：

1. 查看有关创建评估的[最佳做法](best-practices-assessment.md)。
2. 在“服务器”选项卡上的“Azure Migrate: 服务器评估”磁贴中，单击“评估”。

    ![屏幕截图显示在 "评估工具" 下选择了评估的 Azure Migrate 服务器。](./media/how-to-create-assessment/assess.png)

3. 在“评估服务器”中，将评估类型选择为“Azure VM”，选择发现源并指定评估名称。

    ![评估基本信息](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. 单击“全部查看”查看评估属性。

    ![评估属性](./media/how-to-create-assessment//view-all.png)

5. 单击“下一步”以转到“选择要评估的计算机” 。 在“选择或创建组”中，选择“新建”并指定组名称。 组将要评估的一个或多个 VM 集合到一起。
6. 在“将计算机添加到组”中，选择要添加到该组的 VM。
7. 单击“下一步”以转到“查看 + 创建评估”以查看评估详细信息 。
8. 单击“创建评估”以创建该组，并运行评估。

    ![创建评估](./media/how-to-create-assessment//assessment-create.png)

9. 创建评估后，在“服务器” > “Azure Migrate: 服务器评估” > “评估”中查看它。
10. 单击“导出评估”，将评估下载为 Excel 文件。



## <a name="review-an-azure-vm-assessment"></a>查看 Azure VM 评估

Azure VM 评估说明：

- **Azure 迁移就绪性**：VM 是否适合迁移到 Azure。
- **每月成本估算**：在 Azure 中运行 VM 的估算每月计算和存储成本。
- **每月存储成本估算**：迁移后的磁盘存储估算成本。

### <a name="view-an-azure-vm-assessment"></a>查看 Azure VM 评估

1. 在“迁移目标” >  “服务器”中，单击“Azure Migrate:   服务器评估”中的“评估”。
2. 在“评估”中，单击某项评估将其打开。

    ![评估摘要](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>查看 Azure 迁移就绪性

1. 在“Azure 迁移就绪性”中，验证 VM 是否已准备好迁移到 Azure。
2. 查看 VM 状态：
    - **已做好 Azure 迁移准备**：对于评估中的 VM，Azure Migrate 将建议 VM 大小并显示估算成本。
    - **准备就绪但存在以下状况**：显示问题和建议的补救措施。
    - **尚未做好 Azure 迁移准备**：显示问题和建议的补救措施。
    - **就绪性未知**：当数据可用性问题导致 Azure Migrate 无法评估就绪性时使用。

3. 单击某种“Azure 迁移就绪性”状态。 可以查看 VM 就绪性详细信息，并深入查看 VM 详细信息，包括计算、存储和网络设置。



### <a name="review-cost-details"></a>查看成本详细信息

此视图显示在 Azure 中运行 VM 的估算计算和存储成本。

1. 查看每月计算和存储成本。 将聚合评估的组中所有 VM 的成本。

    - 估算成本是根据计算机的大小建议及其磁盘和属性给出的。
    - 将显示估算的每月计算和存储成本。
    - 估算成本适用于作为 IaaS VM 运行的本地 VM。 Azure Migrate 服务器评估不考虑 PaaS 或 SaaS 成本。

2. 可以查看每月存储估算成本。 此视图显示评估的组的聚合存储成本，按不同类型的存储磁盘划分。
3. 可以深入查看特定 VM 的详细信息。


### <a name="review-confidence-rating"></a>查看置信度分级

运行基于性能的评估时，会将一个置信度分级分配到评估。

![置信度分级](./media/how-to-create-assessment/confidence-rating.png)

- 将授予从 1 星（最低）到 5 星（最高）的分级。
- 置信度分级可帮助你判断评估所提供的大小建议的可靠性。
- 置信度分级基于计算评估所需的数据点的可用性。

评估的置信度分级如下。

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星




## <a name="next-steps"></a>后续步骤

- 了解如何使用 [依赖关系映射](how-to-create-group-machine-dependencies.md) 创建高可信度组。
- [详细了解](concepts-assessment-calculation.md)如何计算评估。
