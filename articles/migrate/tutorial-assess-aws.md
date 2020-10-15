---
title: 使用 Azure Migrate 服务器评估来评估要迁移到 Azure 的 AWS 实例
description: 介绍如何使用 Azure Migrate 服务器评估工具评估要迁移到 Azure 的 AWS 实例。
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 14928c8a3249cca172ad088f290b54a22a125ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90107969"
---
# <a name="tutorial-assess-aws-instances-for-migration-to-azure"></a>教程：评估 AWS 实例以便迁移到 Azure

在迁移到 Azure 的过程中，将评估本地工作负载，以衡量云的就绪性、确定风险以及估算成本和复杂性。

本文介绍如何使用 Azure Migrate 评估用于迁移到 Azure 的 Amazon Web Services (AWS) 实例：服务器评估”工具评估本地 VMware VM。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
- 基于计算机元数据和配置信息运行评估。
- 基于性能数据运行评估。

> [!NOTE]
> 教程显示尝试方案的最快路径，并尽可能使用默认选项。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>先决条件

- 在执行本教程中的步骤之前，请完成本系列的第一个教程：[发现本地库存](tutorial-discover-aws.md)。 
- 确保 AWS 实例未运行 Windows Server 2003 或 SUSE Linux。 不支持对这些计算机进行评估。


## <a name="decide-which-assessment-to-run"></a>确定要运行的评估


确定在运行评估时，所使用的大小调整标准是基于按原样在本地收集的计算机配置数据/元数据，还是基于动态的性能数据。

**评估** | **详细信息** | 建议
--- | --- | ---
**按本地原样** | 基于计算机配置数据/元数据进行评估。  | 建议的 Azure VM 大小基于本地 VM大小。<br/><br> 建议的 Azure 磁盘类型基于在评估的存储类型设置中选择的内容。
**基于性能** | 基于收集的动态性能数据进行评估。 | 建议的 Azure VM 大小基于 CPU 和内存利用率数据。<br/><br/> 建议的磁盘类型基于本地磁盘的 IOPS 和吞吐量。

## <a name="run-an-assessment"></a>运行评估

按如下述运行评估：

1. 在“服务器”页 >“Windows 和 Linux 服务器”中，单击“评估和迁移服务器”  。

   ![“评估和迁移服务器”按钮的位置](./media/tutorial-assess-aws/assess.png)

2. 在“Azure Migrate:服务器评估”中，单击“评估”。

    ![“评估”按钮的位置](./media/tutorial-assess-aws/assess-servers.png)

3. 在“评估服务器” > “评估类型”中，选择“Azure VM”  。
4. 在“发现源”中：

    - 如果使用设备发现了计算机，请选择“从 Azure Migrate 设备中发现的计算机”。
    - 如果使用导入的 CSV 文件发现了计算机，请选择“导入的计算机”。 
5. 指定评估的名称。 
6. 单击“全部查看”查看评估属性。

    ![用于查看评估属性的“查看全部”按钮的位置](./media/tutorial-assess-aws/assessment-name.png)

7. 在“评估属性” > “目标属性”中 ：
    - 在“目标位置”中，选择要迁移到的 Azure 区域。
        - 大小和成本建议基于你指定的位置。
        - 在 Azure 政府中，你可以以[这些区域](migrate-support-matrix.md#supported-geographies-azure-government)中的评估为目标
    - 在“存储类型”中，
        - 如果要在评估中使用基于性能的数据，请为 Azure Migrate 选择“自动”，以根据磁盘 IOPS 和吞吐量推荐存储类型。
        - 或者，选择在迁移 VM 时要使用的存储类型。
    - 在“预留实例”中，指定在迁移 VM 时是否要使用 VM 的“预留实例”。
        - 如果选择使用预留实例，则无法指定“折扣(%)”或“VM 运行时间” 。 
        - [了解详细信息](https://aka.ms/azurereservedinstances)。
8. 在“VM 大小”中：
 
    - 在“大小调整条件”中，选择进行评估时是基于计算机配置数据/元数据，还是以基于性能的数据为基础。 如果使用性能数据：
        - 在“性能历史记录”中，指示要用于评估的数据持续时间
        - 在“百分位使用率”中，指定要用于性能示例的百分位数值。 
    - 在“VM 系列”中，指定要列入考虑的 Azure VM 系列。
        - 如果使用基于性能的评估，Azure Migrate 会为你建议一个值。
        - 根据需要调整设置。 例如，如果不需要将生产环境迁移到 Azure 中的 A 系列 VM，可以从系列的列表中排除 A 系列。
    - 在“舒适因子”中，指明要在评估过程中使用的缓冲区。 此帐户用于解决季节性使用情况、短期性能历史记录，以及未来使用量可能会增加等问题。 例如，如果使用舒适因子 2：详细信息 | 利用率 | 添加舒适因子 (2.0) 读取 IOPS | 100 | 200 写入 IOPS | 100 | 200 读取吞吐量 | 100 Mbps | 200 Mbps 写入吞吐量 | 100 Mbps | 200 Mbps  
   
9. 在“定价”中：
    - 在“产品/服务”中，如果已注册，请指定 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 “服务器评估”会估计该产品/服务的费用。
    - 在“货币”中，为帐户选择计费货币。
    - 在“折扣 (%)”中，添加基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。
    - 在“VM 运行时间”中，指定 VM 将运行的持续时间（每月天数/每天小时数）。
        - 这对于无法连续运行的 Azure VM 非常有用。
        - 成本估算基于指定的持续时间。
        - 默认为“每月 31 天/每天 24 小时”。

    - 在“EA 订阅”中，指定是否将企业协议 (EA) 订阅折扣考虑在内以进行成本估算。 
    - 在“Azure 混合权益”中，指定是否已有 Windows Server 许可证。 如果已有许可证，并且许可证具有 Windows Server 订阅的有效软件保障，则在将许可证引入 Azure 时，可以申请 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。

10. 如有更改，请单击“保存”。

    ![评估属性](./media/tutorial-assess-aws/assessment-properties.png)

11. 在“评估服务器”中，单击“下一个” 。
12. 在“选择要评估的计算机”中，选择“新建”并指定组名称 。 
13. 选择设备，然后选择要添加到组的 VM。 然后单击“下一步”。
14. 在“审阅 + 创建评估”中，查看评估详细信息，然后单击“创建评估”以创建组并运行评估。


    > [!NOTE]
    > 对于基于性能的评估，建议在开始发现后至少等待一天，然后再创建评估。 这为收集具有较高置信度的性能数据提供了时间。 理想情况下，在开始发现后，等待指定的性能持续时间（日/周/月）进行高置信度评级。

## <a name="review-an-assessment"></a>查看评估

评估描述：

- **Azure 迁移就绪性**：VM 是否适合迁移到 Azure。
- **每月成本估算**：在 Azure 中运行 VM 的估算每月计算和存储成本。
- **每月存储成本估算**：迁移后的磁盘存储估算成本。

若要查看评估，请执行以下操作：

1. 在“服务器” > “Azure Migrate: 服务器评估”中，单击“评估”旁边的数字。
2. 在“评估”中，选择某项评估将其打开。 例如（估算和费用仅用于示例）： 

    ![评估摘要](./media/tutorial-assess-aws/assessment-summary.png)

3. 查看评估摘要。 你还可以编辑评估属性，或重新计算评估。
 
 
### <a name="review-readiness"></a>查看就绪情况

1. 单击“Azure 迁移就绪性”。
2. 在“Azure 迁移就绪性”中，查看 VM 状态：
    - **已做好 Azure 迁移准备**：在 Azure Migrate 针对评估中的 VM 建议 VM 大小并显示估算成本时使用。
    - **准备就绪但存在以下状况**：显示问题和建议的补救措施。
    - **尚未做好 Azure 迁移准备**：显示问题和建议的补救措施。
    - **就绪性未知**：当数据可用性问题导致 Azure Migrate 无法评估就绪性时使用。

3. 选择某种“Azure 迁移就绪性”状态。 可以查看 VM 就绪详情。 还可以深入查看 VM 详细信息，包括计算、存储和网络设置。

### <a name="review-cost-estimates"></a>查看成本估算

评估摘要显示估算出来的在 Azure 中运行 VM 的计算和存储成本。 

1. 查看每月总成本。 将聚合评估的组中所有 VM 的成本。

    - 成本估算基于计算机及其磁盘和属性的大小建议。
    - 将显示估算的每月计算和存储成本。
    - 估算成本用于在 Azure VM 上运行本地 VM。 该估算不考虑 PaaS 或 SaaS 成本。

2. 查看每月存储成本。 此视图显示评估的组的聚合存储成本，按不同类型的存储磁盘划分。 
3. 可以深入查看特定 VM 的成本详细信息。

### <a name="review-confidence-rating"></a>查看置信度分级

服务器评估为基于性能的评估分配置信度评级。 评级从一星（最低）到五星（最高）。

![置信度分级](./media/tutorial-assess-aws/confidence-rating.png)

置信度评级用于估计评估中的大小建议的可靠性。 此评级基于对评估进行计算时所需数据点的可用性。

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
- 设置[基于代理](how-to-create-group-machine-dependencies.md)的依赖项映射。
