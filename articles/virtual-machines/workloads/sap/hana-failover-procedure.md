---
title: 到 Azure SAP HANA（大型实例）的灾难站点的 HANA 故障转移过程 | Microsoft Docs
description: 如何执行到 Azure SAP HANA（大型实例）灾难恢复站点的故障转移
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
ms.openlocfilehash: 6454903a7c37da30e317e29c126109b39b14efbc
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660577"
---
# <a name="disaster-recovery-failover-procedure"></a>灾难恢复故障转移过程


>[!IMPORTANT]
>本文不是 SAP HANA 管理文档或 SAP 说明的替代文档。 我们假定你已深入了解 SAP HANA 管理和操作的专业技能，尤其是在备份、还原、高可用性和灾难恢复 (DR) 方面的专业技能。 本文中提供的是 SAP HANA Studio 的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。

故障转移到 DR 站点时，有两种情况需要考虑：

- 需要将 SAP HANA 数据库返回到最新状态的数据。 在这种情况下，使用自助服务脚本可以执行故障转移，无需联系 Microsoft。 若要进行故障回复，你需要与 Microsoft 合作。
- 你希望还原不是最新复制快照的存储快照。 对于这种情况，需要与 Microsoft 合作。 

>[!NOTE]
>以下步骤必须在表示 DR 单元的 HANA 大型实例单元上执行。 
 
若要还原到最新的复制存储快照，请按照[适用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)的“执行完整 DR 故障转移 - azure_hana_dr_failover”部分中的步骤进行操作。 

如果想要故障转移多个 SAP HANA 实例，请多次运行 azure_hana_dr_failover 命令。 根据请求输入想要故障转移和还原的 SAP HANA SID。 


你还可以测试 DR 故障转移，这不会影响实际的复制关系。 若要执行测试性故障转移，请按照[适用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)的“执行测试性 DR 故障转移 - azure_hana_test_dr_failover”部分中的步骤进行操作。 

>[!IMPORTANT]
>在**测试故障转移**的整个过程中，不要在 DR 站点中创建的实例上运行任何生产事务。 azure_hana_test_dr_failover 命令会创建一组卷，这些卷与主站点没有任何关系。 结果是无法同步回主站点。 

如果想要测试多个 SAP HANA 实例，请多次运行脚本。 根据请求输入想要测试故障转移的实例的 SAP HANA SID。 

>[!NOTE]
>如果需要故障转移到 DR 站点，以修复一些几小时前删除的数据，并因此需要将 DR 卷设置为早于最新快照的状态，则此过程适用。 

1. 关闭在 HANA 大型实例的灾难恢复单元上运行的 HANA 非生产实例。 已预安装了一个处于休眠状态的 HANA 生产实例。
1. 确保未运行任何 SAP HANA 进程。 使用以下命令执行此项检查：

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList` 列中的一个值匹配。

      输出应显示 **hdbdaemon** 进程处于停止状态且没有其他 HANA 进程处于运行或已开始状态。
1. 确定希望将灾难恢复站点还原到的快照名称或 SAP HANA 备份 ID。 在真实的灾难恢复事例中，此快照通常是最新的快照。 如果需要恢复丢失的数据，请选择更早的快照。
1. 通过高优先级支持请求联系 Azure 支持部门。 请求在 DR 站点上还原该快照，并提供快照的名称和日期或提供 HANA 备份 ID。 默认情况下，运营团队只会还原 /hana/data 卷。 如果还想要还原 / hana/logbackups 卷，需要特意提出此要求。 不要还原 /hana/shared 卷。 改为在重新装载 PRD 的 /hana/shared 卷之后选择特定的文件，例如， **.snapshot** 及其子目录中的 global.ini。 

   在操作端，将执行以下步骤：

   a. 停止生产卷到灾难恢复卷的快照复制。 如果生产站点服务中断是需要执行灾难恢复过程的原因，则此中断可能已经发生。
   
   b. 在灾难恢复卷上还原所选的存储快照或具有备份 ID 的快照。
   
   c. 还原后，灾难恢复卷可装载到灾难恢复区域中的 HANA 大型实例单元。
      
1. 将灾难恢复卷装载到灾难恢复站点中的 HANA 大型实例单元。 
1. 启动处于休眠状态的 SAP HANA 生产实例。
1. 如果已选择复制事务日志备份日志以缩短 RPO 时间，请将事务日志备份合并到新装载的 DR /hana/logbackups 目录。 不要覆盖现有备份。 复制尚未使用存储快照的最新复制内容进行复制的较新备份。
1. 还可以还原未复制到 DR Azure 区域的 /hana/shared/PRD 卷的快照中的单个文件。

下面的步骤展示了如何基于还原的存储快照和可用的事务日志备份恢复 SAP HANA 生产实例。

1. 使用 SAP HANA Studio 将备份位置更改为 **/hana/logbackups**。

   ![更改 DR 恢复的备份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA 会逐个扫描备份文件位置并建议要还原到的最新事务日志备份。 扫描可能需要花费几分钟时间，完成后会显示如下所示的屏幕：

   ![DR 恢复的事务日志备份列表](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 调整一些默认设置：

      - 清除“使用增量备份”。
      - 选择“初始化日志区域”。

   ![设置“初始化日志区域”](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. 选择“完成”。

   ![完成 DR 还原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

应显示如此处所示的进度窗口。 请记住，此为 SAP HANA 三节点横向扩展配置的灾难恢复还原示例。

![还原进度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果还原过程在“完成”屏幕上停止响应且不显示进度屏幕，请确认辅助角色节点上的所有 SAP HANA 实例是否正常运行。 如有必要，请手动启动 SAP HANA 实例。


## <a name="failback-from-a-dr-to-a-production-site"></a>从 DR 站点故障回复到生产站点
可以从 DR 站点故障回复到生产站点。 让我们探讨以下场景：到灾难恢复站点的故障转移是由生产 Azure 区域中的问题引起，而不是因为需要恢复丢失的数据。 

你已在灾难恢复站点中运行了 SAP 生产工作负荷一段时间。 随着生产站点中问题的解决，需要故障回复到生产站点。 由于不能丢失数据，因此回到生产站点这一过程涉及到几个步骤且需要与 Azure 上的 SAP HANA 运营团队进行紧密协作。 问题解决后，需要联系运营团队，以便开始同步回到生产站点。

执行以下步骤:

1. Azure 上的 SAP HANA 运营团队获取从灾难恢复存储卷同步生产存储卷（现表示生产状态）的触发指令。 在此状态中，生产站点中的 HANA 大型实例单元处于关闭状态。
1. Azure SAP HANA 运营团队会监视复制过程并确保在通知你之前实现跟进。
1. 需要关闭在灾难恢复站点使用 HANA 生产实例的应用程序。 然后执行 HANA 事务日志备份。 接下来，停止灾难恢复站点中 HANA 大型实例单元上正在运行的 HANA 实例。
1. 关闭灾难恢复站点中 HANA 大型实例单元上正在运行的 HANA 实例后，运营团队再次手动同步磁盘卷。
1. Azure SAP HANA 运营团队再次启动生产站点中的 HANA 大型实例单元。 然后，他们将其交接给你。 你需要确保在 HANA 大型实例单元启动时，SAP HANA 实例处于关闭状态。
1. 与之前故障转移到灾难恢复站点时一样，执行相同的数据库还原步骤。

## <a name="monitor-disaster-recovery-replication"></a>监视灾难恢复复制

若要监视存储复制进度的状态，请运行 `azure_hana_replication_status` 脚本。 必须从在灾难恢复位置中运行的单元运行此命令，此命令才能按预期发挥作用。 无论复制是否处于活动状态，命令都有效。 可针对灾难恢复位置中租户的每个 HANA 大型实例单元运行此命令。 不能使用此脚本获取有关启动卷的详细信息。 

有关此命令及其输出的详细信息，请参阅[适用于 Azure SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)中的“获取 DR 复制状态 - azure_hana_replication_status”。


## <a name="next-steps"></a>后续步骤
- 请参阅[从 HANA 端进行监视和故障排除](hana-monitor-troubleshoot.md)。
