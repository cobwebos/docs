---
title: 管理 & 监视 Microsoft Azure 恢复服务代理备份
description: 了解如何使用 Azure 备份服务管理和监视 Microsoft Azure 恢复服务代理备份。
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: dacurwin
ms.openlocfilehash: 88a914a2a4d3d62918b01674d6d21f2f011e2c2e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73834302"
---
# <a name="manage-microsoft-azure-recovery-services-agent-backups-by-using-the-azure-backup-service"></a>使用 Azure 备份服务管理 Microsoft Azure 恢复服务代理备份

本文介绍如何管理 Microsoft Azure 恢复服务代理备份的文件和文件夹。

## <a name="create-a-backup-policy"></a>创建备份策略

备份策略指定何时拍摄数据快照以创建恢复点和恢复点的保留时间。 使用 MARS 代理配置备份策略。

按如下所述创建策略：

1. 下载并注册 MARS 代理后，启动代理控制台。 可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。  
2. 在“操作”中单击“计划备份”。

    ![计划 Windows Server 备份](./media/backup-configure-vault/schedule-first-backup.png)
3. 在计划备份向导中选择“开始”，然后单击“下一步”。
4. 在 "**选择要备份的项**" 中，单击 "**添加项**"。

    ![选择要备份的项](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. 在 "**选择项目**" 中，选择要备份的内容，然后单击 **"确定"** 。

    ![要备份的选定项](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. 在 "**选择要备份的项**" 页上，单击 "**下一步**"。
7. 在 "**指定备份计划**" 页上，指定要进行每日或每周备份的时间。 然后单击“下一步”。

    - 创建备份时会创建一个恢复点。
    - 在环境中创建的恢复点数目取决于备份计划。

8. 可以计划每日备份，每天最多备份三次。 例如，屏幕快照显示两个每日备份，一个为午夜，另一个在下午6:00。

    ![每日计划](./media/backup-configure-vault/day-schedule.png)

9. 也可以运行每周备份。 例如，屏幕截图显示每个备用星期日 & 星期三 9:30 AM 到 1:00 AM 创建的备份。

    ![每周日程安排](./media/backup-configure-vault/week-schedule.png)

10. 在“选择保留策略”页上，指定如何存储数据的历史副本。 然后单击“下一步”。

    - 保留设置指定要存储哪些恢复点，以及要存储多长时间。
    - 例如，在设置每日保留设置时，可以指明在针对每日保留指定的时间，要将最新恢复点保留指定的天数。 另举一例，可以指定每月保留策略，指明在每个月的 30 日创建的恢复点应保留 12 个月。
    - 每日和每周恢复点保留期通常与备份计划相一致。 这意味着，根据计划触发备份时，备份创建的恢复点将存储每日或每周保留策略中指定的持续时间。
    - 例如，在以下屏幕截图中：-每日午夜备份和 6:00 PM 保留7天。
            -在星期六午夜和下午6:00 进行的备份保留四周。
            -在每月最后一周的午夜和 6:00 PM 进行的备份保留12个月。
            - 在 3 月份最后一周的星期六创建的备份将保留 10 年。

           ![Retention example](./media/backup-configure-vault/retention-example.png)

11. 在 "**选择初始备份类型**" 中，决定是要通过网络进行初始备份还是使用脱机备份（有关脱机备份的详细信息，请参阅此[文](backup-azure-backup-import-export.md)）。 若要通过网络进行初始备份，请选择 "**自动通过网络**"，然后单击 "**下一步**"。

    ![初始备份类型](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

12. 在“确认”中复查信息，然后单击“完成”。
    ![确认备份类型](./media/backup-azure-manage-mars/confirm-backup-type.png)

13. 在向导完成创建备份计划后，请单击“**关闭**”。
  ![确认修改备份过程](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

你必须在安装了代理的每台计算机上创建一个策略。

## <a name="modify-a-backup-policy"></a>修改备份策略

修改备份策略时，可以添加新项目、从备份中删除现有项，或使用排除设置来排除要备份的文件。

- **添加项**仅将此选项用于添加要备份的新项。 若要删除现有项目，请使用 "**删除项目**" 或 "**排除设置**" 选项。  
- **删除项**使用此选项可以删除要备份的项。
  - 使用**排除设置**来删除卷中的所有项目，而不是**删除项目**。
  - 清除卷中的所有选择会导致旧的项目备份，并根据在上次备份时保留的设置保留，无需修改作用域。
  - 重新选择这些项，将导致首次完整备份，而新的策略更改不会应用于旧备份。
  - 取消选中整个卷将保留过去的备份，而不包含任何用于修改保留策略的作用域。
- **排除设置**使用此选项可从备份中排除特定项目。
  
### <a name="add-new-items-to-existing-policy"></a>将新项添加到现有策略

1. 在“操作”中单击“计划备份”。

    ![计划 Windows Server 备份](./media/backup-configure-vault/schedule-first-backup.png)

2. 在 "**选择策略项**" 选项卡中，**为文件和文件夹选择 "修改备份计划**"，然后单击 "**下一步**"。

    ![选择策略项](./media/backup-azure-manage-mars/select-policy-items.png)

3. 在 "**修改或停止计划备份**" 选项卡中，选择 "**更改备份项或时间**"，然后单击 "**下一步**"。

    ![修改或计划备份](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 在 "**选择要备份的项**" 选项卡中，单击 "**添加项**" 以添加要备份的项。

    ![修改或计划备份添加项](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. 在 "**选择项目**" 窗口中，选择 "飞出" 或要添加的文件夹，然后单击 **"确定"** 。

    ![选择项](./media/backup-azure-manage-mars/select-item.png)

6. 完成后续步骤，然后单击 "**完成**" 完成操作。

### <a name="add-exclusion-rules-to-existing-policy"></a>向现有策略添加排除规则

你可以添加排除规则以跳过不想备份的文件和文件夹。 在定义新策略或修改现有策略时，可以执行此操作。

1. 在 "操作" 窗格中，单击 "**计划备份**"。 选择 "**选择要备份的项**"，然后单击 "**排除设置**"。

    ![选择项](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. 在 "**排除设置**" 中，单击 "**添加排除**"。

    ![选择项](./media/backup-azure-manage-mars/add-exclusion.png)

3. 从 "**选择要排除的项**" 中，浏览文件和文件夹，然后选择要排除的项，然后单击 **"确定"** 。

    ![选择项](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 默认情况下，将排除所选文件夹中的所有**子文件夹**。 可以通过选择 **"是"** 或 "**否**" 来更改此。 您可以按如下所示编辑和指定要排除的文件类型：

    ![选择项](./media/backup-azure-manage-mars/subfolders-type.png)

5. 完成后续步骤，然后单击 "**完成**" 完成操作。

### <a name="remove-items-from-existing-policy"></a>从现有策略删除项

1. 在 "操作" 窗格中，单击 "**计划备份**"。 请参阅**选择要备份的项**。 从列表中，选择要从备份计划中删除的文件和文件夹，然后单击 "**删除项**"。

    ![选择项](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> 从策略中完全删除卷时，请继续操作。  如果需要再次添加，则会将其视为新卷。 下一次计划的备份将执行初始备份（完整备份），而不是增量备份。 如果需要在以后临时删除和添加项，则建议使用**排除项设置**，而不是**删除项**，以确保进行增量备份而不是完整备份。

2. 完成后续步骤，然后单击 "**完成**" 完成操作。

## <a name="stop-protecting-files-and-folder-backup"></a>停止保护文件和文件夹备份

可以通过两种方法来停止保护文件和文件夹备份：

- **停止保护并保留备份数据**。
  - 此选项将停止所有将来的备份作业。
  - Azure 备份服务将保留已根据保留策略备份的恢复点。
  - 你将能够还原未过期恢复点的备份数据。
  - 如果决定恢复保护，则可以使用 "*重新启用备份计划*" 选项。 然后，将根据新的保留策略保留数据。
- **停止保护并删除备份数据**。
  - 此选项将停止所有将来的备份作业以保护你的数据并删除所有恢复点。
  - 你将收到 "删除备份数据" 警报电子邮件，其中包含一条已*删除此备份项目的数据的消息。此数据暂时可用于14天，之后它将被永久删除*，建议操作*在14天内重新保护备份项目以恢复数据。*
  - 若要恢复保护，请从删除操作14天内重新保护。

### <a name="stop-protection-and-retain-backup-data"></a>停止保护并保留备份数据

1. 打开 MARS 管理控制台，中转到 "**操作" 窗格**，然后**选择 "计划备份**"。
    ![修改或停止计划的备份。](./media/backup-azure-manage-mars/mars-actions.png)
1. 在 "**选择策略项**" 页上，选择 "**修改文件和文件夹的备份计划**"，然后单击 "**下一步**"。
    ![修改或停止计划的备份。](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 从 "**修改或停止计划的备份**" 页上，选择 **"停止使用此备份计划"，但保留存储的备份，直到再次激活计划**。 然后，选择“下一步”。  
    ![修改或停止计划的备份。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. 在 "**暂停计划的备份**" 中，单击 "**完成**" ![修改或停止计划的备份。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. 在 "**修改备份过程**" 中，检查 "计划备份暂停状态成功"，然后单击 "**关闭**" 完成操作。

### <a name="stop-protection-and-delete-backup-data"></a>停止保护并删除备份数据

1. 打开 MARS 管理控制台，转到“操作”窗格并选择“计划备份”。
2. 在“修改或停止计划的备份”页中选择“停止使用此备份计划并删除所有存储的备份”选项。 然后，选择“下一步”。

    ![修改或停止计划的备份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 在“停止计划的备份”页中选择“完成”。

    ![停止计划的备份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系统会提示输入安全 PIN（个人标识号）。必须手动生成该 PIN。 为此，请先登录到 Azure 门户。
5. 转到“恢复服务保管库” > “设置” > “属性”。
6. 在“安全 PIN”下选择“生成”。 复制此 PIN。 该 PIN 的有效时间仅为五分钟。
7. 在管理控制台中粘贴该 PIN，然后选择“确定”。

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 "**修改备份进度**" 页中，将显示以下消息：*已删除的备份数据将保留14天。之后，备份数据将被永久删除。*  

    ![删除备份基础结构。](./media/backup-azure-delete-vault/deleted-backup-data.png)

删除本地备份项后，遵循门户中的后续步骤。

## <a name="re-enable-protection"></a>重新启用保护

如果在保留数据的同时停止了保护并决定恢复保护，则可以使用修改备份策略重新启用备份计划。

1. 在 "**操作**" 中选择 "**计划备份**"。
1. 选择**重新启用备份计划。你还可以修改备份项或 tines** ，并单击 "**下一步**"。
    ![删除备份基础结构。](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. 在 "**选择要备份的项**" 中，单击 "**下一步**"。
    ![删除备份基础结构。](./media/backup-azure-manage-mars/re-enable-next.png)
1. 在 "**指定备份计划**" 中，指定备份计划，然后单击 "**下一步**"。
1. 在 "**选择保留策略**" 中，指定保持期，并单击 "**下一步**"。
1. 最后，在 "**聘约确认**" 屏幕中查看策略详细信息，然后单击 "**完成**"。

## <a name="next-steps"></a>后续步骤

- 有关支持的方案和限制的详细信息，请参阅[MARS 的支持矩阵](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)。
- 了解有关即席[备份策略保留行为](backup-configure-vault.md#ad-hoc-backup-policy-retention-behavior)的详细信息。
