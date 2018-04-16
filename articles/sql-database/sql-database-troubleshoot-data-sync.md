---
title: 排查 Azure SQL 数据同步（预览版）问题 | Microsoft Docs
description: 了解如何排查 Azure SQL 数据同步（预览版）的常见问题
services: sql-database
ms.date: 04/01/2018
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 6e29c93f37017a88aa4b6d69168e649f7397d56b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>排查 SQL 数据同步（预览版）的问题

本文介绍如何排查 Azure SQL 数据同步（预览版）的已知问题。 如果某个问题有了解决方法，会在本文中提供。

有关 SQL 数据同步（预览版）的概述，请参阅[使用 Azure SQL 数据同步（预览版）跨多个云和本地数据库同步数据](sql-database-sync-data.md)。

## <a name="sync-issues"></a>同步问题

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>在门户 UI 中针对与客户端代理关联的本地数据库执行同步失败

#### <a name="description-and-symptoms"></a>描述和症状

在 SQL 数据同步（预览版）门户 UI 中针对与代理关联的本地数据库执行同步失败。 在运行代理的本地计算机上，事件日志中出现 System.IO.IOException 错误。 这些错误指出磁盘空间不足。

#### <a name="resolution"></a>解决方法

在 %TEMP% 目录所在的驱动器上腾出更多的空间。

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>我的同步组停留在“正在处理”状态

#### <a name="description-and-symptoms"></a>描述和症状

SQL 数据同步（预览版）中的同步组长时间处于“正在处理”状态。 该同步组不响应 **stop** 命令，并且日志中未显示新条目。

#### <a name="cause"></a>原因

以下任何条件均可导致同步组停滞在“正在处理”状态：

-   **客户端代理脱机**。 请确保客户端代理处于联机状态，然后重试。

-   **客户端代理已卸载或缺失**。 客户端代理是否已卸载或缺失：

    1. 将代理 XML 文件（如果存在）从 SQL 数据同步（预览版）安装文件夹中删除。
    2. 在某台本地计算机（可以是相同或不同的计算机）上安装代理。 然后，提交门户中针对显示为脱机的代理生成的代理密钥。

-   **SQL 数据同步服务已停止**。

    1. 在“开始”菜单中，搜索“服务”。
    2. 在搜索结果中，选择“服务”。
    3. 找到“SQL 数据同步（预览版）”服务。
    4. 如果服务状态为“已停止”，请右键单击服务名称，选择“启动”。

#### <a name="resolution"></a>解决方法

如果上述信息无法使同步组摆脱“正在处理”状态，可让 Microsoft 支持部门重置该同步组的状态。 若要重置同步组状态，请在 [Azure SQL 数据库论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)中发帖。 在贴子中，请包含自己的订阅 ID，以及需要重置的组的同步组 ID。 Microsoft 支持工程师将会回复帖子，并在重置状态后予以通知。

### <a name="i-see-erroneous-data-in-my-tables"></a>在表中发现错误数据

#### <a name="description-and-symptoms"></a>描述和症状

如果在同步操作中包含来自不同数据库架构的同名表，则同步后，表中会出现错误数据。

#### <a name="cause"></a>原因

SQL 数据同步（预览版）预配过程针对不同架构中的同名表使用相同的跟踪表。 因此，这两个表中的更改会反映在同一个跟踪表中。 这会导致同步期间发生错误的数据更改。

#### <a name="resolution"></a>解决方法

确保同步中涉及的表名称不同，即使这些表属于数据库中不同的架构。

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>同步成功后发现主键数据不一致

#### <a name="description-and-symptoms"></a>描述和症状

系统报告同步成功，且日志中未显示失败或跳过的行，但是，发现同步组中数据库间的主键数据不一致。

#### <a name="cause"></a>原因

此结果是设计使然。 任何主键列中的更改都会导致被更改主键所在行中的数据不一致。

#### <a name="resolution"></a>解决方法

若要避免此问题，请确保主键列中的数据没有进行任何更改。

若要在发生此问题后予以解决，请从同步组中的所有终结点内删除包含不一致数据的行。 然后重新插入该行。

### <a name="i-see-a-significant-degradation-in-performance"></a>发现性能显著降低

#### <a name="description-and-symptoms"></a>描述和症状

性能显著降低，甚至达到了无法打开数据同步 UI 的程度。

#### <a name="cause"></a>原因

最可能的原因是同步循环。 当同步组 A 的同步触发了同步组 B 的同步，进而又触发了同步组 A 的同步时，就会出现同步循环。实际情况可能更为复杂，其中可能涉及循环中两个以上的同步组。 问题在于，存在因同步组彼此重叠而导致的同步循环触发。

#### <a name="resolution"></a>解决方法

最佳解决方案就是预防。 确保同步组中没有循环引用。 由一个同步组同步的任何行都不能由其他同步组同步。

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>有一条错误消息指出“无法在列 \<column\> 中插入值 NULL。 此列不允许 null 值。” 这是什么意思，如何解决该错误？ 
此错误消息表示发生了两个以下问题之一：
-  某个表没有主键。 若要解决此问题，请将主键添加到要同步的所有表。
-  CREATE INDEX 语句中可能存在 WHERE 子句。 数据同步（预览版）不会处理这种情况。 若要解决此问题，请删除 WHERE 子句，或手动对所有数据库进行更改。 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>数据同步（预览版）如何处理循环引用？ 也就是说，如果在多个同步组中同步相同的数据，这些数据是否不断更改？
数据同步（预览版）不会处理循环引用。 请务必避免循环引用。 

## <a name="client-agent-issues"></a>客户端代理问题

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>客户端代理安装、卸载或修复失败

#### <a name="cause"></a>原因

许多情况会导致这种失败。 若要确定具体原因，请查看日志。

#### <a name="resolution"></a>解决方法

若要找到失败的具体原因，请生成并查看 Windows Installer 日志。 可以在命令提示符下启用日志记录。 例如，如果下载的 AgentServiceSetup.msi 文件是 LocalAgentHost.msi，可使用以下命令行生成并检查日志文件：

-   对于安装：`msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   对于卸载：`msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

也可以对 Windows Installer 执行的所有安装启用日志记录。 Microsoft 知识库文章[如何启用 Windows Installer 日志记录](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)提供了启用 Windows Installer 的日志记录的一键式解决方案。 此外它还提供了日志的位置。

### <a name="my-client-agent-doesnt-work"></a>我的客户端代理不能正常运行

#### <a name="description-and-symptoms"></a>描述和症状

尝试使用客户端代理时收到以下消息：

“同步失败并显示异常：尝试反序列化参数 www.microsoft.com/.../05:GetBatchInfoResult 时出错。” 有关详细信息，请参阅 InnerException。”

“内部异常消息：类型 “Microsoft.Synchronization.ChangeBatch” 是无效的集合类型，因为它没有默认的构造函数。”

#### <a name="cause"></a>原因

这是 SQL 数据同步（预览版）安装存在的一个已知问题。 出现此消息的最可能原因是下列其中一项：

-   正在运行 Windows 8 开发人员预览版。
-   已安装 .NET Framework 4.5。

#### <a name="resolution"></a>解决方法

确保安装客户端代理的计算机上未运行 Windows 8 开发人员预览版，且未安装 .NET Framework 4.5。

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>取消卸载后，我的客户端代理不能正常运行

#### <a name="description-and-symptoms"></a>描述和症状

即使取消卸载，客户端代理仍然无法正常运行。

#### <a name="cause"></a>原因

出现此问题的原因是 SQL 数据同步（预览版）客户端代理未存储凭据。

#### <a name="resolution"></a>解决方法

可尝试以下两种解决方法：

-   使用 services.msc 重新输入客户端代理的凭据。
-   卸载此客户端代理并安装新代理。 从[下载中心](http://go.microsoft.com/fwlink/?linkid=221479)下载和安装最新的客户端代理。

### <a name="my-database-isnt-listed-in-the-agent-list"></a>代理列表中未列出我的数据库

#### <a name="description-and-symptoms"></a>描述和症状

尝试将现有 SQL Server 数据库添加到同步组时，代理列表中未显示该数据库。

#### <a name="cause"></a>原因

以下情况可能会导致此问题：

-   客户端代理和同步组位于不同的数据中心。
-   客户端代理的数据库列表不是最新的。

#### <a name="resolution"></a>解决方法

解决方法取决于问题的原因。

- **客户端代理和同步组位于不同的数据中心**

    客户端代理和同步组必须位于同一个数据中心。 可使用以下两个选项实现这种设置：

    -   在同步组所在的数据中心创建新代理。 然后，将数据库注册到该代理。
    -   删除当前同步组。 然后，在代理所在的数据中心重新创建同步组。

- **客户端代理的数据库列表不是最新的**

    停止客户端代理服务，然后重新启动该服务。

    本地代理仅在首次提交代理密钥时下载关联的数据库列表。 以后提交代理密钥时，它不会下载关联的数据库列表。 在代理移动期间注册的数据库不会显示在原始代理实例中。

### <a name="client-agent-doesnt-start-error-1069"></a>客户端代理未启动（错误 1069）

#### <a name="description-and-symptoms"></a>描述和症状

发现代理未在托管 SQL Server 的计算机上运行。 尝试手动启动代理时出现一个对话框，其中显示消息“错误 1069: 由于登录失败，服务未启动。”

![数据同步错误 1069 对话框](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>原因

出现此错误可能的原因是，自从创建代理和代理密码后，本地服务器上的密码已更改。

#### <a name="resolution"></a>解决方法

将代理的密码更新为当前服务器密码：

1. 查找 SQL 数据同步（预览版）客户端代理预览服务。  
    a. 选择“启动”。  
    b. 在搜索框中输入 **services.msc**。  
    c. 在搜索结果中，选择“服务”。  
    d. 在“服务”窗口中，滚动到 **SQL 数据同步（预览版）代理预览**的条目。  
2. 右键单击“SQL 数据同步(预览版)代理预览版”并选择“停止”。
3. 右键单击“SQL 数据同步(预览版)代理预览版”并选择“属性”。
4. 在“SQL 数据同步(预览版)代理预览版属性”中，选择“登录”选项卡。
5. 在“密码”框中输入自己的密码。
6. 在“确认密码”框中再次输入自己的密码。
7. 依次选择“应用”、“确定”。
8. 在“服务”窗口中，右键单击“SQL 数据同步(预览版)代理预览版”服务并单击“启动”。
9. 关闭“服务”窗口。

### <a name="i-cant-submit-the-agent-key"></a>无法提交代理密钥

#### <a name="description-and-symptoms"></a>描述和症状

创建或重新创建代理密钥后，尝试通过 SqlAzureDataSyncAgent 应用程序提交该密钥时， 提交过程无法完成。

![同步错误对话框 - 无法提交代理密钥](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

在继续之前，请检查是否满足以下条件：

-   SQL 数据同步（预览版）Windows 服务正在运行。  
-   SQL 数据同步（预览版）预览 Windows 服务的服务帐户具有网络访问权限。    
-   客户端代理能够访问定位器服务。 检查以下注册表项是否具有值 https://locator.sync.azure.com/LocatorServiceApi.svc:  
    -   x86 计算机：`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   x64 计算机：`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>原因

代理密钥唯一标识每个本地代理。 密钥必须满足以下两个条件：

-   SQL 数据同步（预览版）服务器和本地计算机上的客户端代理密钥必须相同。
-   客户端代理密钥仅能使用一次。

#### <a name="resolution"></a>解决方法

如果代理无法正常运行，是因为不满足其中一个条件或这两个条件都不满足。 若要使代理再次运行：

1. 生成一个新密钥。
2. 向代理应用新密钥。

向代理应用新密钥：

1. 在文件资源管理器中，转到代理安装目录。 默认安装目录为 C:\\Program Files (x86)\\Microsoft SQL Data Sync。
2. 双击“bin”子目录。
3. 打开 SqlAzureDataSyncAgent 应用程序。
4. 选择“提交代理密钥”。
5. 在提供的空白处粘贴剪贴板中的密钥。
6. 选择“确定”。
7. 关闭程序。

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>如果与客户端代理关联的本地数据库不可访问，则无法从门户删除该客户端代理。

#### <a name="description-and-symptoms"></a>描述和症状

如果注册到 SQL 数据同步（预览版）客户端代理的本地终结点（即数据库）不可访问，则无法将该客户端代理删除。

#### <a name="cause"></a>原因

无法删除本地代理，因为该代理中仍然注册了不可访问的数据库。 尝试删除代理时，删除过程尝试访问数据库，操作失败。

#### <a name="resolution"></a>解决方法

使用“强制删除”来删除不可访问的数据库。

> [!NOTE]
> 如果在执行“强制删除”后，同步元数据表仍然存在，请使用 deprovisioningutil.exe 将其清除。

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>本地同步代理应用无法连接到本地同步服务

#### <a name="resolution"></a>解决方法

请尝试以下步骤：

1. 退出应用。  
2. 打开组件服务面板。  
    a. 在任务栏上的搜索框中输入 **services.msc**。  
    b. 在搜索结果中，双击“服务”。  
3. 停止“SQL 数据同步(预览版)”服务。
4. 重启“SQL 数据同步(预览版)”服务。  
5. 重新打开应用。

## <a name="setup-and-maintenance-issues"></a>设置和维护问题

### <a name="i-get-a-disk-out-of-space-message"></a>我收到“磁盘空间不足”的消息

#### <a name="cause"></a>原因

需要删除残留的文件时，可能会显示“磁盘空间不足”消息。 出现此消息的原因可能是使用了防病毒软件，或者在尝试执行删除操作时文件处于打开状态。

#### <a name="resolution"></a>解决方法

手动删除 %temp% 文件夹中的同步文件 (`del \*sync\* /s`)。 然后，删除 %temp% 文件夹中的子目录。

> [!IMPORTANT]
> 同步正在进行时，请不要删除任何文件。

### <a name="i-cant-delete-my-sync-group"></a>无法删除同步组

#### <a name="description-and-symptoms"></a>描述和症状

尝试删除同步组失败。

#### <a name="causes"></a>原因

以下任何情况都可能导致删除同步组失败：

-   客户端代理处于脱机状态。
-   客户端代理已卸载或缺失。 
-   数据库处于脱机状态。 
-   同步组正在预配或同步。 

#### <a name="resolution"></a>解决方法

解决无法删除同步组的问题：

-   确保客户端代理已联机，然后重试。
-   客户端代理是否已卸载或缺失：  
    a. 将代理 XML 文件（如果存在）从 SQL 数据同步（预览版）安装文件夹中删除。  
    b. 在某台本地计算机（可以是相同或不同的计算机）上安装代理。 然后，提交门户中针对显示为脱机的代理生成的代理密钥。
-   确保 SQL 数据同步（预览版）服务正在运行：  
    a. 在“开始”菜单中，搜索“服务”。  
    b. 在搜索结果中，选择“服务”。  
    c. 找到 **SQL 数据同步（预览版）预览**服务。  
    d. 如果服务状态为“已停止”，请右键单击服务名称，选择“启动”。
-   确保 SQL 数据库和 SQL Server 数据库都已联机。
-   等待预配或同步过程完成，然后重试删除同步组。

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>无法注销本地 SQL Server 数据库

#### <a name="cause"></a>原因

很可能是因为正在尝试注销一个已被删除的数据库。

#### <a name="resolution"></a>解决方法

若要注销某个本地 SQL Server 数据库，请选择该数据库，再选择“强制删除”。

如果此操作无法从同步组中删除数据库：

1. 请停止再重启客户端代理主机服务。  
    a. 选择“开始”菜单。  
    b. 在搜索框中输入 **services.msc**。  
    c. 在搜索结果窗格的“程序”部分，双击“服务”。  
    d. 右键单击“SQL 数据同步(预览版)”服务。  
    e. 如果该服务正在运行，请将其停止。  
    f. 右键单击该服务，并选择“启动”。  
    g. 检查数据库是否仍已注册。 如果已不再注册数据库，则操作完成。 否则，请继续执行下一步。
2. 打开客户端代理应用 (SqlAzureDataSyncAgent)。
3. 选择“编辑凭据”，输入数据库的凭据。
4. 继续执行注销。

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>没有足够的特权启动系统服务

#### <a name="cause"></a>原因

在两种情况下会发生此错误：
-   用户名和/或密码不正确。
-   指定的用户帐户没有足够的特权作为服务登录。

#### <a name="resolution"></a>解决方法

向用户帐户授予“作为服务登录”凭据：

1. 转到“开始” > “控制面板” > “管理工具” > “本地安全策略” > “本地策略” > “用户权限管理”。
2. 选择“作为服务登录”。
3. 在“属性”对话框中添加用户帐户。
4. 依次选择“应用”、“确定”。
5. 关闭所有窗口。

### <a name="a-database-has-an-out-of-date-status"></a>数据库具有“过期”状态

#### <a name="cause"></a>原因

SQL 数据同步（预览版）会从服务中删除已脱机达 45 天或更长时间的数据库（从数据库脱机时算起）。 如果数据库已脱机达 45 天或更长时间，然后重新联机，则其状态会变为“过期”。

#### <a name="resolution"></a>解决方法

确保所有数据库都不会脱机达 45 天或更长时间，即可避免“过期”状态。

如果数据库的状态为“过期”：

1. 从同步组中删除处于“过期”状态的数据库。
2. 将数据库添加回同步组。

> [!WARNING]
> 在数据库脱机时，将丢失对该数据库所做的所有更改。

### <a name="a-sync-group-has-an-out-of-date-status"></a>同步组具有“过期”状态

#### <a name="cause"></a>原因

如果在 45 天的整个保留期内未能应用一个或多个更改，则同步组可能会过期。

#### <a name="resolution"></a>解决方法

若要避免“过期”状态，请定期在历史记录查看器中检查同步作业的结果。 调查并解决未能应用的任何更改。

如果同步组的状态为“过期”，请删除同步组并重新创建。

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>在卸载或停止代理后的三分钟内无法删除同步组

#### <a name="description-and-symptoms"></a>描述和症状

在卸载或停止关联的 SQL 数据同步（预览版）客户端代理后的三分钟内无法删除同步组。

#### <a name="resolution"></a>解决方法

1. 在关联的同步代理处于联机状态时删除同步组（推荐）。
2. 如果代理处于脱机状态但已安装，请在本地计算机上将其联机。 等待代理状态在 SQL 数据同步（预览版）门户中显示为“联机”。 然后删除同步组。
3. 如果代理因为被卸载而处于脱机状态：  
    a.  将代理 XML 文件（如果存在）从 SQL 数据同步（预览版）安装文件夹中删除。  
    b.  在某台本地计算机（可以是相同或不同的计算机）上安装代理。 然后，提交门户中针对显示为脱机的代理生成的代理密钥。  
    c. 尝试删除同步组。

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>还原丢失或损坏的数据库时，会发生什么情况？

如果从备份还原丢失或损坏的数据库，数据库所属的同步组中的数据可能不收敛。

## <a name="next-steps"></a>后续步骤
有关 SQL 数据同步（预览版）的详细信息，请参阅：

-   [使用 Azure SQL 数据同步（预览版）跨多个云和本地数据库同步数据](sql-database-sync-data.md)  
-   [安装 Azure SQL 数据同步（预览版）](sql-database-get-started-sql-data-sync.md)  
-   [有关 Azure SQL 数据同步（预览版）的最佳做法](sql-database-best-practices-data-sync.md)  
-   [使用 Log Analytics 监视 Azure SQL 数据同步（预览版）](sql-database-sync-monitor-oms.md)  
-   演示如何配置 SQL 数据同步（预览版）的完整 PowerShell 示例：  
    -   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [下载 SQL 数据同步（预览版）REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

有关 SQL 数据库的详细信息，请参阅：

-   [SQL 数据库概述](sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
