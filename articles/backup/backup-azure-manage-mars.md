---
title: 管理和监视 MARS 代理备份
description: 了解如何使用 Azure 备份服务管理和监视 Microsoft Azure 恢复服务（MARS）代理备份。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: b7e947e7fd473ec787d49ffe82532ffd5b6a98d1
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497009"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>使用 Azure 备份服务管理 Microsoft Azure 恢复服务（MARS）代理备份

本文介绍如何管理 Microsoft Azure 恢复服务代理备份的文件和文件夹。

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

1. 在 "**操作**" 中，单击 "**计划备份**"。

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

4. 默认情况下，将排除所选文件夹中的所有**子文件夹**。 可以通过选择 **"是"** 或 "**否**" 来更改此。 您可以编辑并指定要排除的文件类型，如下所示：

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
1. 在 "**暂停计划的备份**" 中，查看信息，然后单击 "**完成**"。

    ![修改或停止计划的备份。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. 在 "**修改备份过程**" 中，检查计划备份暂停是否处于成功状态，并单击 "**关闭**" 完成操作。

### <a name="stop-protection-and-delete-backup-data"></a>停止保护并删除备份数据

1. 打开 MARS 管理控制台，中转到 "**操作**" 窗格，然后选择 "**计划备份**"。
2. 从 "**修改或停止计划的备份**" 页上，选择 **"停止使用此备份计划" 并删除所有存储的备份**。 然后，选择“下一步”。

    ![修改或停止计划的备份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 从 "**停止计划的备份**" 页上，选择 "**完成**"。

    ![停止计划的备份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系统会提示输入安全 PIN （个人标识号），必须手动生成。 为此，请先登录到 Azure 门户。
5. 请在 "**恢复服务保管库**" > **设置**" > **属性**"。
6. 在 "**安全 PIN**" 下，选择 "**生成**"。 复制此 PIN。 PIN 的有效时间仅为五分钟。
7. 在管理控制台中，粘贴 PIN，然后选择 **"确定"** 。

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 "**修改备份进度**" 页中，将显示以下消息：*已删除的备份数据将保留14天。之后，备份数据将被永久删除。*  

    ![删除备份基础结构。](./media/backup-azure-delete-vault/deleted-backup-data.png)

删除本地备份项后，请按照门户中的后续步骤进行操作。

## <a name="re-enable-protection"></a>重新启用保护

如果在保留数据的同时停止了保护并决定恢复保护，则可以使用修改备份策略重新启用备份计划。

1. 在 "**操作**" 中选择 "**计划备份**"。
1. 选择**重新启用备份计划。你还可以修改备份项或时间**，然后单击 "**下一步**"。<br>

    ![删除备份基础结构。](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. 在 "**选择要备份的项**" 中，单击 "**下一步**"。

    ![删除备份基础结构。](./media/backup-azure-manage-mars/re-enable-next.png)
1. 在 "**指定备份计划**" 中，指定备份计划，然后单击 "**下一步**"。
1. 在 "**选择保留策略**" 中，指定保持期，并单击 "**下一步**"。
1. 最后在**确认**屏幕中查看策略详细信息，然后单击 "**完成**"。

## <a name="re-generate-passphrase"></a>重新生成密码

使用密码来加密和解密数据，同时使用 MARS 代理从 Azure 备份或还原本地或本地计算机。 如果丢失或忘记了通行短语，则可以通过执行以下步骤，重新生成通行短语（前提是计算机仍注册了恢复服务保管库，并配置了备份）：

- 在 MARS 代理控制台中，转到 "**操作" 窗格** > **更改属性**>。 然后，中转到**加密选项卡**。<br>
- 选择 "**更改密码**" 复选框。<br>
- 输入新密码或单击 "**生成密码**"。
- 单击 "**浏览**" 保存新通行短语。

    ![生成通行短语。](./media/backup-azure-manage-mars/passphrase.png)
- 单击“确定”以应用更改。  如果在恢复服务保管库的 Azure 门户上启用了[安全功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features)，则系统会提示输入安全 PIN。 若要接收此 PIN，请按照[本文](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)中列出的步骤进行操作。<br>
- 粘贴门户中的安全 PIN，并单击 **"确定"** 应用更改。<br>

    ![生成通行短语。](./media/backup-azure-manage-mars/passphrase2.png)
- 确保密码安全保存在备用位置（源计算机除外）中，最好是在 Azure Key Vault 中。 如果有多台计算机使用 MARS 代理进行备份，请跟踪所有密码。


## <a name="next-steps"></a>后续步骤

- 有关支持的方案和限制的详细信息，请参阅[MARS 代理的支持矩阵](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)。
- 了解有关[按需备份策略保留行为的](backup-configure-vault.md#on-demand-backup-policy-retention-behavior)详细信息。
