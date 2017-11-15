---
title: "Azure SQL 数据同步故障排除| Microsoft 文档"
description: "了解如何排查 Azure SQL 数据同步的常见问题"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: bbfcac5a54f04f20dbdeeecef7c06b91128b8c6a
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="troubleshoot-issues-with-azure-sql-data-sync-preview"></a>解决 Azure SQL 数据同步（预览版）的问题

本文介绍如何解决对 SQL 数据同步（预览版）团队当前已知的问题。 如果有解决问题的方法，则在此处提供。

有关 SQL 数据同步的概述，请参阅[使用 Azure SQL 数据同步（预览版）跨多个云和本地数据库同步数据](sql-database-sync-data.md)。
                                                           
## <a name="my-client-agent-doesnt-work"></a>我的客户端代理不能正常运行

### <a name="description-and-symptoms"></a>描述和症状

在尝试使用客户端代理时收到以下错误消息。

“同步失败并显示异常：尝试反序列化参数 www.microsoft.com/.../05:GetBatchInfoResult 时出错。” 有关详细信息，请参阅 InnerException。

“内部异常消息：类型 “Microsoft.Synchronization.ChangeBatch” 是无效的集合类型，因为它没有默认的构造函数。”


### <a name="cause"></a>原因

此错误是 SQL 数据同步（预览版）的问题。

出现此问题最可能的原因是：

-   你正在运行 Windows 8 开发人员预览版，

-   或者，你安装了 .NET 4.5。

### <a name="solution-or-workaround"></a>解决方案或解决方法

确保安装客户端代理的计算机上没有运行 Windows 8 开发人员预览版，且没有安装 .NET Framework 4.5。

## <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>取消卸载后，我的客户端代理不能正常运行

### <a name="description-and-symptoms"></a>描述和症状

即使在取消卸载后，我的客户端代理仍然不能正常运行。

### <a name="cause"></a>原因

出现此问题是因为 SQL 数据同步（预览版）客户端代理没有存储凭据。

### <a name="solution-or-workaround"></a>解决方案或解决方法

可以尝试使用以下两种解决方案：

-   首先，使用 services.msc 为客户端代理重新输入凭据。

-   其次，卸载此客户端并安装新的客户端。 从[下载中心](http://go.microsoft.com/fwlink/?linkid=221479)下载和安装最新的客户端代理。

## <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>代理下拉列表中未列出我的数据库

### <a name="description-and-symptoms"></a>描述和症状

尝试将现有 SQL Server 数据库添加到同步组时，在下拉列表中未列出该数据库。

### <a name="cause"></a>原因

出现此问题有多个可能的原因：

-   客户端代理和同步组位于不同的数据中心。

-   客户端代理的数据库列表不是最新的。

### <a name="solution"></a>解决方案

解决方案要取决于问题的原因。

#### <a name="the-client-agent-and-sync-group-are-in-different-data-centers"></a>客户端代理和同步组位于不同的数据中心

必须将客户端代理和同步组置于同一数据中心中。 可以通过执行以下操作之一来设置此配置：

-   在与同步组相同的数据中心中创建新的代理。 然后使用该代理注册数据库。 有关详细信息，请参阅[如何安装 SQL 数据同步（预览版）客户端代理](#install-a-sql-data-sync-client-agent)。

-   删除当前同步组。 然后在与代理相同的数据中心中重新创建同步组。

#### <a name="the-client-agents-list-of-databases-is-not-current"></a>客户端代理的数据库列表不是最新的

停止客户端代理服务，然后重新启动该服务。
本地代理仅在首次提交代理密钥时下载关联的数据库列表，不会在后续提交代理密钥时进行下载。 因此，在代理移动期间所注册的数据库不会显示在原始代理实例上。

## <a name="client-agent-doesnt-start-error-1069"></a>客户端代理未启动（错误 1069）

### <a name="description-and-symptoms"></a>描述和症状

发现代理没有在托管 SQL Server 的计算机上运行。 尝试手动启动代理时，出现错误对话框，并显示“错误 1069: 由于登录失败，服务未启动。”的错误消息。

![数据同步错误 1069 对话框](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

### <a name="cause"></a>原因

出现此错误可能的原因是，自你创建代理并为其提供登录密码后，本地服务器上的密码发生了更改。

### <a name="solution-or-workaround"></a>解决方案或解决方法

将代理的密码更新为当前服务器密码。

1. 查找 SQL 数据同步（预览版）客户端代理预览服务。

    a. 单击“开始”屏幕。

    b. 在搜索框中键入“services.msc”。

    c. 在搜索结果中，单击“服务”。

    d.单击“下一步”。 在“服务”窗口中，滚动到 **SQL 数据同步（预览版）代理预览**的条目。

2. 右键单击“条目”并选择“停止”。

3. 右键单击“条目”，然后单击“属性”。

4. 在“SQL 数据同步（预览版）代理预览属性”窗口中，单击“登录”选项卡。

5. 在“密码”文本框中输入你的密码。

6. 在“确认密码”文本框中确认你的密码。

7. 单击“应用”，并单击“确定”。

8. 在“服务”窗口中，右键单击“SQL 数据同步（预览版）代理预览”服务，然后单击“启动”。

9. 关闭“服务”窗口。

## <a name="i-get-a-disk-out-of-space-message"></a>我收到“磁盘空间不足”的消息

### <a name="cause"></a>原因

应删除的文件未被删除时，可能会显示“磁盘空间不足”的消息。 这种情况可能是使用防病毒软件导致的，也可能是因为在尝试删除操作时文件处于打开状态。

### <a name="solution"></a>解决方案

解决方案是手动删除 `%temp%` (`del \*sync\* /s`) 下的同步文件，然后同时删除子目录。

> [!IMPORTANT]
> 请等待，直到同步完成，然后删除所有文件。

## <a name="i-cannot-delete-my-sync-group"></a>无法删除同步组

### <a name="description-and-symptoms"></a>描述和症状

尝试删除同步组失败。

### <a name="causes"></a>原因

以下任一原因都可能导致删除同步组失败。

-   客户端代理处于脱机状态。

-   客户端代理已卸载或缺失。 

-   数据库处于脱机状态。 

-   同步组正在进行预配或同步。 

### <a name="solutions"></a>解决方案

若要解决删除同步组失败的问题，请检查以下事项：

-   请确保客户端代理处于联机状态，然后重试。

-   客户端代理是否已卸载或缺失：

    a. 如果代理 XML 文件依然存在，请将其从 SQL 数据同步（预览版）安装文件夹中删除。

    b. 从同一台/其他本地计算机上安装代理，为显示脱机状态的代理提交从门户生成的代理密钥。

-   **SQL 数据同步（预览版）服务已停止。**

    a. 在“开始”菜单中，搜索服务。

    b. 在搜索结果中，单击“服务”。

    c. 找到 **SQL 数据同步（预览版）预览**服务。

    d.单击“下一步”。 如果服务状态为“已停止”，右键单击服务名称并从下拉菜单中选择“开始”。

-   检查 SQL 数据库和 SQL Server 数据库，确保它们都处于联机状态。

-   请等待，直到预配或同步过程完成。 然后重试删除同步组。

## <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>针对与客户端代理关联的本地数据库的门户 UI 同步失败

### <a name="description-and-symptoms"></a>描述和症状

与代理关联的本地数据库的 SQL 数据同步（预览版）门户 UI 的同步失败。 在运行代理的本地计算机上，在事件日志发现 System.IO.IOException 错误，指示磁盘空间不足。

### <a name="solution-or-workaround"></a>解决方案或解决方法

为 %TEMP% 目录所在的驱动器创建更多空间。

## <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>无法注销本地 SQL Server 数据库

### <a name="cause"></a>原因

这很可能是因为正在尝试注销的数据库已被删除。

### <a name="solution-or-workaround"></a>解决方案或解决方法

若要注销本地 SQL Server 数据库，请选择该数据库并单击“强制删除”。

如果此操作无法从同步组中删除数据库，请执行以下操作：

1. 停止客户端代理主机服务，然后重新启动该服务。

    a. 单击“开始”菜单。

    b. 在搜索框中键入“services.msc”。

    c. 在结果窗格的“程序”部分，双击“服务”。

    d.单击“下一步”。 找到并右键单击服务“SQL 数据同步（预览版）”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 如果该服务正在运行，请将其停止。

    f. 右键单击并选择“启动”。

    g. 检查是否已不再注册数据库。 如果已不再注册数据库，则操作完成。 否则，请继续执行下一步。

2. 打开客户端代理应用 (SqlAzureDataSyncAgent)。

3. 单击“编辑凭据”并提供数据库凭据，以使其可以访问。

4. 继续执行注销。

## <a name="i-cannot-submit-the-agent-key"></a>无法提交代理密钥

### <a name="description-and-symptoms"></a>描述和症状

创建或重新创建代理密钥后，尝试通过 SqlAzureDataSyncAgent 应用程序提交该密钥，但提交过程无法完成。

![同步错误对话框 - 无法提交代理密钥](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

在继续前，请确保以下条件之一不是导致此问题的原因。

-   SQL 数据同步（预览版）Windows 服务正在运行。

-   SQL 数据同步（预览版）预览 Windows 服务的服务帐户具有网络访问权限。

-   客户端代理能够联系定位符服务。 请检查以下注册表项具有值“https://locator.sync.azure.com/LocatorServiceApi.svc”

    -   x86 计算机：`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   x64 计算机：`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

### <a name="cause"></a>原因

代理密钥唯一标识每个本地代理。 密钥必须满足以下两个条件才有效：

-   SQL 数据同步（预览版）服务器和本地计算机上的客户端代理密钥必须相同。

-   客户端代理密钥仅能使用一次。

### <a name="solution-or-workaround"></a>解决方案或解决方法

如果代理无法正常运行，是因为不满足其中一个条件或这两个条件都不满足。 若要使代理再次运行：

1. 生成一个新密钥。

2. 向代理应用新密钥。

若要向代理应用新密钥，请执行以下操作：

1. 使用文件资源管理器导航到你的代理安装目录。 默认安装目录为 `c:\\program files (x86)\\microsoft sql data sync`。

2. 双击 `bin` 子目录。

3. 启动 `SqlAzureDataSyncAgent` 应用程序。

4. 单击“提交代理密钥”。

5. 在提供的空白处粘贴剪贴板中的密钥。

6. 单击 **“确定”**。

7. 关闭程序。

## <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>我没有足够的权限来启动系统服务

### <a name="cause"></a>原因

在两种情况下会发生此错误：

-   用户名和/或密码不正确。

-   指定的用户帐户没有足够的权限作为服务登录。

### <a name="solution-or-workaround"></a>解决方案或解决方法

向用户帐户授予“作为服务登录”凭据。

1. 导航到“启动 | 控制面板 | 管理工具 | 本地安全策略 |本地策略 |用户权限管理”。

2. 找到并单击“作为服务登录”条目。

3. 在“作为服务登录”属性对话框中添加用户帐户。

4. 单击“应用”，并单击“确定”。

5. 关闭窗口。

## <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>本地同步代理应用无法连接到本地同步服务

### <a name="solution-or-workaround"></a>解决方案或解决方法

请尝试以下步骤：

1. 退出应用。

2. 打开组件服务面板。

    a. 在任务栏上的搜索框中键入“services.msc”。

    b. 在搜索结果中，双击“服务”。

3. 停止，然后重新启动“SQL 数据同步（预览版）预览”服务。

4. 重新启动应用。

## <a name="install-uninstall-or-repair-fails"></a>安装、卸载或修复错误

### <a name="cause"></a>原因

导致此错误有多个可能的原因。 若要确定导致此错误的具体原因，需要查看日志。

### <a name="solution-or-workaround"></a>解决方案或解决方法

若要找到所遇错误的具体原因，需要生成并查看 Windows Installer 日志。 可以从命令行启用日志记录。 例如，假定所下载的 AgentServiceSetup.msi 文件是 LocalAgentHost.msi。 使用以下命令行生成并检查日志文件：

-   对于安装：`msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   对于卸载：`msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

也可以对 Windows Installer 执行的所有安装启用日志记录。 Microsoft 知识库文章[如何启用 Windows Installer 日志记录](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)提供了启用 Windows Installer 的日志记录的一键式解决方案。 此外它还提供了这些日志的位置。

## <a name="a-database-has-an-out-of-date-status"></a>数据库具有“过期”状态

### <a name="cause"></a>原因

SQL 数据同步（预览版）会从服务中删除已脱机达 45 天或更长时间的数据库（从数据库脱机时算起）。 如果数据库已脱机达 45 天或更长的时间，然后重新联机，则其状态被设置为“过期”。

### <a name="solution-or-workaround"></a>解决方案或解决方法

为了避免“过期”状态，请确保所有数据库都未脱机达 45 天或更长的时间。

如果某个数据库的状态为“脱机”，需要执行以下操作：

1. 从同步组删除“过期”数据库。

2. 将数据库添加回同步组。

> [!WARNING]
> 在数据库脱机时，将丢失对该数据库所做的所有更改。

## <a name="a-sync-group-has-an-out-of-date-status"></a>同步组具有“过期”状态

### <a name="cause"></a>原因

如果在 45 天的整个保留期内未能应用一个或多个更改，则同步组可能会过期。

### <a name="solution-or-workaround"></a>解决方案或解决方法

若要避免“过期”状态，请定期检查历史记录查看器中的同步作业结果，并调查和解决未能应用的任何更改。

如果同步组的状态为“过期”，则需删除该同步组并重新创建。

## <a name="i-see-erroneous-data-in-my-tables"></a>在表中发现错误数据

### <a name="description-and-symptoms"></a>描述和症状

如果数据库中具有相同名称但来自不同架构的表包含在同步中，则在同步后，会在这些表中发现错误数据。

### <a name="cause-and-fix"></a>原因和解决方法

SQL 数据同步（预览版）预配过程将对具有相同名称但来自不同架构的表使用相同的跟踪表。 结果是，在这两个表中所做的更改都会反映在同一跟踪表中，而这一行为会导致同步期间出现错误数据更改。

### <a name="resolution-or-workaround"></a>解决方案或解决方法

确保同步中涉及的表名称不同，即使它们属于不同的架构。

## <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>在同步成功后发现主键数据不一致

### <a name="description-and-symptoms"></a>描述和症状

在系统报告同步成功，且日志没有显示失败或跳过行后，发现同步组中数据库间的主键数据不一致。

### <a name="cause"></a>原因

这是设计的行为。 任何主键列中的更改都会导致被更改主键所在行中的数据不一致。

### <a name="resolution-or-workaround"></a>解决方案或解决方法

若要避免此问题，请确保主键列中的数据没有进行任何更改。

若要在出现该问题后加以解决，必须从同步组中的所有终结点删除受影响的行，然后重新插入行。

## <a name="i-see-a-significant-degradation-in-performance"></a>发现性能显著降低

### <a name="description-and-symptoms"></a>描述和症状

性能显著降低，甚至达到了无法启动数据同步 UI 的程度。

### <a name="cause"></a>原因

最可能的原因是同步循环。 当同步组 A 的同步触发了同步组 B 的同步，进而又触发了同步组 A 的同步时，将会出现同步循环。实际情况可能更为复杂，其中会涉及循环中两个以上的同步组，但重要的因素是，存在因同步组彼此重叠而导致的同步循环触发。

### <a name="resolution-or-workaround"></a>解决方案或解决方法

最佳解决方案就是预防。 确保在同步组中没有循环引用。 由一个同步组同步的任何行都不能由其他同步组同步。

## <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>如果与客户端代理关联的本地数据库不可访问，则无法从门户删除该客户端代理。

### <a name="description-and-symptoms"></a>描述和症状

如果使用 SQL 数据同步（预览版）客户端代理注册的本地终结点（即数据库）变得不可访问，则无法将该客户端代理删除。

### <a name="cause"></a>原因

无法删除本地代理，因为不可访问的数据库仍然使用该代理注册。 尝试删除代理时，删除过程尝试访问数据库，操作失败。

### <a name="resolution-or-workaround"></a>解决方案或解决方法

使用“强制删除”来删除不可访问的数据库。

> [!NOTE]
> 如果在执行“强制删除”后，同步元数据表仍然存在，请使用 deprovisioningutil.exe 将其清除。

## <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>无法在卸载/停止代理的三分钟内删除同步组。

### <a name="description-and-symptoms"></a>描述和症状

无法在卸载/停止关联的 SQL 数据同步（预览版）客户端代理的三分钟内删除同步组。

### <a name="resolution-or-workaround"></a>解决方案或解决方法

1. 在关联的同步代理处于联机状态时删除同步组（推荐）。

2. 如果代理处于脱机状态但已安装，请在本地计算机上使其联机。 然后在 SQL 数据同步（预览版）门户中等待代理状态显示为联机状态，再删除同步组。

3. 如果代理因为被卸载而处于脱机状态，请执行以下操作。 然后尝试删除同步组。

    a.  如果代理 XML 文件依然存在，将其从 SQL 数据同步（预览版）安装文件夹中删除。

    b.  从同一台或其他本地计算机上安装代理，为显示脱机状态的代理提交从门户生成的代理密钥。

## <a name="my-sync-group-is-stuck-in-the-processing-state"></a>我的同步组停留在“正在处理”状态

### <a name="description-and-symptoms"></a>描述和症状

SQL 数据同步（预览版）中的同步组长时间处于“正在处理”状态，不响应停止命令，且日志没有显示新条目。

### <a name="causes"></a>原因

以下任何条件均可导致同步组停留在“正在处理”状态。

-   客户端代理处于脱机状态。 请确保客户端代理处于联机状态，然后重试。

-   客户端代理已卸载或缺失。 客户端代理是否已卸载或缺失：

    1. 如果代理 XML 文件依然存在，请将其从 SQL 数据同步（预览版）安装文件夹中删除。

    2. 在同一台/其他本地计算机上安装代理。 然后对显示为脱机的代理提交从门户生成的代理密钥。

-   **SQL 数据同步（预览版）服务已停止。**

    1. 在“开始”菜单中，搜索服务。

    2. 在搜索结果中，单击“服务”。

    3. 找到“SQL 数据同步（预览版）”服务。

    4. 如果服务状态为“已停止”，右键单击服务名称并从下拉菜单中选择“开始”。

### <a name="solution-or-workaround"></a>解决方案或解决方法

如果无法解决此问题，可以通过 Microsoft 支持部门重置同步组的状态。 如需重置状态，请在 [Azure SQL 数据库论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)上创建一个论坛帖子，其中包括你的订阅 ID 以及需要被重置的组的同步组 ID。 Microsoft 支持工程师将会响应你的帖子并告诉你重置状态的时间。

## <a name="next-steps"></a>后续步骤
有关 SQL 数据同步的详细信息，请参阅：

-   [使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)
-   [Azure SQL 数据同步入门](sql-database-get-started-sql-data-sync.md)
-   [Azure SQL 数据同步最佳实践](sql-database-best-practices-data-sync.md)

-   演示如何配置 SQL 数据同步的完整 PowerShell 示例：
    -   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [下载 SQL 数据同步 REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

有关 SQL 数据库的详细信息，请参阅：

-   [SQL 数据库概述](sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
