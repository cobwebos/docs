---
title: 管理和监视 MARS 代理备份
description: 了解如何使用 Azure 备份服务管理和监视 Microsoft Azure 恢复服务 （MARS） 代理备份。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c11d73edd32c197aac2cec58eeb1cc20e5c6a339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673246"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>使用 Azure 备份服务管理 Microsoft Azure 恢复服务 （MARS） 代理备份

本文介绍如何管理使用 Microsoft Azure 恢复服务代理备份的文件和文件夹。

## <a name="modify-a-backup-policy"></a>修改备份策略

修改备份策略时，可以添加新项目、从备份中删除现有项目，或使用排除设置排除备份文件。

- **添加项目**仅用于添加新项目进行备份。 要删除现有项目，请使用 **"删除项目**"或 **"排除设置"** 选项。  
- **删除项目**使用此选项从备份中删除项目。
  - 使用**排除设置**删除卷中的所有项目，而不是**删除项目**。
  - 清除卷中的所有选择会导致项目的旧备份（根据上次备份时保留的保留设置）保留，而没有修改的余地。
  - 重新选择这些项目，将会导致第一次完全备份，并且新的策略更改不会应用于旧备份。
  - 取消选择整个卷将保留过去的备份，没有任何修改保留策略的余地。
- **排除设置**使用此选项从备份中排除特定项。

### <a name="add-new-items-to-existing-policy"></a>将新项目添加到现有策略

1. 在“操作”中单击“计划备份”。********

    ![计划 Windows Server 备份](./media/backup-configure-vault/schedule-first-backup.png)

2. 在 **"选择策略项**"选项卡中，然后选择 **"修改文件和文件夹的备份计划**"，然后单击"**下一步**"。

    ![选择策略项](./media/backup-azure-manage-mars/select-policy-items.png)

3. 在 **"修改或停止计划备份**"选项卡中，选择 **"更改备份项目或时间**"，然后单击"**下一步**"。

    ![修改或计划备份](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 在 **"将项目选择备份**"选项卡中，单击"**添加项目**"以添加要备份的项目。

    ![修改或计划备份添加项目](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. 在 **"选择项目"** 窗口中，选择要添加的苍蝇或文件夹，然后单击"**确定**"。

    ![选择项目](./media/backup-azure-manage-mars/select-item.png)

6. 完成后续步骤，然后单击 **"完成"** 以完成操作。

### <a name="add-exclusion-rules-to-existing-policy"></a>将排除规则添加到现有策略

您可以添加排除规则来跳过不想备份的文件和文件夹。 您可以在定义新策略或修改现有策略期间执行此操作。

1. 在"操作"窗格中，单击 **"计划备份**"。 转到 **"选择要备份的项目**"，然后单击 **"排除设置**"。

    ![选择项目](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. 在**排除设置中**，单击"**添加排除**"。

    ![选择项目](./media/backup-azure-manage-mars/add-exclusion.png)

3. 从 **"选择项目"到"排除**"，浏览文件和文件夹并选择要排除的项目，然后单击"**确定**"。

    ![选择项目](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 默认情况下，所选**文件夹中的所有子文件夹**都被排除在外。 您可以通过选择 **"是**"或 **"否**"来更改此。 您可以编辑和指定要排除的文件类型，如下所示：

    ![选择项目](./media/backup-azure-manage-mars/subfolders-type.png)

5. 完成后续步骤，然后单击 **"完成"** 以完成操作。

### <a name="remove-items-from-existing-policy"></a>从现有策略中删除项目

1. 在"操作"窗格中，单击 **"计划备份**"。 转到 **"选择要备份的项目**"。 从列表中，选择要从备份计划中删除的文件和文件夹，然后单击 **"删除项目**"。

    ![选择项目](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> 从策略中完全删除卷时，请小心操作。  如果需要再次添加它，则它将被视为新卷。 下一个计划备份将执行初始备份（完全备份），而不是增量备份。 如果需要稍后临时删除和添加项目，建议使用 **"排除设置"** 而不是 **"删除项目**"来确保增量备份而不是完全备份。

2. 完成后续步骤，然后单击 **"完成"** 以完成操作。

## <a name="stop-protecting-files-and-folder-backup"></a>停止保护文件和文件夹备份

有两种方法可以停止保护文件和文件夹备份：

- **停止保护并保留备份数据**。
  - 此选项将停止保护以后的所有备份作业。
  - Azure 备份服务将保留已根据保留策略备份的恢复点。
  - 您将能够为未过期的恢复点还原备份的数据。
  - 如果您决定恢复保护，则可以使用 *"重新启用备份计划"* 选项。 之后，将根据新的保留政策保留数据。
- **停止保护并删除备份数据**。
  - 此选项将停止所有将来的备份作业保护数据并删除所有恢复点。
  - 您将收到删除备份数据警报电子邮件，邮件显示*此备份项目的数据已被删除。此数据将暂时使用 14 天，之后将被永久删除*，建议在*14 天内重新保护备份项目以恢复您的数据。*
  - 要恢复保护，请从删除操作的 14 天内重新保护。

### <a name="stop-protection-and-retain-backup-data"></a>停止保护并保留备份数据

1. 打开 MARS 管理控制台，转到 **"操作"窗格**，然后**选择"计划备份**"。

    ![修改或停止计划的备份。](./media/backup-azure-manage-mars/mars-actions.png)
1. 在 **"选择策略项目"** 页**中，选择"修改文件和文件夹的备份计划**"，然后单击"**下一步**"。

    ![修改或停止计划的备份。](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 在 **"修改或停止计划备份"页中**，选择 **"停止使用此备份计划"，但保留存储的备份，直到计划再次激活**。 然后，选择 **"下一步**"。

    ![修改或停止计划的备份。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. 在**暂停计划备份**中查看信息，然后单击 **"完成**"。

    ![修改或停止计划的备份。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. 在 **"修改备份过程"** 中，请检查您的计划备份暂停处于成功状态，然后单击**接近**完成。

### <a name="stop-protection-and-delete-backup-data"></a>停止保护并删除备份数据

1. 打开 MARS 管理控制台，转到“操作”窗格并选择“计划备份”。********
2. 在“修改或停止计划的备份”页中选择“停止使用此备份计划并删除所有存储的备份”选项。******** 然后，选择 **"下一步**"。

    ![修改或停止计划的备份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 在“停止计划的备份”页中选择“完成”。********

    ![停止计划的备份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系统会提示输入安全 PIN（个人标识号）。必须手动生成该 PIN。 为此，请先登录到 Azure 门户。
5. 转到**恢复服务保管库** > **设置** > **属性**。
6. 在“安全 PIN”下选择“生成”********。 复制此 PIN。 该 PIN 的有效时间仅为五分钟。
7. 在管理控制台中粘贴该 PIN，然后选择“确定”。****

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 **"修改备份进度"** 页中，将显示以下消息：*已删除的备份数据将保留 14 天。之后，备份数据将被永久删除。*  

    ![删除备份基础结构。](./media/backup-azure-delete-vault/deleted-backup-data.png)

删除本地备份项后，遵循门户中的后续步骤。

## <a name="re-enable-protection"></a>重新启用保护

如果在保留数据时停止保护，并决定恢复保护，则可以使用修改备份策略重新启用备份计划。

1. 在 **"操作"** 上选择 **"计划备份**"。
1. 选择 **"重新启用备份计划"。您还可以修改备份项目或时间**，然后单击 **"下一步**"。<br>

    ![删除备份基础结构。](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. 在 **"选择要备份的项目**"中，单击 **"下一步**"。

    ![删除备份基础结构。](./media/backup-azure-manage-mars/re-enable-next.png)
1. 在 **"指定备份计划"** 中，指定备份计划，然后单击"**下一步**"。
1. 在 **"选择保留策略"** 中，指定保留期限，然后单击 **"下一步**"。
1. 最后在**确认**屏幕中，查看策略详细信息，然后单击 **"完成**"。

## <a name="re-generate-passphrase"></a>重新生成密码短语

密码短语用于加密和解密数据，同时使用 MARS 代理备份或还原本地或本地计算机以从 Azure 备份或还原本地计算机。 如果您丢失或忘记了密码短语，则可以按照以下步骤重新生成密码短语（前提是您的计算机仍在恢复服务保管库注册并配置了备份）：

- 从 MARS 代理控制台转到**操作窗格** > **更改属性**>。 然后转到**加密选项卡**。<br>
- 选择 **"更改密码"** 复选框。<br>
- 输入新的密码短语或单击 **"生成密码短语**"。
- 单击 **"浏览"** 以保存新密码。

    ![生成密码。](./media/backup-azure-manage-mars/passphrase.png)
- 单击"**确定"** 以应用更改。  如果在恢复服务保管库的 Azure 门户上启用[了安全功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features)，则系统将提示您输入安全 PIN。 要接收 PIN，请按照[本文](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)中列出的步骤操作。<br>
- 从门户粘贴安全 PIN 并单击 **"确定"** 以应用更改。<br>

    ![生成密码。](./media/backup-azure-manage-mars/passphrase2.png)
- 确保密码短语安全地保存在备用位置（源计算机之外），最好保存在 Azure 密钥保管库中。 如果使用 MARS 代理备份多台计算机，请跟踪所有密码。


## <a name="next-steps"></a>后续步骤

- 有关受支持的方案和限制的信息，请参阅 MARS[代理的支持矩阵](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)。
- 详细了解[按需备份策略保留行为](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)。
