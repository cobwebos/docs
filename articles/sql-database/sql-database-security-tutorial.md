---
title: "保护 Azure SQL 数据库 | Microsoft Docs"
description: "介绍保护 Azure SQL 数据库的技术和功能。"
services: sql-database
documentationcenter: 
author: DRediske
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/07/2017
ms.author: daredis
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 34815f5b716a38f957392d8955f924eeb6fe621e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="secure-your-azure-sql-database"></a>保护 Azure SQL 数据库

SQL 数据库通过限制对数据库的访问来保护数据，具体措施包括：使用防火墙规则，实施要求用户证明其身份的身份验证机制，通过基于角色的成员身份和权限实施数据访问授权，以及使用行级别安全性和动态数据屏蔽。

只需几个简单的步骤，即可大大提升数据库抵御恶意用户或未经授权的访问的能力。 在本教程中，你将学习： 

> [!div class="checklist"]
> * 为服务器和/或数据库设置防火墙规则
> * 使用安全连接字符串连接到数据库
> * 管理用户访问权限
> * 通过加密保护数据
> * 启用 SQL 数据库审核
> * 启用 SQL 数据库威胁检测

若要完成本教程，请确保已安装 Excel 和最新版的 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)。


## <a name="set-up-firewall-rules-for-your-database"></a>设置数据库的防火墙规则

SQL 数据库受 Azure 中的防火墙保护。 默认情况下，将拒绝与服务器和服务器内数据库的所有连接，连接自其他 Azure 服务的连接除外。 最安全的配置是将“允许访问 Azure 服务”设置为“关闭”。 如果需要从 Azure VM 或云服务连接到数据库，则应创建[保留 IP](../virtual-network/virtual-networks-reserved-public-ip.md)，且仅允许保留的 IP 地址通过防火墙进行访问。 

按照以下步骤为服务器创建 [SQL 数据库服务器级防火墙规则](sql-database-firewall-configure.md)，以允许从特定 IP 地址进行连接。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 从左侧菜单中单击“SQL 数据库”，然后在“SQL 数据库”页单击要为其配置防火墙规则的数据库。 此时将打开数据库的概述页，其中显示了完全限定的服务器名称（例如 **mynewserver-20170313.database.windows.net**），并提供了其他配置的选项。

      ![服务器防火墙规则](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. 如上图所示，在工具栏上单击“设置服务器防火墙”。 此时会打开 SQL 数据库服务器的“防火墙设置”页。 

3. 在工具栏单击“添加客户端 IP”，添加连接到门户的计算机的公共 IP 地址，或者手动输入防火墙规则，然后单击“保存”。

      ![设置服务器防火墙规则](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. 单击“确定”，然后单击“X”关闭“防火墙设置”页。

现可使用指定的 IP 地址或 IP 地址范围连接到服务器中的任何数据库。

> [!NOTE]
> 通过端口 1433 进行的 SQL 数据库通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到 Azure SQL 数据库服务器，除非你的 IT 部门打开了端口 1433。
>

如果要求对同一逻辑服务器内的不同数据库采用不同的防火墙设置，则必须为每个数据库创建数据库级规则。 数据库级防火墙规则只能通过使用 Transact-SQL 语句进行配置，而且只能在配置了第一个服务器级防火墙规则后才能配置。 按照以下步骤操作，创建特定于数据库的防火墙规则。

1. 例如，使用 [SQL Server Management Studio](./sql-database-connect-query-ssms.md) 连接到数据库。

2. 在对象资源管理器中，右键单击要为其添加防火墙规则的数据库，然后单击“新建查询”。 此时会打开一个空白查询窗口，该窗口连接到数据库。

3. 在查询窗口中输入以下查询：

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. 在工具栏上，单击“执行”创建防火墙规则。

## <a name="connect-to-your-database-using-a-secure-connection-string"></a>使用安全连接字符串连接到数据库

若要确保客户端和 SQL 数据库之间安全的加密连接，连接字符串必须配置为 1) 请求加密连接和 2) 不信任服务器证书。 这会使用传输层安全性 (TLS) 建立连接并减少中间人攻击的风险。 可从 Azure 门户为支持的客户端驱动程序的 SQL 数据库获得正确配置的连接字符串，如 ADO.net 的以下屏幕截图所示。

1. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。

2. 在数据库“概述”边栏选项卡上，单击“显示数据库连接字符串”。

3. 查看完整的 ADO.NET 连接字符串。

    ![ADO.NET 连接字符串](./media/sql-database-security-tutorial/adonet-connection-string.png)


## <a name="user-management"></a>用户管理

创建任何用户前，必须首先从 Azure SQL 数据库支持的两种身份验证类型中选择一种类型： 

SQL 身份验证，为登录名和仅在逻辑服务器内特定数据库的上下文中有效的用户使用用户名和密码。 

Azure Active Directory 身份验证，使用 Azure Active Directory 管理的标识。 

如果要使用 [Azure Active Directory](./sql-database-aad-authentication.md) 对 SQL 数据库进行身份验证，必须存在已填充的 Azure Active Directory 才能继续下一步操作。

请按照以下步骤操作，使用 SQL 身份验证创建用户：

1. 连接到数据库，例如使用 [SQL Server Management Studio](./sql-database-connect-query-ssms.md)，使用服务器管理员凭据。

2. 在对象资源管理器中，右键单击要为其添加新用户的数据库，然后单击“新建查询”。 此时会打开一个空白查询窗口，该窗口连接到所选数据库。

3. 在查询窗口中输入以下查询：

    ```sql
    CREATE USER ApplicationUserUse' WITH PASSWORD = 'YourStrongPassword1';
    ```

4. 在工具栏上，单击“执行”创建用户。

5. 默认情况下，用户可连接到数据库，但没有读取或写入数据的权限。 若要向新建用户授予这些权限，可在新的查询窗口中执行以下两个命令

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUserUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUserUser;
    ```

最好在数据库级别创建这些非管理员帐户以连接到数据库，除非需要执行创建新用户等管理员任务。 请查看 [Azure Active Directory 教程](./sql-database-aad-authentication-configure.md)，了解如何使用 Azure Active Directory 进行身份验证。


## <a name="protect-your-data-with-encryption"></a>通过加密保护数据

Azure SQL 数据库透明数据加密 (TDE) 会自动加密静态数据，无需对访问加密数据库的应用程序做任何更改。 要为数据库启用 TDE，请按照下列步骤操作：

1. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 

2. 单击“透明数据加密”，打开 TDE 的配置页。

    ![透明数据加密](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. 将“数据加密”设置为打开，然后单击“保存”。

加密进程在后台启动。 可以使用 [SQL Server Management Studio](./sql-database-connect-query-ssms.md) 连接到 SQL 数据库并查询 `sys.dm_database_encryption_keys` 视图的 encryption_state 列，来监视进度。

## <a name="enable-sql-database-auditing"></a>启用 SQL 数据库审核

Azure SQL 数据库审核跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。 审核可帮助你一直保持遵从法规、了解数据库活动，以及深入了解可以指明潜在安全违规的偏差和异常。 请按照以下步骤操作，为 SQL 数据库创建默认审核策略：

1. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。

2. 在“设置”边栏选项卡中，选择“审核和威胁检测”。

    ![“审核”边栏选项卡](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. 在数据库审核配置边栏选项卡中，可以选中“从服务器继承审核设置”复选框，指定根据服务器的设置对数据库进行审核。 如果选中此选项，将会看到“查看服务器审核设置”链接，可以使用该链接在此上下文中查看或修改服务器审核设置。

    ![继承设置](./media/sql-database-security-tutorial/auditing-get-started-server-inherit.png)

4. 若要启用不同于在数据库级别指定的审核类型（或位置），请**取消选中**“从服务器继承审核设置”选项，将审核设置为“打开”，然后选择“Blob”审核类型。

    > 如果启用了服务器 Blob 审核，数据库配置的审核将与服务器 Blob 审核并存。

    ![启用审核](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

5. 选择“存储详细信息”打开“审核日志存储”边栏选项卡。 选择日志要保存到的 Azure 存储帐户以及保留期（超过此期限的旧日志将被删除），然后单击底部的“确定”。 **提示：**为所有审核的数据库使用相同的存储帐户，以便充分利用审核报告模板。

    ![导航窗格](./media/sql-database-security-tutorial/auditing-get-started-storage-details.png)

6. 若要自定义审核的事件，可以使用 PowerShell 或 REST API - 有关更多详细信息，请参阅[自动化 (PowerShell/REST API)](#subheading-7) 部分。

7. 单击“保存” 。


## <a name="enable-sql-database-threat-detection"></a>启用 SQL 数据库威胁检测

威胁检测提供新的安全层，在发生异常活动时会提供安全警报，让客户检测潜在威胁并做出响应。 用户可以使用 SQL 数据库审核来探查可疑事件，判断这些可疑事件是否是因为有人尝试访问、破坏或利用数据库中的数据而生成的。 你不必是安全专家，也不需要管理先进的安全监视系统，就能使用威胁检测轻松解决数据库的潜在威胁。
威胁检测会检测异常的数据库活动，指出潜在的 SQL 注入企图。 SQL 注入是 Internet 上常见的 Web 应用程序安全问题之一，用于攻击数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。

1. 导航到要监视的 SQL 数据库的“配置”边栏选项卡。 在“设置”边栏选项卡中，选择“审核和威胁检测”。

    ![导航窗格](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. 在“审核和威胁检测”配置边栏选项卡中，将审核设置为“打开”，随后会显示威胁检测设置。

3. 将威胁检测设置为“打开”。

4. 配置在检测到异常数据库活动时需要接收安全警报的电子邮件列表。

5. 在“审核和威胁检测”边栏选项卡中单击“保存”，以保存新的或更新的审核和威胁检测策略。

    ![导航窗格](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    检测到异常数据库活动时，你将收到电子邮件通知。 电子邮件将提供可疑安全事件的相关信息，包括异常活动的性质、数据库名称、服务器名称和事件时间。 此外，还会提供可能原因和建议操作的相关信息，以帮助你调查和缓解数据库的潜在威胁。 后续步骤将引导你完成收到此类电子邮件后所需执行的操作：

    ![威胁检测电子邮件](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. 在电子邮件中，单击“Azure SQL 审核日志”链接以启动 Azure 门户，并显示发生可疑事件前后的相关审核记录。

    ![审核记录](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. 单击审核记录可以查看有关可疑数据库活动的详细信息，例如 SQL 语句、失败原因和客户端的 IP。

    ![记录详细信息](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. 在“审核记录”边栏选项卡中，单击“在 Excel 中打开”以打开预配置的 Excel 模板，以导入发生可疑事件前后的审核日志，并运行更深入的分析。

    > [!NOTE]
    > 在 Excel 2010 或更高版本中，需要配置 Power Query 和“快速合并”设置。

    ![在 Excel 中打开记录](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. 若要配置“快速合并”设置 - 请在“POWER QUERY”功能区选项卡中，选择“选项”以显示“选项”对话框。 选择“隐私”部分，然后选择第二个选项 -“忽略隐私级别并潜在地改善性能”：

    ![Excel 快速合并](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. 若要加载 SQL 审核日志，请确保设置选项卡中的参数设置正确，选择“数据”功能区，然后单击“全部刷新”按钮。

    ![Excel 参数](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. 结果将显示在“SQL 审核日志”工作表中，使你能够对检测到的异常活动运行更深入的分析，并缓解应用程序中安全事件造成的影响。


## <a name="next-steps"></a>后续步骤
只需几个简单的步骤，即可大大提升数据库抵御恶意用户或未经授权的访问的能力。 在本教程中，你将学习： 

> [!div class="checklist"]
> * 为服务器和/或数据库设置防火墙规则
> * 使用安全连接字符串连接到数据库
> * 管理用户访问权限
> * 通过加密保护数据
> * 启用 SQL 数据库审核
> * 启用 SQL 数据库威胁检测

> [!div class="nextstepaction"]
>[提高 SQL 数据库性能](sql-database-performance-tutorial.md)


