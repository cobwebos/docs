---
title: Azure SAP HANA（大型实例）的灾难站点的 HANA 故障转移过程 | Microsoft Docs
description: 如何执行到 Azure SAP HANA（大型实例）灾难恢复站点的故障转移
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48bea302f2f8e878275e6a7c222d703e0f31f239
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2018
ms.locfileid: "44492804"
---
# <a name="disaster-recovery-failover-procedure"></a>灾难恢复故障转移过程


>[!IMPORTANT]
>本文档不是 SAP HANA 管理文档或 SAP 说明的替代文档。 读者应深入了解 SAP HANA 管理和操作的专业技能，尤其是在备份、还原和高可用性以及灾难恢复主题方面。 本文档中提供 SAP HANA Studio 的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。

故障转移到 DR 站点时，有两种情况需要考虑：

- 需要将 SAP HANA 数据库返回到最新状态的数据。 在这种情况下，使用自助服务脚本可以执行故障转移，无需联系 Microsoft。 但是，故障回复时需要与 Microsoft 合作。
- 希望还原不是最新复制快照的存储快照。 对于这种情况，需要与 Microsoft 合作。 

>[!NOTE]
>以下步骤需要在表示 DR 单元的 HANA 大型实例单元上执行。 
 
如需还原到最新的复制存储快照，请执行以下步骤： 

1. 关闭在 HANA 大型实例的灾难恢复单元上运行的 HANA 非生产实例。 这是因为，已预安装 HANA 休眠生产实例。
1. 确保未运行任何 SAP HANA 进程。 使用以下命令执行此项检查：`/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`。 输出应显示 **hdbdaemon** 进程处于停止状态且没有其他 HANA 进程处于运行或已开始状态。
1. 在 DR 站点 HANA 大型实例单元上, 执行脚本 *azure_hana_dr_failover.pl*。 脚本要求还原 SAP HANA SID。 根据请求键入一个或唯一一个已复制并保存在 DR 站点中的 HANA 大型实例单元上 *HANABackupCustomerDetails.txt* 文件中的 SAP HANA SID。 

      如果想要故障转移多个 SAP HANA 实例，则需要多次运行脚本。 根据请求键入想要故障转移和还原的 SAP HANA SID。 完成后，脚本会显示添加到 HANA 大型实例单元的卷的装载点列表。 此列表还包括已还原的 DR 卷。

1. 使用 Linux 操作系统命令，将已还原的灾难恢复卷装载到灾难恢复站点中的 HANA 大型实例单元。 
1. 启动处于休眠状态的 SAP HANA 生产实例。
1. 如果已选择复制事务日志备份以缩短 RPO 时间，则需要将这些事务日志备份合并到新装载的 DR /hana/logbackups 目录。 不要覆盖现有备份。 复制尚未使用最新存储快照复制内容复制的较新备份。
1. 还可以还原已复制到 DR Azure 区域中 /hana/shared/PRD 卷的快照中的单个文件。 

也可测试 DR 故障转移，而不会影响实际的复制关系。 若要执行测试故障转移，请执行上文列出的步骤 1 和 2，然后继续执行以下步骤 3。

>[!IMPORTANT]
>使用步骤 3 中所述的脚本执行**测试故障转移**期间，不要在 DR 站点中创建的实例上运行任何生产事务。 该命令会创建一组卷，这些卷与主站点没有任何关系。 结果是无法同步回主站点。 

故障转移测试的步骤 3：

在 DR 站点 HANA 大型实例单元上, 执行脚本 azure_hana_test_dr_failover.pl。 此脚本不会停止主站点和 DR 站点之间的复制关系。 相反，此脚本会克隆 DR 存储卷。 克隆过程成功之后，克隆的卷将还原到最新快照的状态，然后装载到 DR 单元。 脚本要求还原 SAP HANA SID。 键入一个或唯一一个已复制并保存在 DR 站点中的 HANA 大型实例单元上 *HANABackupCustomerDetails.txt* 文件中的 SAP HANA SID。 

如果想要测试多个 SAP HANA 实例，则需要多次运行脚本。 根据请求键入想要测试故障转移的实例的 SAP HANA SID。 完成后，脚本会显示添加到 HANA 大型实例单元的卷的装载点列表。 此列表还包括已克隆的 DR 卷。

继续执行步骤 4。

   >[!NOTE]
   >如果需要故障转移到 DR 站点，以修复一些几小时前删除的数据，并因此需要将 DR 卷设置为早于最新快照的状态，此过程适用。 

1. 关闭在 HANA 大型实例的灾难恢复单元上运行的 HANA 非生产实例。 这是因为，已预安装 HANA 休眠生产实例。
1. 确保未运行任何 SAP HANA 进程。 使用以下命令执行此项检查：`/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`。 输出应显示 **hdbdaemon** 进程处于停止状态且没有其他 HANA 进程处于运行或已开始状态。
1. 确定希望将灾难恢复站点还原到的快照名称或 SAP HANA 备份 ID。 在真实的灾难恢复事例中，此快照通常是最新的快照。 如果需要恢复丢失的数据，请选择更早的快照。
1. 通过高优先级支持请求联系 Azure 支持部门。 请求在 DR 站点上还原该快照（快照的名称和日期）或请求提供 HANA 备份 ID。 默认情况下，运营团队只会还原 /hana/data 卷。 如果还想要还原 / hana/logbackups 卷，需要特意提出此要求。 不要还原 /hana/shared 卷。 应在重新装载 PRD 的 /hana/shared 卷之后选择特定的文件，例如，**.snapshot** 及其子目录中的 global.ini。 

   在操作端，将执行以下步骤：

   a. 停止生产卷到灾难恢复卷的快照复制。 如果生产站点服务中断是需要执行灾难恢复过程的原因，则此中断可能已经发生。
   
   b. 在灾难恢复卷上还原所选的存储快照或具有备份 ID 的快照。
   
   c. 还原后，灾难恢复卷可装载到灾难恢复区域中的 HANA 大型实例单元。
      
1. 将灾难恢复卷装载到灾难恢复站点中的 HANA 大型实例单元。 
1. 启动处于休眠状态的 SAP HANA 生产实例。
1. 如果已选择复制事务日志备份以缩短 RPO 时间，则需要将这些事务日志备份合并到新装载的 DR /hana/logbackups 目录。 不要覆盖现有备份。 复制尚未使用最新存储快照复制内容复制的较新备份。
1. 还可以还原已复制到 DR Azure 区域中 /hana/shared/PRD 卷的快照中的单个文件。

接下来的一系列步骤包括基于还原的存储快照和可用的事务日志备份恢复 SAP HANA 生产实例。

1. 使用 SAP HANA Studio 将备份位置更改为 **/hana/logbackups**。
   ![更改 DR 恢复的备份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA 会逐个扫描备份文件位置并建议要还原到的最新事务日志备份。 扫描需要花费几分钟时间，扫描完成后会显示如下所示的屏幕：![DR 恢复的事务日志备份列表](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 调整一些默认设置：

      - 清除“使用增量备份”。
      - 选择“初始化日志区域”。

   ![设置“初始化日志区域”](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. 选择“完成”。

   ![完成 DR 还原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

应显示如此处所示的进度窗口。 请记住，此为 SAP HANA 三节点横向扩展配置的灾难恢复还原示例。

![还原进度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果还原过程一直停留在“完成”屏幕且不显示进度屏幕，请确认辅助角色节点上的所有 SAP HANA 实例是否正常运行。 如有必要，请手动启动 SAP HANA 实例。


## <a name="failback-from-a-dr-to-a-production-site"></a>从 DR 站点故障回复到生产站点
可以从 DR 站点故障回复到生产站点。 让我们探讨以下场景：到灾难恢复站点的故障转移是由生产 Azure 区域中的问题引起，而不是因为需要恢复丢失的数据。 用户已在灾难恢复站点中运行了 SAP 生产工作负荷一段时间。 随着生产站点中问题的解决，需要故障回复到生产站点。 由于不能丢失数据，因此回到生产站点这一过程涉及到几个步骤且需要与 Azure 上的 SAP HANA 运营团队进行紧密协作。 问题解决后，需要联系运营团队，以便开始同步回到生产站点。

步骤顺序如下：

1. Azure 上的 SAP HANA 运营团队获取从灾难恢复存储卷同步生产存储卷（现表示生产状态）的触发指令。 在此状态中，生产站点中的 HANA 大型实例单元处于关闭状态。
1. Azure 上的 SAP HANA 运营团队监视复制并确保在通知客户之前实现跟进。
1. 需要关闭在灾难恢复站点使用 HANA 生产实例的应用程序。 然后执行 HANA 事务日志备份。 接下来，停止灾难恢复站点中 HANA 大型实例单元上运行的 HANA 实例。
1. 关闭灾难恢复站点中 HANA 大型实例单元上运行的 HANA 实例后，运营团队再次手动同步磁盘卷。
1. Azure 上的 SAP HANA 运营团队再次启动生产站点中的 HANA 大型实例单元，并将其交接于你。 确保在 HANA 大型实例单元启动时，SAP HANA 实例处于关闭状态。
1. 执行相同的数据库还原步骤，与之前故障转移到灾难恢复站点时一样。

## <a name="monitor-disaster-recovery-replication"></a>监视灾难恢复复制

可通过执行脚本 `azure_hana_replication_status.pl` 监视存储复制的进度状态。 必须从灾难恢复位置中运行的单元运行此脚本。 无论复制是否处于活动状态，此脚本都会正常运行。 可为灾难恢复位置中租户的每个 HANA 大型实例单元运行此脚本。 不能使用此脚本获取有关启动卷的详细信息。

使用以下命令调用脚本：
```
./azure_hana_replication_status.pl
```

输出按卷细分为以下部分：  

- 链接状态
- 当前复制活动
- 最新复制的快照 
- 最新快照的大小
- 当前快照之间的迟延时间（上一个完成的快照复制与当前复制之间）

链接状态显示为“活动”，除非位置之间的链接关闭或当前正在进行故障转移事件。 复制活动会处理当前是否正在复制任何数据、数据是否处于空闲状态或该链接是否发生其他活动的问题。 上一个复制的快照应仅显示为 `snapmirror…`。 然后将显示上一个快照的大小。 最后显示迟延时间。 迟延时间表示从计划的复制时间到复制结束之间的时间。 对于数据复制，迟延时间可能超过一小时，尤其是初始复制（尽管复制已开始）。 迟延时间将继续延长，直到完成正在进行的复制。

下面是输出的示例：

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```

**后续步骤**
- 请参阅 [HANA 端的监视和故障排除](hana-monitor-troubleshoot.md)。
