---
title: 在 Azure（大型实例）上，SAP HANA 的故障转移过程到灾难站点 |微软文档
description: 如何在 Azure 上对 SAP HANA 的灾难恢复站点执行故障转移（大型实例）
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fe3ee79318ab9fdc9f2c0e9585051439b76b5cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617144"
---
# <a name="disaster-recovery-failover-procedure"></a>灾难恢复故障转移过程


>[!IMPORTANT]
>本文不能替代 SAP HANA 管理文档或 SAP 说明。 我们希望您在 SAP HANA 管理和操作方面有扎实的了解和专业知识，尤其是在备份、恢复、高可用性和灾难恢复 （DR） 方面。 在本文中，将显示 SAP HANA 工作室的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。

故障转移到 DR 站点时需要考虑两种情况：

- 需要将 SAP HANA 数据库返回到最新状态的数据。 在这种情况下，有一个自助服务脚本，您可以使用该脚本执行故障转移，而无需与 Microsoft 联系。 对于故障恢复，您需要与 Microsoft 合作。
- 您希望还原到不是最新复制快照的存储快照。 对于这种情况，需要与 Microsoft 合作。 

>[!NOTE]
>必须在表示 DR 单元的 HANA 大型实例单元上执行以下步骤。 
 
要还原到最新的复制存储快照，请按照 Microsoft 快照工具中为[Azure 上的 SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)执行"完全 DR 故障转移 - azure_hana_dr_failover"中的步骤。 

如果要使多个 SAP HANA 实例失败，则多次运行azure_hana_dr_failover命令。 请求时，输入要故障转移和恢复的 SAP HANA SID。 


您还可以在不影响实际复制关系的情况下测试 DR 故障转移。 要执行测试故障转移，请按照[在 Azure 上为 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)中的"执行测试 DR 故障转移 - azure_hana_test_dr_failover"中的步骤操作。 

>[!IMPORTANT]
>*不要*通过**测试故障转移**在 DR 站点中创建的实例上运行任何生产事务。 命令azure_hana_test_dr_failover创建一组与主站点没有关系的卷。 结果是无法同步回主站点。** 

如果要测试多个 SAP HANA 实例，则多次运行脚本。 请求时，输入要测试故障转移的实例的 SAP HANA SID。 

>[!NOTE]
>如果需要故障转移到 DR 站点以拯救几小时前删除并需要将 DR 卷设置为较早的快照的一些数据，则此过程适用。 

1. 关闭正在运行的 HANA 大型实例的灾难恢复单元上的 HANA 非生产实例。 预装休眠的 HANA 生产实例。
1. 确保未运行任何 SAP HANA 进程。 使用此检查使用以下命令：

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      输出应显示处于已停止状态的**hdbdaemon**进程，并且没有其他 HANA 进程处于运行或启动状态。
1. 确定希望将灾难恢复站点还原到的快照名称或 SAP HANA 备份 ID。 在真实的灾难恢复事例中，此快照通常是最新的快照。 如果需要恢复丢失的数据，请选择更早的快照。
1. 通过高优先级支持请求联系 Azure 支持部门。 请求还原该快照，并还原快照的名称和日期或 DR 站点上的 HANA 备份 ID。 默认情况下，运营团队只会还原 /hana/data 卷。 如果也希望有 /hana/log 备份卷，则需要专门说明这一点。 不要还原 /hana/shared 卷。** 相反，在重新装入 PRD 的 /hana/shared 卷后，从 **.snapshot**目录及其子目录中选择特定文件，如 global.ini。 

   在操作端，将执行以下步骤：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 停止生产卷到灾难恢复卷的快照复制。 如果生产站点服务中断是需要执行灾难恢复过程的原因，则此中断可能已经发生。
   
   b.保留“数据库类型”设置，即设置为“共享”。 在灾难恢复卷上还原所选的存储快照或具有备份 ID 的快照。
   
   c. 还原后，灾难恢复卷可装载到灾难恢复区域中的 HANA 大型实例单元。
      
1. 将灾难恢复卷装载到灾难恢复站点中的 HANA 大型实例单元。 
1. 启动处于休眠状态的 SAP HANA 生产实例。
1. 如果选择复制事务日志备份日志以减少 RPO 时间，请将事务日志备份合并到新安装的 DR/hana/日志备份目录中。 不要覆盖现有备份。 复制未使用存储快照的最新复制复制的较新的备份。
1. 您还可以从未复制到 DR Azure 区域中的 /hana/shared/PRD 卷的快照中还原单个文件。

以下步骤演示如何基于还原的存储快照和可用的事务日志备份恢复 SAP HANA 生产实例。

1. 使用 SAP HANA Studio 将备份位置更改为 **/hana/logbackups**。

   ![更改 DR 恢复的备份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA 会逐个扫描备份文件位置并建议要还原到的最新事务日志备份。 扫描可能需要几分钟时间，直到出现如下所示的屏幕：

   ![用于 DR 恢复的事务日志备份列表](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 调整一些默认设置：

      - 清除“使用增量备份”。****
      - 选择“初始化日志区域”。****

   ![设置“初始化日志区域”](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. 选择“完成”****。

   ![完成 DR 还原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

应显示如此处所示的进度窗口。 请记住，此为 SAP HANA 三节点横向扩展配置的灾难恢复还原示例。

![还原进度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果还原停止在 **"完成"** 屏幕响应，并且未显示进度屏幕，请确认辅助节点上的所有 SAP HANA 实例都在运行。 如有必要，请手动启动 SAP HANA 实例。


## <a name="failback-from-a-dr-to-a-production-site"></a>从 DR 站点故障回复到生产站点
可以从 DR 站点故障回复到生产站点。 让我们探讨以下场景：到灾难恢复站点的故障转移是由生产 Azure 区域中的问题引起，而不是因为需要恢复丢失的数据。 

您在灾难恢复站点中运行 SAP 生产工作负载已有一段时间了。 随着生产站点中问题的解决，需要故障回复到生产站点。 由于不能丢失数据，因此回到生产站点这一过程涉及到几个步骤且需要与 Azure 上的 SAP HANA 运营团队进行紧密协作。 问题解决后，需要联系运营团队，以便开始同步回到生产站点。

执行以下步骤:

1. Azure 上的 SAP HANA 运营团队获取从灾难恢复存储卷同步生产存储卷（现表示生产状态）的触发指令。 在此状态中，生产站点中的 HANA 大型实例单元处于关闭状态。
1. Azure 操作团队的 SAP HANA 监视复制，并确保在它们通知您之前将其捕获。
1. 需要关闭在灾难恢复站点使用 HANA 生产实例的应用程序。 然后执行 HANA 事务日志备份。 接下来，您停止在灾难恢复站点的 HANA 大型实例单元上运行的 HANA 实例。
1. 关闭灾难恢复站点中 HANA 大型实例单元中的 HANA 实例后，操作团队将再次手动同步磁盘卷。
1. Azure 操作团队的 SAP HANA 在生产站点中再次启动 HANA 大型实例单元。 他们把它交给你 在 HANA 大型实例单元的启动时间，请确保 SAP HANA 实例处于关闭状态。
1. 执行与以前故障转移到灾难恢复站点时相同的数据库还原步骤。

## <a name="monitor-disaster-recovery-replication"></a>监视灾难恢复复制

要监视存储复制进度的状态，请运行脚本`azure_hana_replication_status`。 此命令必须从在灾难恢复位置运行的单位运行，才能按预期运行。 无论复制是否处于活动状态，该命令都有效。 可以为灾难恢复位置的租户的每个 HANA 大型实例单元运行该命令。 它不能用于获取有关引导卷的详细信息。 

有关命令及其输出的详细信息，请参阅[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)中"获取 DR 复制状态 - azure_hana_replication_status"。


## <a name="next-steps"></a>后续步骤
- 请参阅[从 HANA 侧进行监视和故障排除](hana-monitor-troubleshoot.md)。
