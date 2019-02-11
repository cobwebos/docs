---
title: Azure SQL 数据同步的数据同步代理 | Microsoft Docs
description: 了解如何安装并运行 Azure SQL 数据同步的数据同步代理来将数据与本地 SQL Server 数据库进行同步
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 78d85239e1e82c290b210d33d3ca7000d6a05a1f
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567904"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Azure SQL 数据同步的数据同步代理

通过安装并配置 Azure SQL 数据同步的数据同步代理来将数据与本地 SQL Server 数据库进行同步。有关 SQL 数据同步的详细信息，请参阅[使用 SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)。

> [!IMPORTANT]
> 目前，Azure SQL 数据同步**不**支持 Azure SQL 数据库托管实例。

## <a name="download-and-install"></a>下载并安装

若要下载数据同步代理，请转到 [SQL Azure 数据同步代理](https://www.microsoft.com/download/details.aspx?id=27693)。

### <a name="install-silently"></a>以无提示方式安装

若要从命令提示符以无提示方式安装数据同步代理，请输入类似于以下示例的命令。 检查下载的 .msi 文件的文件名，并为 **TARGETDIR** 和 **SERVICEACCOUNT** 参数提供你自己的值。

- 如果没有为 **TARGETDIR** 提供值，则默认值为 `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`。

- 如果提供 `LocalSystem` 作为 **SERVICEACCOUNT** 的值，请在将代理配置为连接到本地 SQL Server 时使用 SQL Server 身份验证。

- 如果提供域用户帐户或本地用户帐户作为 **SERVICEACCOUNT** 的值，则还必须使用 **SERVICEPASSWORD** 参数提供密码。 例如，`SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`。

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>将数据与本地 SQL Server 进行同步

若要配置数据同步代理以便可以将数据与一个或多个本地 SQL Server 数据库进行同步，请参阅[添加本地 SQL Server 数据库](sql-database-get-started-sql-data-sync.md#add-on-prem)。

## <a name="agent-faq"></a> 数据同步代理常见问题解答

### <a name="why-do-i-need-a-client-agent"></a>为什么需要客户端代理

SQL 数据同步服务通过客户端代理与 SQL Server 数据库进行通信。 此安全功能可防止与防火墙后的数据库进行直接通信。 与代理通信时，SQL 数据同步服务使用加密连接和唯一令牌或代理密钥。 SQL Server 数据库使用连接字符串和代理密钥对代理进行身份验证。 这种设计为数据提供高度安全性。

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>可以运行多少个本地代理 UI 实例

只能运行一个 UI 实例。

### <a name="how-can-i-change-my-service-account"></a>如何更改服务帐户

安装客户端代理后，更改服务帐户的唯一方法是卸载它，然后使用新的服务帐户安装新的客户端代理。

### <a name="how-do-i-change-my-agent-key"></a>如何更改我的代理密钥

一个代理只能使用一次代理密钥。 删除并重新安装新代理后，不能重复使用它，它也不能被多个代理使用。 如果需要为现有代理新建密钥，必须确保使用客户端代理和 SQL 数据同步服务记录相同密钥。

### <a name="how-do-i-retire-a-client-agent"></a>如何停用客户端代理

若要立即使代理失效或停用代理，请在门户中重新生成其密钥，但不要在代理 UI 中提交。 无论相应的代理处于联机还是脱机状态，重新生成密钥都会使以前的密钥失效。

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>如何将客户端代理移至另一台计算机

如果想要从另一台计算机上运行本地代理，请执行以下操作：

1. 在所需的计算机上安装代理。
2. 登录 SQL 数据同步门户，为新代理重新生成代理密钥。
3. 使用新代理的 UI 提交新代理密钥。
4. 客户端代理下载以前已注册的本地数据库列表时，请稍候。
5. 为显示为无法访问的所有数据库提供数据库凭据。 这些数据库必须可从安装代理的新计算机上访问。

## <a name="agent-tshoot"></a> 解决数据同步代理问题

- [客户端代理安装、卸载或修复失败](#agent-install)

- [取消卸载后，客户端代理无法正常运行](#agent-uninstall)

- [代理列表中未列出我的数据库](#agent-list)

- [客户端代理未启动（错误 1069）](#agent-start)

- [无法提交代理密钥](#agent-key)

- [如果与客户端代理关联的本地数据库不可访问，则无法从门户删除该客户端代理](#agent-delete)

- [本地同步代理应用无法连接到本地同步服务](#agent-connect)

### <a name="agent-install"></a>客户端代理安装、卸载或修复失败

- **原因**。 许多情况会导致这种失败。 若要确定具体原因，请查看日志。

- **解决方法**。 若要找到失败的具体原因，请生成并查看 Windows Installer 日志。 可以在命令提示符下启用日志记录。 例如，如果下载的安装文件为 `SQLDataSyncAgent-2.0-x86-ENU.msi`，请使用以下命令行生成并检查日志文件：

    -   对于安装：`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
    -   对于卸载：`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    也可以对 Windows Installer 执行的所有安装启用日志记录。 Microsoft 知识库文章[如何启用 Windows Installer 日志记录](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging)提供了启用 Windows Installer 的日志记录的一键式解决方案。 此外它还提供了日志的位置。

### <a name="agent-uninstall"></a>取消卸载后，客户端代理无法正常运行

即使取消卸载，客户端代理仍然无法正常运行。

- **原因**。 出现此问题的原因是 SQL 数据同步客户端代理未存储凭据。

- **解决方法**。 可尝试以下两种解决方法：

    -   使用 services.msc 重新输入客户端代理的凭据。
    -   卸载此客户端代理并安装新代理。 从[下载中心](https://go.microsoft.com/fwlink/?linkid=221479)下载和安装最新的客户端代理。

### <a name="agent-list"></a>代理列表中未列出我的数据库

尝试将现有 SQL Server 数据库添加到同步组时，代理列表中未显示该数据库。

以下情况可能会导致此问题：

- **原因**。 客户端代理和同步组位于不同的数据中心。

- **解决方法**。 客户端代理和同步组必须位于同一个数据中心。 可使用以下两个选项实现这种设置：

    -   在同步组所在的数据中心创建新代理。 然后，将数据库注册到该代理。
    -   删除当前同步组。 然后，在代理所在的数据中心重新创建同步组。

- **原因**。 客户端代理的数据库列表不是最新的。

- **解决方法**。 停止客户端代理服务，然后重新启动该服务。

    本地代理仅在首次提交代理密钥时下载关联的数据库列表。 以后提交代理密钥时，它不会下载关联的数据库列表。 在代理移动期间注册的数据库不会显示在原始代理实例中。

### <a name="agent-start"></a>客户端代理未启动（错误 1069）

发现代理未在托管 SQL Server 的计算机上运行。 尝试手动启动代理时出现一个对话框，其中显示消息“错误 1069:由于登录失败，服务未启动。”

![数据同步错误 1069 对话框](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **原因**。 出现此错误可能的原因是，自从创建代理和代理密码后，本地服务器上的密码已更改。

- **解决方法**。 将代理的密码更新为当前服务器密码：

  1. 找到 SQL 数据同步客户端代理服务。  
    a. 选择“启动”。  
    b. 在搜索框中输入 **services.msc**。  
    c. 在搜索结果中，选择“服务”。  
    d. 在“服务”窗口中，滚动到 **SQL 数据同步代理**所对应的条目。  
  1. 右键单击“SQL 数据同步代理”并选择“停止”。
  1. 右键单击“SQL 数据同步代理”并选择“属性”。
  1. 在“SQL 数据同步代理属性”中，选择“登录”选项卡。
  1. 在“密码”框中输入自己的密码。
  1. 在“确认密码”框中再次输入自己的密码。
  1. 依次选择“应用”、“确定”。
  1. 在“服务”窗口中，右键单击“SQL 数据同步代理”服务并单击“启动”。
  1. 关闭“服务”窗口。

### <a name="agent-key"></a>无法提交代理密钥

创建或重新创建代理密钥后，尝试通过 SqlAzureDataSyncAgent 应用程序提交该密钥时， 提交过程无法完成。

![同步错误对话框 - 无法提交代理密钥](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **先决条件**。 在继续之前，请先检查是否符合以下先决条件：

  - SQL 数据同步 Windows 服务正在运行。

  - SQL 数据同步 Windows 服务的服务帐户具有网络访问权限。

  - 出站 1433 端口在本地防火墙规则中处于打开状态。

  - 本地 IP 已添加到同步元数据库的服务器或数据库防火墙规则。

- **原因**。 代理密钥唯一标识每个本地代理。 密钥必须满足以下两个条件：

  -   SQL 数据同步服务器和本地计算机上的客户端代理密钥必须相同。
  -   客户端代理密钥仅能使用一次。

- **解决方法**。 如果代理无法正常运行，是因为不满足其中一个条件或这两个条件都不满足。 若要使代理再次运行：

  1. 生成一个新密钥。
  1. 向代理应用新密钥。

  向代理应用新密钥：

  1. 在文件资源管理器中，转到代理安装目录。 默认安装目录为 C:\\Program Files (x86)\\Microsoft SQL Data Sync。
  1. 双击“bin”子目录。
  1. 打开 SqlAzureDataSyncAgent 应用程序。
  1. 选择“提交代理密钥”。
  1. 在提供的空白处粘贴剪贴板中的密钥。
  1. 选择“确定”。
  1. 关闭程序。

### <a name="agent-delete"></a>如果与客户端代理关联的本地数据库不可访问，则无法从门户删除该客户端代理

如果注册到 SQL 数据同步客户端代理的本地终结点（即数据库）不可访问，则无法将该客户端代理删除。

- **原因**。 无法删除本地代理，因为该代理中仍然注册了不可访问的数据库。 尝试删除代理时，删除过程尝试访问数据库，操作失败。

- **解决方法**。 使用“强制删除”来删除不可访问的数据库。

> [!NOTE]
> 如果在执行“强制删除”后，同步元数据表仍然存在，请使用 `deprovisioningutil.exe` 将其清除。

### <a name="agent-connect"></a>本地同步代理应用无法连接到本地同步服务

- **解决方法**。 请尝试以下步骤：

  1. 退出应用。  
  1. 打开组件服务面板。  
    a. 在任务栏上的搜索框中输入 **services.msc**。  
    b. 在搜索结果中，双击“服务”。  
  1. 停止“SQL 数据同步”服务。
  1. 重启“SQL 数据同步”服务。  
  1. 重新打开应用。

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>从命令提示符运行数据同步代理

可以从命令提示符运行以下数据同步代理命令：

### <a name="ping-the-service"></a>对服务执行 Ping 命令

#### <a name="usage"></a>使用情况

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>示例

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>显示已注册的数据库

#### <a name="usage"></a>使用情况

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>示例

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>提交代理密钥

#### <a name="usage"></a>使用情况

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>示例

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>注册数据库

#### <a name="usage"></a>使用情况

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>示例

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>取消注册数据库

使用此命令取消注册数据库时，它会完全取消数据库的设置。 如果数据库参与其他同步组，则此操作会中断其他同步组。

#### <a name="usage"></a>使用情况

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>示例

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>更新凭据

#### <a name="usage"></a>使用情况

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>示例

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>后续步骤

有关 SQL 数据同步的详细信息，请参阅以下文章：

-   概述 - [使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)
-   设置数据同步
    - 在门户中 - [教程：设置 SQL 数据同步，以在 Azure SQL 数据库和本地 SQL Server 之间同步数据](sql-database-get-started-sql-data-sync.md)
    - 使用 PowerShell
        -  [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)
-   最佳做法 - [Azure SQL 数据同步最佳做法](sql-database-best-practices-data-sync.md)
-   监视 - [使用 Log Analytics 监视 SQL 数据同步](sql-database-sync-monitor-oms.md)
-   故障排除 - [排查 Azure SQL 数据同步问题](sql-database-troubleshoot-data-sync.md)
-   更新同步架构
    -   使用 Transact-SQL - [在 Azure SQL 数据同步中自动复制架构更改](sql-database-update-sync-schema.md)
    -   使用 PowerShell - [使用 PowerShell 更新现有同步组中的同步架构](scripts/sql-database-sync-update-schema.md)
