---
title: 备份 Azure Stack 文件和应用程序
description: 使用 Azure 备份将 Azure Stack 文件与应用程序备份和恢复到 Azure Stack 环境。
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 7baaa29d205c09daaeeebf44a4bad338913dcad9
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248854"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>在 Azure Stack 上备份文件和应用程序
可以使用 Azure 备份保护（或备份）Azure Stack 上的文件和应用程序。 若要备份文件和应用程序，请将 Microsoft Azure 备份服务器安装为 Azure Stack 上运行的虚拟机。 可以保护同一虚拟网络中任何 Azure Stack 服务器上运行的任何应用程序。 安装 Azure 备份服务器后，可添加 Azure 磁盘以增加可用于短期备份数据的本地存储。 Azure 备份服务器将 Azure 存储用于长期保留。

> [!NOTE]
> 尽管 Azure 备份服务器与 System Center Data Protection Manager (DPM) 类似，但不支持将 DPM 与 Azure Stack 配合使用。
>

本文不介绍如何在 Azure Stack 环境中安装 Azure 备份服务器。 若要在 Azure Stack 中安装 Azure 备份服务器，请参阅[准备使用 Azure 备份服务器备份工作负荷](backup-mabs-install-azure-stack.md)一文。


## <a name="back-up-azure-stack-vm-file-data-to-azure"></a>将 Azure Stack VM 文件数据备份到 Azure

若要配置 Azure 备份服务器来保护 IaaS 虚拟机，请打开 Azure 备份服务器控制台。 将使用控制台配置保护组，以及保护虚拟机上的数据。

1. 在 Azure 备份服务器控制台中单击“保护”，并在工具栏中，单击“新建”打开“创建新保护组”向导。

   ![在 Azure 备份服务器控制台中配置保护](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    打开向导可能需要花费几秒钟。 打开向导后，单击“下一步”转到“选择保护组类型”屏幕。

   ![“新建保护组”向导打开](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. 在“选择保护组类型”屏幕上，选择“服务器”并单击“下一步”。

    ![“新建保护组”向导打开](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    此时会打开“选择组成员”屏幕。 

    ![“新建保护组”向导打开](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. 在“选择组成员”屏幕中，单击 **+** 展开子项列表。 选中想要保护的所有项对应的复选框。 选择所有项后，单击“下一步”。

    ![“新建保护组”向导打开](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft 建议使将要共享保护策略的所有虚拟机放入同一个保护组。 有关规划和部署保护组的完整信息，请参阅 System Center DPM 文章[部署保护组](https://docs.microsoft.com/en-us/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801)。

4. 在“选择数据保护方法”屏幕中，键入保护组的名称。 选中“我想使用以下介质进行短期保护:”和“我需要在线保护”对应的复选框。 单击“资源组名称” 的 Azure 数据工厂。

    ![“新建保护组”向导打开](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    若要选择“我需要在线保护”，必须先选中“我想使用以下介质进行短期保护: 磁盘”。 Azure 备份服务器无法在磁带中保护，因此磁盘是短期保护的唯一选项。

5. 在“指定短期目标”屏幕中，选择要在磁盘中保存恢复点的时间长短，以及何时保存增量备份。 单击“资源组名称” 的 Azure 数据工厂。

    > [!IMPORTANT]
    > **不**应在 Azure 备份服务器附加的磁盘上保留操作恢复（备份）数据超过 5 天。
    >

    ![“新建保护组”向导打开](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    无需选择增量备份的间隔，只需单击“直接在恢复点之前”，即可在每个计划的恢复点之前运行快速完整备份。 如果保护的是应用程序工作负荷，Azure 备份服务器会根据同步频率计划创建恢复点（前提是应用程序支持增量备份）。 如果应用程序不支持增量备份，Azure 备份服务器会运行快速完整备份。

    对于“文件恢复点”，请指定何时创建恢复点。 单击“修改”设置创建恢复点的时间和星期日期。

6. 在“检查磁盘分配”屏幕中，检查为保护组分配的存储池磁盘空间。

    “总数据大小”是要备份的数据大小，“要在 Azure 备份服务器上预配的磁盘空间”是为保护组建议的空间。 Azure 备份服务器会根据设置选择理想的备份卷。 但是，可以在“磁盘分配详细信息”中编辑备份卷选项。 对于工作负荷，请在下拉菜单中选择首选的存储。 编辑操作会更改“可用磁盘存储”窗格中的“存储总量”和“可用存储”值。 预配不足的空间是 Azure 备份服务器建议添加到卷以便将来继续顺利备份的存储量。

7. 在“选择副本创建方法”中，选择要如何处理初始完整数据复制。 如果确定通过网络复制，Azure 建议选择非高峰时间。 如果数据量很大或者网络状态欠佳，请考虑使用可移动媒体脱机复制数据。

8. 在“选择一致性检查选项”中，选择要如何自动执行一致性检查。 使一致性检查仅在数据复制变得不一致时才运行，或根据计划运行。 如果不想配置自动一致性检查，可随时通过以下方式运行手动检查：
    * 在 Azure 备份服务器控制台的“保护”区域中，右键单击保护组，并选择“执行一致性检查”。

9. 如果选择备份到 Azure，请在“指定在线保护数据”页上，确保选择要备份到 Azure 的工作负荷。

10. 在“指定在线备份计划”中，指定何时增量备份到 Azure。 

    可将备份计划为每日/每周/每月/每年运行，并指定运行备份的时间/日期。 备份一天最多可以进行两次。 每次备份作业运行时，会通过 Azure 备份服务器磁盘上存储的备份数据的副本在 Azure 中创建数据恢复点。

11. 在“指定在线保留策略”中，指定如何在 Azure 中保留通过每日/每周/每月/每年备份创建的恢复点。

12. 在“选择在线复制”中，指定如何进行数据的初始完整复制。 

    可以通过网络复制，也可以执行脱机备份（脱机设定种子）。 脱机备份使用 [Azure 导入功能](./backup-azure-backup-import-export.md)。

13. 在“摘要”中检查设置。 单击“创建组”时，会进行初始数据复制。 数据复制完成后，在“状态”页上，保护组状态显示为“正常”。 初始备份作业根据保护组设置运行。

需要回答的问题：如何为 Azure Stack 短期磁盘存储扩展磁盘存储。 需要回顾哪些准则来解释短期磁盘存储？

## <a name="recover-file-data"></a>恢复文件数据

使用 Azure 备份服务器控制台将数据恢复到虚拟机。

1. 在 Azure 备份服务器控制台中的导航栏上单击“恢复”，并浏览到想要恢复的数据。 在结果窗格中选择数据。

2. 在恢复点部分的日历上，以粗体显示的日期表示可用的恢复点。 选择要恢复的恢复点的日期。

3. 在“可恢复的项”窗格中，选择要恢复的项。

4. 在“操作”窗格中，单击“恢复”打开“恢复向导”。

5. 可按如下所述恢复数据：

    * **恢复到原始位置** - 如果已通过 VPN 连接客户端计算机，则此选项不起作用。 请改用备用位置，然后从该位置复制数据。
    * **恢复到备用位置**

6. 指定恢复选项：

    * 对于“现有版本恢复行为”，请选择“创建副本”、“跳过”或“覆盖”。 仅当恢复到原始位置时才能使用“覆盖”。
    * 对于“还原安全性”，请选择“应用目标计算机的设置”或“应用恢复点版本的安全设置”。
    * 对于“网络带宽使用限制”，请单击“修改”启用网络带宽使用限制。
    * 选择“使用硬件快照启用基于 SAN 的恢复”，使用基于 SAN 的硬件快照加快恢复速度。 仅当已有一个启用了硬件快照功能的 SAN 时，此选项才有效。 若要使恢复点可写，SAN 必须能够创建克隆和拆分克隆。 受保护的 VM 和 Azure 备份服务器必须连接到同一 SAN。
    * **通知**单击“恢复完成时发送电子邮件”，并指定通知的收件人。 使用逗号分隔电子邮件地址。
    * 完成选择后，单击“下一步”

7. 检查恢复设置，单击“恢复”。 

    > [!Note] 
    > 恢复作业正在进行时，会取消针对选定恢复项运行的所有同步作业。
    >

如果使用新式备份存储 (MBS)，则不支持文件服务器最终用户恢复 (EUR)。 文件服务器 EUR 依赖于卷影复制服务 (VSS)，而新式备份存储并不使用该服务。 如果启用了 EUR，请使用以下步骤恢复数据：

1. 导航到受保护的文件，右键单击文件名并选择“属性”。

2. 在“属性”菜单中单击“旧版”，然后选择要恢复的版本。



## <a name="register-azure-backup-server-with-a-vault"></a>将 Azure 备份服务器注册到保管库
提供以下操作的步骤：

1. 打开恢复服务保管库。
2. 单击“备份基础结构”。
3. 查看备份管理服务器。

## <a name="see-also"></a>另请参阅
有关使用 Azure 备份服务器保护其他工作负荷的信息，请参阅以下文章之一：
- [备份 SharePoint 场](backup-azure-backup-sharepoint-mabs.md)
- [备份 SQL Server](backup-azure-sql-mabs.md)
