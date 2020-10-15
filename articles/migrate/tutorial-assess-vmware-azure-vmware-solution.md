---
title: 使用 Azure Migrate 评估要迁移到 Azure VMware 解决方案 (AVS) 的 VMware VM
description: 了解如何使用 Azure Migrate 服务器评估来评估要迁移到 AVS 的 VMware VM。
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 29f7f824d96aedd80e490ba84c390be4d9493683
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604234"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>教程：评估 VMware VM 以便迁移到 AVS

在迁移到 Azure 的过程中，将评估本地工作负载，以衡量云的就绪性、确定风险以及估算成本和复杂性。

本文介绍如何使用 Azure Migrate 来评估发现的 VMware 虚拟机 (VM)，以便迁移到 Azure VMware 解决方案 (AVS)：服务器评估”工具评估本地 VMware VM。 AVS 是一项托管服务，可用于在 Azure 中运行 VMware 平台。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
- 基于计算机元数据和配置信息运行评估。
- 基于性能数据运行评估。

> [!NOTE]
> 教程显示尝试方案的最快路径，并尽可能使用默认选项。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。



## <a name="prerequisites"></a>先决条件

在按照本教程评估用于迁移到 AVS 的计算机之前，请确保已发现要评估的计算机：

- 若要使用 Azure Migrate 设备发现计算机，请[遵循本教程](tutorial-discover-vmware.md)。 
- 若要使用导入的 CSV 文件发现计算机，请[遵循本教程](tutorial-discover-import.md)。


## <a name="decide-which-assessment-to-run"></a>确定要运行的评估


确定在运行评估时，所使用的大小调整标准是基于按原样在本地收集的计算机配置数据/元数据，还是基于动态的性能数据。

**评估** | **详细信息** | 建议
--- | --- | ---
**按本地原样** | 基于计算机配置数据/元数据进行评估。  | AVS 中建议的节点大小基于本地 VM 大小，以及在评估中针对节点类型、存储类型和允许的故障数设置所指定的设置。
**基于性能** | 基于收集的动态性能数据进行评估。 | AVS 中建议的节点大小基于 CPU 和内存利用率数据，以及在评估中针对节点类型、存储类型和允许的故障数设置所指定的设置。

## <a name="run-an-assessment"></a>运行评估

按如下述运行评估：

1. 在“服务器”页 >“Windows 和 Linux 服务器”中，单击“评估和迁移服务器”  。

   ![“评估和迁移服务器”按钮的位置](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

2. 在 **Azure Migrate:** 服务器评估”中，单击“评估”。

3. 在“评估服务器” > “评估类型”中，选择“Azure VMware 解决方案 (AVS)（预览版）”  。
4. 在“发现源”中：

    - 如果使用设备发现了计算机，请选择“从 Azure Migrate 设备中发现的计算机”。
    - 如果使用导入的 CSV 文件发现了计算机，请选择“导入的计算机”。 
    
5. 指定评估的名称。 
6. 单击“全部查看”查看评估属性。

    ![用于选择评估设置的页面](./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png)


7. 在“评估属性” > “目标属性”中 ：

    - 在“目标位置”中，选择要迁移到的 Azure 区域。
       - 大小和成本建议基于你指定的位置。
       - 目前可评估三个区域（美国东部、美国西部、西欧）
   - 在“存储类型”中，保留“vSAN” 。 这是用于 AVS 私有云的默认存储类型。
   - 预留实例当前不支持 AVS 节点。
8. 在“VM 大小”中：
    - 在“节点类型”中，根据在本地 VM 上运行的工作负载选择节点类型。
        - Azure Migrate 会建议将 VM 迁移到 AVS 时所需的节点。
        - 默认节点类型为 AV36。
    - 在“FTT 设置，RAID 级别”中，选择“允许的故障数”和“RAID”的组合。  选定的 FTT 选项，与本地 VM 磁盘要求共同决定 AVS 中所需的总 vSAN 存储。
    - 在“CPU 超额订阅”中，指定与 AVS 节点中一个物理内核关联的虚拟核心的比率。 超过 4:1 的超额订阅可能会导致性能降低，但可应用于 Web 服务器类型的工作负载。

9. 在“节点大小”中： 
    - 在“大小调整条件”中，选择进行评估时是基于静态元数据，还是以基于性能的数据为基础。 如果使用性能数据：
        - 在“性能历史记录”中，指示要用于评估的数据持续时间
        - 在“百分位使用率”中，指定要用于性能示例的百分位数值。 
    - 在“舒适因子”中，指明要在评估过程中使用的缓冲区。 此帐户用于解决季节性使用情况、短期性能历史记录，以及未来使用量可能会增加等问题。 例如，如果使用舒适因子 2：
    
        **组件** | **有效利用率** | **添加舒适因子 (2.0)**
        --- | --- | ---  
        核心数 | 2 | 4
        内存 | 8 GB | 16 GB     

10. 在“定价”中：
    - 在“产品/服务”中，会显示已注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)，“服务器评估”会估计该产品/服务的费用。
    - 在“货币”中，为帐户选择计费货币。
    - 在“折扣 (%)”中，添加基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。

11. 如有更改，请单击“保存”。

    ![评估属性](./media/tutorial-assess-vmware-azure-vmware-solution/view-all.png)

12. 在“评估服务器”中，单击“下一个” 。
13. 在“评估服务器” > “选择要评估的计算机”中，若要创建新的服务器组用于评估，请选择“新建”，并指定组名称  。 
14. 选择设备，然后选择要添加到组的 VM。 然后单击“下一步”。
15. 在“审阅 + 创建评估”中，查看评估详细信息，然后单击“创建评估”以创建组并运行评估 。

    > [!NOTE]
    > 对于基于性能的评估，建议在开始发现后至少等待一天，然后再创建评估。 这为收集具有较高置信度的性能数据提供了时间。 理想情况下，在开始发现后，等待指定的性能持续时间（日/周/月）进行高置信度评级。

## <a name="review-an-assessment"></a>查看评估

AVS 评估描述：

- AVS 就绪情况：本地 VM 是否适合迁移到 Azure VMware 解决方案 (AVS)。
- AVS 节点数：运行 VM 所需的 AVS 节点的估计数目。
- 跨 AVS 节点的利用率：跨所有节点的预计的 CPU、内存和存储利用率。
- 每月成本估算：运行本地 VM 的所有 Azure VMware 解决方案 (AVS) 节点的每月估算成本。

## <a name="view-an-assessment"></a>查看评估

若要查看评估，请执行以下操作：

1. 在“服务器” > “Azure Migrate: 服务器评估”中，单击“评估”旁边的数字。
2. 在“评估”中，选择某项评估将其打开。 
3. 查看评估摘要。 你还可以编辑评估属性，或重新计算评估。
 

### <a name="review-readiness"></a>查看就绪情况

1. 单击“Azure 迁移就绪性”。
2. 在“Azure 迁移就绪性”中，查看 VM 状态。

    - **AVS 已就绪**：计算机可以照原样迁移到 Azure AVS，无需任何更改。 计算机将在 AVS 中启动，并提供完整的 AVS 支持。
    - **准备就绪但存在以下状况**：计算机可能出现与当前 vSphere 版本的兼容性问题。 在 AVS 中获得全部功能之前，可能需要先安装 VMware 工具或其他设置。
    - **AVS 未就绪**：VM 不会在 AVS 中启动。 例如，如果本地 VMware VM 附加了外部设备（如 CD-ROM），并且你正在使用 VMware VMotion，则 VMotion 操作会失败。
 - **就绪性未知**：由于从本地环境收集的元数据不足，Azure Migrate 无法确定计算机就绪情况。

3. 查看建议的工具。

    - VMware HCX 或 Enterprise：对于 VMware 计算机，VMWare 混合云扩展 (HCX) 解决方案是将本地工作负荷迁移到 Azure VMware 解决方案 (AVS) 私有云的推荐迁移工具。 了解详情。
    - 未知：对于通过 CSV 文件导入的计算机，默认迁移工具是未知的。 但对于 VMware 计算机，建议使用 VMware 混合云扩展 (HCX) 解决方案。
4. 单击某种“AVS 迁移就绪性”状态。 可以查看 VM 就绪性详细信息，并深入查看 VM 详细信息，包括计算、存储和网络设置。

### <a name="review-cost-estimates"></a>查看成本估算

评估摘要显示估算出来的在 Azure 中运行 VM 的计算和存储成本。 

1. 查看每月总成本。 将聚合评估的组中所有 VM 的成本。

    - 成本估算基于所需的 AVS 节点数，并考虑所有 VM 的总资源要求。
    - 由于 AVS 是按节点定价的，因此总成本不含计算成本和存储成本。
    - 估算成本适用于在 AVS 上运行的本地 VM。 Azure Migrate 服务器评估不考虑 PaaS 或 SaaS 成本。

2. 查看每月存储估算。 此视图显示评估的组的聚合存储成本，按不同类型的存储磁盘划分。 
3. 可以深入查看特定 VM 的成本详细信息。

### <a name="review-confidence-rating"></a>查看置信度分级

服务器评估为基于性能的评估分配置信度评级。 评级从一星（最低）到五星（最高）。

![置信度分级](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

置信度评级用于判断评估中大小建议的可靠性。 此评级基于对评估进行计算时所需数据点的可用性。

> [!NOTE]
> 如果基于 CSV 文件创建评估，则不会分配置信度评级。

置信度评级如下所示。

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星

[详细了解](concepts-assessment-calculation.md#confidence-ratings-performance-based)置信度评级。

## <a name="next-steps"></a>后续步骤

- 使用[依赖项映射](concepts-dependency-visualization.md)查找计算机依赖项。
- 设置[无代理](how-to-create-group-machine-dependencies-agentless.md)或[基于代理](how-to-create-group-machine-dependencies.md)的依赖项映射。
