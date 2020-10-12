---
title: 在 Azure Stack 上备份 SharePoint 场
description: 在 Azure Stack 上使用 Azure 备份服务器备份和还原 SharePoint 数据。 本文介绍如何配置 SharePoint 场，以便在 Azure 中存储所需的数据。 可以从磁盘或 Azure 还原受保护的 SharePoint 数据。
ms.topic: conceptual
ms.date: 06/07/2020
ms.openlocfilehash: 1e237e63b92468fafff4f8f8f525d1388840d162
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378315"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>在 Azure Stack 上备份 SharePoint 场

在 Azure Stack 上使用 Microsoft Azure 备份服务器 (MABS) 将 SharePoint 场备份到 Microsoft Azure，其方法与备份其他数据源极为类似。 Azure 备份提供灵活的备份计划来创建每日、每周、每月或每年备份点，并提供适用于各种备份点的保留策略选项。 利用该技术，不仅可以存储本地磁盘副本以实现快速的恢复时间目标 (RTO)，还可以将副本存储到 Azure 以进行经济高效的长期保留。

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint 支持的版本与相关保护方案

MABS 的 Azure 备份支持以下方案：

| 工作负载 | 版本 | Sharepoint 部署 | 保护和恢复 |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016、SharePoint 2013、SharePoint 2010 |部署为 Azure Stack 虚拟机的 SharePoint <br> -------------- <br> SQL AlwaysOn | 保护 SharePoint 场恢复选项：从磁盘恢复点恢复场、数据库、文件或列表项。  从 Azure 恢复点恢复场和数据库。 |

## <a name="before-you-start"></a>开始之前

在将 SharePoint 场备份到 Azure 之前，需要确保满足几个条件。

### <a name="whats-not-supported"></a>不支持的功能

* 保护 SharePoint 场的 MABS 不会保护搜索索引或应用程序服务数据库。 需要单独为这些数据库配置保护。

* MABS 不提供横向扩展文件服务器 (SOFS) 共享托管的 SharePoint SQL Server 数据库备份。

### <a name="prerequisites"></a>先决条件

在继续下一步之前，请确保符合[使用 Microsoft Azure 备份保护工作负载的所有先决条件](backup-azure-dpm-introduction.md#prerequisites-and-limitations)。 先决条件包括如下任务：创建备份保管库、下载保管库凭据、安装 Azure 备份代理，以及在保管库中注册 Azure 备份服务器。

其他先决条件和限制：

* 默认情况下，在保护 SharePoint 时，所有内容数据库（以及 SharePoint_Config 和 SharePoint_AdminContent* 数据库）都将受到保护。 如果要添加自定义项，（如搜索索引、模板、应用程序服务数据库或用户配置文件服务），则需要单独配置这些项以进行保护。 确保为包括这些类型的功能或自定义文件的所有文件夹启用保护。

* 无法将 SharePoint 数据库作为 SQL Server 数据源进行保护。 可以从场备份恢复单个数据库。

* 请记住，MABS 以本地系统的形式运行，并备份 SQL Server 数据库，它需要对帐户具有 SQL Server 的 sysadmin 权限。 在想要备份的 SQL Server 上，将 NT AUTHORITY\SYSTEM 设置为 sysadmin。

* 针对场中的每 1000 万个项，必须有至少 2 GB 的卷空间用于放置 MABS 文件夹。 此空间对目录生成是必要的。 为了使你能够使用 MABS 恢复特定项（网站集、站点、列表、文档库、文件夹、单个文档与列表项），目录生成将创建一个包含在每个内容数据库中的 URL 列表。 可以在 MABS 管理员控制台的“恢复”任务区域中，查看“可恢复项”窗格中的 URL 列表。

* 在 SharePoint 场中，如果你具有使用 SQL Server 别名配置的 SQL Server 数据库，请在 MABS 将要保护的前端 Web 服务器上安装 SQL Server 客户端组件。

* SharePoint 2013 不支持保护应用程序存储项。

* MABS 不支持保护远程 FILESTREAM。 FILESTREAM 应是数据库的一部分。

## <a name="configure-backup"></a>配置备份

若要备份 SharePoint 场，请使用 ConfigureSharePoint.exe 配置 SharePoint 保护，然后在 MABS 中创建保护组。

1. **运行 ConfigureSharePoint.exe** - 此工具将配置 SharePoint VSS 编写器服务 \(WSS\) 并为保护代理提供 SharePoint 场的凭据。 部署保护代理之后，可在前端 Web 服务器上的 `<MABS Installation Path\>\bin` 文件夹中找到 ConfigureSharePoint.exe 文件。  如果有多台 WFE 服务器，只需在其中一台服务器上安装它即可。 请按如下所述运行：

    * 在 WFE 服务器上的命令提示符处，导航到 `\<MABS installation location\>\\bin\\` 并运行 `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]`，其中：

        * EnableSharePointProtection 启用对 SharePoint 场的保护、启用 VSS 编写器，并注册 DCOM 应用程序 WssCmdletsWrapper 的标识，以采用其凭据随此选项一起输入的用户身份运行。 此帐户应该是场管理员，也是前端 Web 服务器上的本地管理员。

        * EnableSPSearchProtection 通过使用前端 Web 服务器上的 HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ 下的注册表项 SharePointSearchEnumerationEnabled 来保护 WSS 3.0 SP 搜索，并注册 DCOM 应用程序 WssCmdletsWrapper 的标识，以采用其凭据随此选项一起输入的用户身份运行。 此帐户应该是场管理员，也是前端 Web 服务器上的本地管理员。

        * ResolveAllSQLAliases 显示 SharePoint VSS 编写器报告的所有别名，并将它们解析为对应的 SQL Server。 它还显示这些别名解析的实例名称。 如果服务器已建立镜像，它还将显示镜像服务器。 它报告未解析为 SQL Server 的所有别名。

        * SetTempPath 将环境变量 TEMP 和 TMP 设置为指定的路径。 如果正在恢复大网站集、站点、列表或项目，并且场管理员临时文件夹中的空间不足，则项目级恢复将失败。 通过此选项，你可以将临时文件的文件夹路径更改为具有充足空间来存储所恢复的网站集或站点的卷。

    * 输入场管理员凭据。 此帐户应是 WFE 服务器上本地管理员组的成员。 如果场管理员不是本地管理员，请在 WFE 服务器上授予以下权限：

        * 向 WSS_Admin_WPG 组授予对 MABS 文件夹 (`%Program Files%\Data Protection Manager\DPM\`) 的完全控制权限。

        * 向 WSS_Admin_WPG 组授予对 MABS 注册表项 (`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager`) 的读取访问权限。

        运行 ConfigureSharePoint.exe 后，如果 SharePoint 场管理员凭据发生更改，则需要重新运行。

1. 若要创建保护组，请选择 "**保护**  >  **操作**" "  >  **创建保护组**" 以在 MABS 控制台中打开 "**创建新保护组**" 向导。

1. 在“选择保护组类型”中，选择“服务器” 。

1. 在“选择组成员”中，展开包含 WFE 角色的服务器。 如果有多个 WFE 服务器，请选择在其上安装 ConfigureSharePoint.exe 的服务器。

    展开 SharePoint 服务器时，MABS 会查询 VSS，以查看 MABS 可以保护的数据。  如果 SharePoint 数据库是远程数据库，则 MABS 将连接到该数据库。 如果未显示 SharePoint 数据源，请检查 VSS 编写器是否在 SharePoint 服务器和任何远程 SQL Server 上运行，并确保 MABS 代理同时安装在 SharePoint 服务器和远程 SQL Server 上。 此外，请确保不会像 SQL Server 数据库一样在其他位置保护 SharePoint 数据库。

1. 在“选择数据保护方法”中，指定要如何处理短期和长期备份。 短期 \- 备份始终是磁盘，可以选择通过 Azure 备份从磁盘备份到 azure 云， \( 用于短期或长期 \- \) 。

1. 在“选择短期目标”中，指定要如何备份到磁盘上的短期存储。   在“保持期”中，指定要在磁盘上保留数据的时间长度。 在“同步频率”中，指定要对磁盘运行增量备份的频率。 如果不想设置备份间隔，可以选中“就在恢复点之前”，以便 MABS 将计划在每个恢复点之前运行快速完整备份。

1. 在“检查磁盘分配”页中，检查为保护组分配的存储池磁盘空间。

    “总数据大小”是要备份的数据大小，“要在 MABS 上预配的磁盘空间”是 MABS 为保护组建议的空间 。 MABS 根据设置选择理想的备份卷。 但是，你可以在“磁盘分配详细信息”中编辑备份卷选项。 对于工作负荷，请在下拉菜单中选择首选的存储。 编辑时，更改的是“可用磁盘存储”窗格中的“总存储”和“可用存储”值。 预配不足的空间是 MABS 建议添加到卷以便将来继续顺利备份的存储量。

1. 在“选择副本创建方法”中，选择要如何处理初始完整数据复制。  如果选择通过网络进行复制，我们建议选择非高峰时间。 如果数据量很大或者网络状态欠佳，请考虑使用可移动媒体脱机复制数据。

1. 在“选择一致性检查选项”中，选择要如何自动执行一致性检查。 可以仅在副本数据变得不一致时执行检查，或者按计划执行检查。 如果不想配置自动的一致性检查，你可以随时运行手动检查，可以右键单击 MABS 控制台的“保护”区域中的保护组，然后选择“执行一致性检查” 。

1. 如果选择使用 Azure 备份来备份到云，则在“指定在线保护数据”页上，确保选择要备份到 Azure 的工作负荷。

1. 在“指定在线备份计划”中，指定增量备份到 Azure 的频率。 可将备份计划为每日/每周/每月/每年运行，并指定运行备份的时间/日期。 备份一天最多可以进行两次。 每次备份运行时，会通过 MABS 磁盘上存储的备份数据的副本在 Azure 中创建数据恢复点。

1. 在“指定在线保留策略”中，可以指定如何在 Azure 中保留通过每日/每周/每月/每年备份创建的恢复点。

1. 在“选择在线复制”中，指定如何进行数据的初始完整复制。 可以通过网络复制，也可以执行脱机备份（脱机设定种子）。 脱机备份使用 Azure 导入功能。 [了解详细信息](./backup-azure-backup-import-export.md)。

1. 在“摘要”页上，查看设置。 选择“创建组”**** 之后，就会进行数据的初始复制。 完成后，“状态”页上的保护组状态将显示为“正常” 。 然后，将根据保护组设置开始进行备份。

## <a name="monitoring"></a>监视

创建保护组之后，将进行初始复制，且 MABS 将开始备份和同步 SharePoint 数据。 MABS 将监视初始同步和后续备份。  可以通过以下几种方式监视 SharePoint 数据：

* 使用默认 MABS 监视，可以通过发布警报和配置通知为主动监视设置通知。 可通过电子邮件发送关于关键警报、警告警报或信息警报以及实例化恢复的状态的通知。

* 如果使用 Operations Manager，则可以集中发布警报。

### <a name="set-up-monitoring-notifications"></a>设置监视通知

1. 在 MABS 管理员控制台中，选择 "**监视**" "操作" "  >  **Action**  >  **选项**"。

2. 选择 " **SMTP 服务器**"，键入要将通知发送到的服务器名称、端口和电子邮件地址。 该地址必须为有效地址。

3. 在“经过身份验证的 SMTP 服务器”中，键入用户名和密码。 用户名和密码必须是其“发件人”地址在上一步中进行了描述的用户的域帐户名。 否则，发送通知将失败。

4. 若要测试 SMTP 服务器设置，请选择 " **发送测试电子邮件**"，键入要 MABS 发送测试邮件的目标电子邮件地址，然后选择 **"确定"**。 选择 "**选项**  >  **通知**"，并选择要向其通知收件人的警报类型。 在“收件人”中，键入 MABS 要发送通知副本的每位收件人的电子邮件地址。

### <a name="publish-operations-manager-alerts"></a>发布 Operations Manager 警报

1. 在 MABS 管理员控制台中，选择 "**监视**" "  >  **Action**  >  **Options**  >  **警报发布**" "发布  >  **活动警报**"

2. 启用“警报发布”后，用户可能需要将所有现有 MABS 警报将发布到“MABS 警报”事件日志 。 安装在 MABS 服务器上的 Operations Manager 代理随后会将这些警报发布到 Operations Manager，并在生成新警报时继续更新控制台。

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>使用 MABS 从磁盘还原 SharePoint 项

在以下示例中，“*恢复 SharePoint 项*”被意外删除，需要恢复。
![MABS SharePoint 保护 4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. 打开“MABS 管理员控制台”。 MABS 保护的所有 SharePoint 场都在“保护”选项卡中显示。

    ![MABS SharePoint 保护 3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. 若要开始恢复该项，请选择“**恢复**”选项卡。

    ![MABS SharePoint 保护 5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. 可以通过在恢复点范围内执行基于通配符的搜索，在 SharePoint 中搜索“*恢复 SharePoint 项*”。

    ![MABS SharePoint 保护 6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. 从搜索结果中选择相应的恢复点，单击右键该项，并选择“**恢复**”。
5. 还可以浏览各个恢复点，并选择要恢复的数据库或项。 选择“**日期 > 恢复时间**”，并选择正确的“**数据库 > SharePoint 场 > 恢复点 > 项**”。

    ![MABS SharePoint 保护 7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. 右键单击该项，并选择“**恢复**”打开“**恢复向导**”。 选择“下一步”。

    ![复查恢复选择](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. 选择要执行的恢复类型，然后选择 " **下一步**"。

    ![恢复类型](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > 示例中所选的“**恢复到原始**”会将该项恢复到原始 SharePoint 站点。
   >
   >
8. 选择要使用的“恢复过程”。

   * 如果 SharePoint 场未更改，并且与正在还原的恢复点相同，请选择 " **不使用恢复场进行恢复** "。
   * 如果 SharePoint 场自创建恢复点后已更改，请选择“使用恢复场进行恢复”  。

     ![恢复过程](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. 提供暂时恢复数据库的暂存 SQL Server 实例位置，并在要恢复该项的 MABS 和运行 SharePoint 的服务器上提供暂存文件共享。

    ![暂存位置 1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS 将承载 SharePoint 项的内容数据库附加到临时 SQL Server 实例。 MABS 将从内容数据库恢复该项，并将它放在 MABS 上的暂存文件位置。 现在，需要将暂存位置中的已恢复项导出到 SharePoint 场上的暂存位置。

    ![暂存位置 2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. 选择“**指定恢复选项**”，并将安全设置应用到 SharePoint 场，或应用恢复点的安全设置。 选择“下一步”。

    ![恢复选项](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > 可以选择限制网络带宽使用率。 这可以在生产时段最大程度地降低对生产服务器的影响。
    >
    >
11. 查看摘要信息，然后选择 " **恢复** " 开始恢复文件。

    ![恢复摘要](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. 现在，在“MABS 管理员控制台”中选择“监视”选项卡以查看恢复的“状态”。  

    ![恢复状态](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > 文件现已还原。 可以刷新 SharePoint 站点来检查已还原的文件。
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>使用 MABS 从 Azure 还原 SharePoint 数据库

1. 若要恢复 SharePoint 内容数据库，请浏览各个恢复点（如上所示），并选择要还原的恢复点。

    ![MABS SharePoint 保护 8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. 双击 SharePoint 恢复点以显示可用的 SharePoint 目录信息。

   > [!NOTE]
   > 由于 SharePoint 场在 Azure 中受长期保留保护，因此 MABS 服务器上没有可用的目录信息（元数据）。 这样，每当需要恢复时间点 SharePoint 内容数据库时，都需要重新编录 SharePoint 场。
   >
   >
3. 选择 " **重新编录**"。

    ![MABS SharePoint 保护 10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    此时显示“**云重新编录**”状态窗口。

    ![MABS SharePoint 保护 11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    完成编录后，状态更改为“成功” 。 选择“关闭”。

    ![MABS SharePoint 保护 12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. 选择 MABS " **恢复** " 选项卡中显示的 SharePoint 对象，以获取内容数据库结构。 右键单击该项目，然后选择 " **恢复**"。

    ![MABS SharePoint 保护 13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. 此时，请按照本文前面介绍的恢复步骤，从磁盘恢复 Sharepoint 内容数据库。

## <a name="switching-the-front-end-web-server"></a>切换前端 Web 服务器

如果你有多个前端 Web 服务器，并且希望切换 MABS 用于保护场的服务器，请按照说明进行操作：

以下过程使用具有两个前端 Web 服务器（Server1 和 Server2）的服务器场的示例 。 MABS 使用 Server1 保护场。 将 MABS 使用的前端 Web 服务器更改为 Server2，以便可以从场中删除 Server1 。

> [!NOTE]
> 如果 MABS 用于保护场的前端 Web 服务器不可用，请使用以下过程，从步骤 4 开始操作来更改前端 Web 服务器。

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>更改 MABS 用于保护场的前端 Web 服务器

1. 通过在命令提示符处运行以下命令，在 Server1 上停止 SharePoint VSS 编写器服务：

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. 在 Server1 上，打开“注册表编辑器”并导航到以下项：

   HKLM\System\CCS\Services\VSS\VssAccessControl

1. 检查 VssAccessControl 子项中列出的所有值。 如果任何条目的数值数据为 0，并且另一个 VSS 编写器正在关联的帐户凭据下运行，请将数值数据更改为 1。

1. 在 Server2 上安装保护代理。

   > [!WARNING]
   > 如果两个服务器位于同一域中，则只能切换 Web 前端服务器。

1. 在 Server2 上的命令提示符处，将目录更改为 `_MABS installation location_\bin\` 并运行 ConfigureSharepoint。 有关 ConfigureSharePoint 的详细信息，请参阅[配置备份](#configure-backup)。

1. 选择服务器场所属的保护组，然后选择 " **修改保护组**"。

1. 在“修改组”向导中，在“选择组成员”页上展开 Server2 并选择服务器场，然后完成向导。

   这将启动一致性检查。

1. 如果执行了步骤 6，那么现在可以从保护组中删除该卷。

## <a name="next-steps"></a>后续步骤

* 请参阅[备份文件和应用程序](backup-mabs-files-applications-azure-stack.md)一文。
* 请参阅[在 Azure Stack 上备份 SQL Server](backup-mabs-sql-azure-stack.md) 一文。
