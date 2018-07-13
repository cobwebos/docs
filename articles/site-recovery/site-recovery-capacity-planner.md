---
title: 估算 Azure 中的复制容量 | Microsoft 文档
description: 参考本文估算使用 Azure Site Recovery 进行复制时可用的容量
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: nisoneji
ms.openlocfilehash: d177dae71cd0787a343bbada7c900aebdbad86cc
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920655"
---
# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>计划用于使用 Site Recovery 保护 Hyper-V VM 的容量

[适用于 Hyper-V 到 Azure 部署的 Azure Site Recovery 部署规划器](site-recovery-hyper-v-deployment-planner.md)的新增强版本现已可用。 它取代了旧工具。 使用新工具规划部署。
该工具提供以下指导原则：

* 根据磁盘数量、磁盘大小、IOPS、变动量和一些 VM 特征评估 VM 的合格性
* 网络带宽需求与 RPO 评估
* Azure 基础结构需求
* 本地基础结构需求
* 初始复制批处理指导原则
* 灾难恢复到 Azure 的总估计成本


Azure Site Recovery Capacity Planner 工具可帮助用户确定通过 Azure Site Recovery 复制 Hyper-V VM 时的容量要求。

使用 Site Recovery Capacity Planner 可以分析源环境和工作负荷。 它可以帮助估计带宽需求、源位置所需的服务器资源，以及目标位置所需的资源（VM 和存储等）。

可以通过两种模式运行该工具：

* **快速规划**：在此模式下运行该工具时，可以根据 VM 平均数、磁盘、存储和更改率来获取网络和服务器投影。
* **详细规划**：提供 VM 级别每个工作负荷的详细信息。 分析 VM 兼容性，获取网络和服务器投影。

## <a name="before-you-start"></a>开始之前

* 收集环境信息，包括 VM 数、每个 VM 的磁盘数、每个磁盘的存储空间。
* 确定已复制数据的每日更改（改动）率。 请下载 [Hyper-V 容量计划工具](https://www.microsoft.com/download/details.aspx?id=39057)来获取更改率。 [详细了解](site-recovery-capacity-planning-for-hyper-v-replication.md)此工具。 我们建议运行此工具一周以上的时间以获取平均值。


## <a name="run-the-quick-planner"></a>运行快速规划器
1. 下载并打开 [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 工具。 需要运行宏。 在出现提示时，请选择启用编辑内容的选项。

2. 在“选择规划器类型”列表框中，选择“快速规划器”。

   ![入门](./media/site-recovery-capacity-planner/getting-started.png)

3. 在“Capacity Planner”工作表中输入所需信息。 必须填写以下屏幕快照中标有红圈的所有字段：

   a. 在“选择方案”中，选择“从 Hyper-V 到 Azure”或“从 VMware/物理设备到 Azure”。

   b. 在“平均每日数据更改率(%)”中输入使用 [Hyper-V 容量计划工具](site-recovery-capacity-planning-for-hyper-v-replication.md)或 [Site Recovery Deployment Planner](./site-recovery-deployment-planner.md) 收集的信息。

   c. 将 Hyper-V VM 复制到 Azure 时，不使用“压缩”设置。 对于压缩，请使用第三方应用程序（例如 Riverbed）。

   d. 在“保留天数”中，指定副本应保留的天数。

   e. 在“完成成批虚拟机的初始复制所需的小时数”和“初始复制批处理的虚拟机数”中，可以输入用于计算初始复制要求的设置。 部署 Site Recovery 时，应上传整个初始数据集。

   ![输入](./media/site-recovery-capacity-planner/inputs.png)

4. 输入源环境的值以后，显示的输出将包括：

   * **增量复制所需的带宽(MB/秒)**：根据每日数据更改率的平均值计算增量复制的网络带宽。
   * **初始复制所需的带宽(MB/秒)**：根据输入的初始复制值计算初始复制的网络带宽。
   * **所需存储空间(GB)**：所需的总 Azure 存储空间。
   * **标准存储帐户的总 IOPS**：根据所有标准存储帐户的 8K IOPS 单元大小计算的数字。 对于快速规划器，该数字根据所有源 VM 磁盘数以及每日数据更改率来计算。 对于详细规划器，该数字根据映射到标准 Azure VM 的 VM 总数以及这些 VM 的数据更改率来计算。
   * **所需的标准存储帐户数**：提供对 VM 进行保护所需的标准存储帐户的总数。 标准存储帐户最高可以在标准存储的所有 VM 中保持 20000 IOPS。 每个磁盘支持的最大 IOPS 为 500
   * **所需 Blob 磁盘数**：要在 Azure 存储中创建的磁盘数。
   * **所需高级存储帐户数**：对 VM 进行保护所需的高级存储帐户的总数。 IOPS 较高（高于 20,000）的源 VM 需要高级存储帐户。 高级存储帐户最高可支持 80,000 IOPS。
   * **高级存储的总 IOPS**：根据高级存储帐户总数按 256K 的 IOPS 单元大小计算的数字。 对于快速规划器，该数字根据所有源 VM 磁盘数以及每日数据更改率来计算。 对于详细规划器，该数字根据映射到高级 Azure VM（DS 和 GS 系列）的 VM 总数以及这些 VM 的数据更改率来计算。
   * **所需配置服务器数**：显示需要多少个配置服务器才能完成该部署。
   * **所需其他进程服务器数**：显示除了默认在配置服务器上运行的进程服务器外，是否还需要其他进程服务器。
   * **100% 源的其他存储**：显示源位置是否需要其他存储。

      ![输出](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>运行详细规划器

1. 下载并打开 [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 工具。 需要运行宏。 在出现提示时，请选择启用编辑内容的选项。

2. 在“选择规划器类型”中，从列表框选择“详细规划器”。

   ![入门指南](./media/site-recovery-capacity-planner/getting-started-2.png)

3. 在“工作负荷限定”工作表中输入所需的信息。 必须填写所有标记的字段。

   a. 在“处理器内核数”中指定源服务器上的内核总数。

   b. 在“内存分配(MB)”中指定源服务器的 RAM 大小。

   c. **NIC 数**：指定源服务器上的网络适配器数。

   d. 在“总存储空间(GB)”中指定 VM 存储空间的总大小。 例如，如果源服务器有 3 个磁盘，每个磁盘的大小为 500 GB，则总的存储空间大小为 1500 GB。

   e. 在“附加磁盘数”中指定源服务器的磁盘总数。

   f. 在“磁盘容量使用率”中指定平均使用率。

   g. 在“每日数据更改率(%)”中指定源服务器的每日数据更改率。

   h. 在“映射 Azure VM 大小”中输入要映射的 Azure VM 大小。 如果不想手动执行此操作，请选择“计算 IaaS VM”。 如果输入了手动设置，然后单击“计算 IaaS VM”，系统可能会覆盖手动设置。 计算过程会自动确定 Azure VM 大小的最佳匹配项。

   ![工作负荷限定工作表](./media/site-recovery-capacity-planner/workload-qualification.png)

4. 如果选择“计算 IaaS VM”，则会执行以下操作：

   * 验证必需的输入。
   * 计算 IOPS，并为每个可以复制到 Azure 的 VM 提供最佳 Azure VM 大小匹配项的建议。 如果系统检测不到合适的 Azure VM 大小，则会显示错误消息。 例如，如果附加的磁盘数为 65，则会显示错误消息，因为 Azure VM 的最大大小为 64。
   * 建议一个适用于 Azure VM 的存储帐户。
   * 计算工作负荷所需的标准存储帐户和高级存储帐户的总数。 向下滚动，以便查看可用于源服务器的 Azure 存储类型和存储帐户。
   * 根据为 VM 分配的所需存储类型（标准或高级）以及附加磁盘数完成表的其余部分并对其排序。 对于所有满足 Azure 要求的 VM，列“VM 是否合格?”显示“是”。 如果 VM 无法备份到 Azure，则会显示错误消息。

AA 到 AE 列为输出，提供的是每个 VM 的信息。

![输出列 AA 到 AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>示例
例如，如果有 6 个 VM，其值如表中所示，该工具会计算和分配最佳 Azure VM 匹配项以及 Azure 存储需求。

![工作负荷限定分配](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* 在示例输出中，请注意以下问题：

  * 第一列是用于 VM、磁盘和改动的验证列。
  * 5 个 VM 需要 2 个标准存储帐户和 1 个高级存储帐户。
  * VM3 不符合受保护资格，因为一个或多个磁盘大于 1 TB。
  * VM1 和 VM2 可以使用第一个标准存储帐户。
  * VM4 可以使用第二个标准存储帐户。
  * VM5 和 VM6 需要高级存储帐户，并且都可以使用单个帐户。

    > [!NOTE]
    > 标准存储和高级存储的 IOPS 在 VM 级别而非磁盘级别进行计算。 标准 VM 最多可处理每个磁盘 500 IOPS。 如果磁盘的 IOPS 大于 500，则需要高级存储。 如果某个磁盘的 IOPS 超出 500 但所有 VM 磁盘的 IOPS 仍在标准 Azure VM 的支持范围内，则规划器会选取标准 VM 而不是 DS 或 GS 系列。 （Azure VM 限制包括 VM 大小、磁盘数、适配器数、CPU 和内存。）需要使用相应的 DS 或 GS 系列 VM 来手动更新“映射 Azure 大小”单元格。


输入所有信息后，选择“将数据提交到规划器工具”打开 Capacity Planner。 工作负荷会突出显示，以表明其是否符合受保护资格。

### <a name="submit-data-in-capacity-planner"></a>在 Capacity Planner 中提交数据
1. 打开“Capacity Planner”工作表时，该表会根据你所指定的设置进行填充。 “工作负荷”一词会出现在“基础输入源”单元格中，用来表示输入是“工作负荷限定”工作表。

2. 如果想要进行更改，则需修改“工作负荷限定”工作表。 然后再次选择“将数据提交到规划器工具”。

   ![容量规划器](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>后续步骤
[了解如何运行](site-recovery-capacity-planning-for-hyper-v-replication.md)容量计划工具。
