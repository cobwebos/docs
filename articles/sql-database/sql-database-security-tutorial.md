---
title: 在 Azure SQL 数据库中确保单一数据库的安全 | Microsoft Docs
description: 了解在 Azure SQL 数据库中保护单一数据库的技术和功能。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: 814d558efee4a72a25d956828e0db237424cab24
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409762"
---
# <a name="tutorial-secure-a-single-database-in-azure-sql-database"></a>教程：在 Azure SQL 数据库中确保单一数据库的安全

SQL 数据库在单一 Azure SQL 数据库中保护数据的方式是：

- 使用防火墙规则限制对数据库的访问
- 使用需要标识的身份验证机制
- 通过基于角色的成员身份和权限实现对数据的授权
- 行级别安全性
- 动态数据掩码

SQL 数据库还有复杂的监视、审核和威胁检测功能。

> [!IMPORTANT]
> Azure SQL 数据库在托管实例中保护数据库安全的方式是使用网络安全规则和专用终结点。 有关详细信息，请参阅[Azure SQL 数据库托管实例](sql-database-managed-instance-index.yml)和 [Azure SQL 数据库托管实例连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。

只需几个简单的步骤，即可大大提升数据库抵御恶意用户或未经授权的访问的能力。 在本教程中，学习：

> [!div class="checklist"]
> - 在 Azure 门户中创建服务器的服务器级防火墙规则
> - 使用 SSMS 创建数据库的数据库级防火墙规则
> - 使用安全连接字符串连接到数据库
> - 为 Azure SQL 配置 Azure Active Directory 管理员
> - 管理用户访问权限
> - 通过加密保护数据
> - 启用 SQL 数据库审核
> - 启用 SQL 数据库威胁检测

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保符合以下条件：

- 已安装最新版 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。
- 已安装 Microsoft Excel
- 已创建 Azure SQL 服务器和数据库。请参阅[在 Azure 门户中创建 Azure SQL 数据库](sql-database-get-started-portal.md)、[使用 Azure CLI 创建单一 Azure SQL 数据库](sql-database-cli-samples.md)和[使用 PowerShell 创建单一 Azure SQL 数据库](sql-database-powershell-samples.md)。

> [!NOTE]
> 本教程假定你已配置 Azure Active Directory，或使用初始 Azure Active Directory 托管域。 若要了解如何为各种方案配置 Azure Active Directory，请参阅[将本地标识与 Azure Active Directory 集成](../active-directory/hybrid/whatis-hybrid-identity.md)、[将自己的域名添加到 Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)、[Microsoft Azure 现在支持与 Windows Server Active Directory 联合](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理 Azure AD 目录](../active-directory/fundamentals/active-directory-administer.md)、[使用 Windows PowerShell 管理 Azure AD](/powershell/azure/overview?view=azureadps-2.0) 和[混合标识所需端口和协议](../active-directory/hybrid/reference-connect-ports.md)。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 门户中创建服务器级防火墙规则

SQL 数据库受 Azure 中的防火墙保护。 默认情况下，将拒绝与服务器和服务器内数据库的所有连接，连接自其他 Azure 服务的连接除外。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](sql-database-firewall-configure.md)。

最安全的配置是将“允许访问 Azure 服务”设置为“关闭”。 如果需要从 Azure VM 或云服务连接到数据库，则应创建[保留 IP](../virtual-network/virtual-networks-reserved-public-ip.md)（经典部署），且仅允许保留的 IP 地址通过防火墙进行访问。 如果使用[资源管理器](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)部署模型，则会为资源分配专用公共 IP 地址。应该允许此 IP 地址通过防火墙。

按照以下步骤为服务器创建 [SQL 数据库服务器级防火墙规则](sql-database-firewall-configure.md)，以允许从特定 IP 地址进行连接。

> [!NOTE]
> 如果已根据前面的教程或快速入门之一在 Azure 中创建了示例数据库，并要使用这些教程使用的同一 IP 地址在计算机上执行本教程，可以跳过这一步，因为已创建服务器级防火墙规则。

1. 从左侧菜单中单击“SQL 数据库”，并在“SQL 数据库”页单击要为其配置防火墙规则的数据库。 此时会打开数据库的概述页，其中显示了完全限定的服务器名称（例如 **mynewserver-20170313.database.windows.net**），并提供了其他配置的选项。

      ![服务器防火墙规则](./media/sql-database-security-tutorial/server-firewall-rule.png)

2. 如上图所示，在工具栏上单击“设置服务器防火墙”。 此时会打开 SQL 数据库服务器的“防火墙设置”页。

3. 在工具栏单击“添加客户端 IP”，添加连接到门户的计算机的公共 IP 地址，或者手动输入防火墙规则，并单击“保存”。

      ![设置服务器防火墙规则](./media/sql-database-security-tutorial/server-firewall-rule-set.png)

4. 单击“确定”，并单击“X”关闭“防火墙设置”页。

现可使用指定的 IP 地址或 IP 地址范围连接到服务器中的任何数据库。

> [!NOTE]
> 通过端口 1433 进行的 SQL 数据库通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到 Azure SQL 数据库服务器，除非 IT 部门打开了端口 1433。
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>使用 SSMS 创建数据库级防火墙规则

使用数据库级防火墙规则，可以为同一逻辑服务器内的各个数据库创建不同的防火墙设置，并创建可移植的防火墙规则。也就是说，这些规则在故障转移期间与数据库一起转移，而不是存储在 SQL 服务器上。 数据库级防火墙规则只能通过使用 Transact-SQL 语句进行配置，而且只能在配置了第一个服务器级防火墙规则后才能配置。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](sql-database-firewall-configure.md)。

按照以下步骤操作，创建特定于数据库的防火墙规则。

1. 例如，使用 [SQL Server Management Studio](./sql-database-connect-query-ssms.md) 连接到数据库。

2. 在对象资源管理器中，右键单击要为其添加防火墙规则的数据库，并单击“新建查询”。 此时会打开一个空白查询窗口，该窗口连接到数据库。

3. 在查询窗口中，将 IP 地址修改为公共 IP 地址，再执行以下查询：

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. 在工具栏上，单击“执行”创建防火墙规则。

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>了解如何使用安全连接字符串将应用程序与数据库相连

若要确保客户端应用程序与 SQL 数据库的连接安全且已加密，必须将连接字符串配置为：

- 请求实现加密连接；以及
- 不信任服务器证书。

这会使用传输层安全性 (TLS) 建立连接并减少中间人攻击的风险。 可从 Azure 门户为支持的客户端驱动程序的 SQL 数据库获得正确配置的连接字符串，如 ADO.net 的以下屏幕截图所示。 有关 TLS 和连接的信息，请参阅 [TLS 注意事项](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)。

1. 从左侧菜单中选择“SQL 数据库”，并单击“SQL 数据库”页上的数据库。

2. 在数据库“概述”边栏选项卡上，单击“显示数据库连接字符串”。

3. 查看完整的 ADO.NET 连接字符串。

    ![ADO.NET 连接字符串](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>为 Azure SQL 数据库服务器预配 Azure Active Directory 管理员

在 Azure 门户中为 Azure SQL Server 预配 Azure Active Directory 管理员。

1. 在 [Azure 门户](https://portal.azure.com/)右上角，选择你的连接，以下拉可能的 Active Directory 列表。 选择正确的 Active Directory 作为默认的 Azure AD。 此步骤将与订阅关联的 Active Directory 链接到 Azure SQL Server，确保为 Azure AD 和 SQL Server 使用相同的订阅。 （Azure SQL Server 托管的可能是 Azure SQL 数据库或 Azure SQL 数据仓库。）

    ![选择-AD](./media/sql-database-aad-authentication/8choose-ad.png)

2. 在“SQL Server”页上选择“Active Directory 管理员”，在“Active Directory 管理员”页上选择“设置管理员”。![选择 active directory](./media/sql-database-aad-authentication/select-active-directory.png)  

3. 在“添加管理员”页中，搜索某位用户，选择该用户或组作为管理员，然后选择“选择”。 （“Active Directory 管理员”页会显示 Active Directory 的所有成员和组。 若用户或组为灰显，则无法选择，因为不支持它们作为 Azure AD 管理员。 （请参阅[将 Azure Active Directory 身份验证与使用 SQL 数据库或 SQL 数据仓库进行身份验证结合使用](sql-database-aad-authentication.md)的“Azure AD 功能和限制”部分中支持的管理员列表。）基于角色的访问控制 (RBAC) 仅适用于该门户，不会传播到 SQL Server。
    ![选择管理员](./media/sql-database-aad-authentication/select-admin.png)  

4. 在“Active Directory 管理员”页顶部，选择“保存”。
    ![保存管理员](./media/sql-database-aad-authentication/save-admin.png)

更改管理员的过程可能需要几分钟时间。 然后，新管理员将出现在“Active Directory 管理员”框中。

   > [!NOTE]
   > 设置 Azure AD 管理员时，此新的管理员名称（用户或组）不能已作为 SQL Server 身份验证用户存在于虚拟 master 数据库中。 如果存在，Azure AD 管理员设置会失败；将回滚其创建，并指示此管理员（名称）已存在。 由于这种 SQL Server 身份验证用户不是 Azure AD 的一部分，因此使用 Azure AD 身份验证连接到服务器的任何尝试都会失败。

## <a name="creating-database-users"></a>创建数据库用户

创建任何用户前，必须首先从 Azure SQL 数据库支持的两种身份验证类型中选择一种类型：

SQL 身份验证，为登录名和仅在逻辑服务器内特定数据库的上下文中有效的用户使用用户名和密码。

Azure Active Directory 身份验证，使用 Azure Active Directory 管理的标识。

### <a name="create-a-user-using-sql-authentication"></a>使用 SQL 身份验证创建用户

请按照以下步骤操作，使用 SQL 身份验证创建用户：

1. 连接到数据库，例如使用 [SQL Server Management Studio](./sql-database-connect-query-ssms.md)，使用服务器管理员凭据。

2. 在对象资源管理器中，右键单击要为其添加新用户的数据库，并单击“新建查询”。 此时会打开一个空白查询窗口，该窗口连接到所选数据库。

3. 在查询窗口中输入以下查询：

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. 在工具栏上，单击“执行”创建用户。

5. 默认情况下，用户可连接到数据库，但没有读取或写入数据的权限。 若要向新建用户授予这些权限，可在新的查询窗口中执行以下两个命令

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

最好在数据库级别创建这些非管理员帐户以连接到数据库，除非需要执行创建新用户等管理员任务。 请查看 [Azure Active Directory 教程](./sql-database-aad-authentication-configure.md)，了解如何使用 Azure Active Directory 进行身份验证。

### <a name="create-a-user-using-azure-active-directory-authentication"></a>使用 Azure Active Directory 身份验证创建用户

Azure Active Directory 身份验证要求以包含的数据库用户的身份创建数据库用户。 基于 Azure AD 标识的包含的数据库用户是在 master 数据库中不具有登录名的数据库用户，它映射到与数据库相关联的 Azure AD 目录中的标识。 Azure AD 标识可以是单独的用户帐户，也可以是组。 有关包含的数据库用户的详细信息，请参阅[包含的数据库用户 - 使数据库可移植](https://msdn.microsoft.com/library/ff929188.aspx)。

> [!NOTE]
> 不能使用 Azure 门户创建数据库用户（管理员除外）。 RBAC 角色不会传播到 SQL Server、SQL 数据库或 SQL 数据仓库。 Azure RBAC 角色用于管理 Azure 资源，不会应用于数据库权限。 例如，**SQL Server 参与者**角色不会授予连接到 SQL 数据库或 SQL 数据仓库的访问权限。 必须使用 Transact-SQL 语句直接在数据库中授予访问权限。
> [!WARNING]
> 不支持在 T-SQL CREATE LOGIN 和 CREATE USER 语句中将特殊字符（例如冒号 `:` 或与号 `&`）用作用户名。

1. 至少使用 **ALTER ANY USER** 权限通过 Azure Active Directory 帐户连接到 Azure SQL Server。
2. 在对象资源管理器中，右键单击要为其添加新用户的数据库，并单击“新建查询”。 此时会打开一个空白查询窗口，该窗口连接到所选数据库。

3. 在查询窗口中输入以下查询，并将 `<Azure_AD_principal_name>` 修改为 Azure AD 用户的所需用户主体名称，或者修改为 Azure AD 组的显示名称：

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

   > [!NOTE]
   > Azure AD 用户在数据库元数据中均标记为类型 E (EXTERNAL_USER)，而组则标记为类型 X (EXTERNAL_GROUPS)。 有关详细信息，请参阅 [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)。

## <a name="protect-your-data-with-encryption"></a>通过加密保护数据

Azure SQL 数据库透明数据加密 (TDE) 会自动加密静态数据，无需对访问加密数据库的应用程序做任何更改。 对于新创的数据库，TDE 默认处于开启状态。 若要为数据库启用 TDE 或验证 TDE 是否开启，请按照以下步骤操作：

1. 从左侧菜单中选择“SQL 数据库”，并单击“SQL 数据库”页上的数据库。

2. 单击“透明数据加密”，打开 TDE 的配置页。

    ![透明数据加密](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. 必要时，将“数据加密”设置为“开”，再单击“保存”。

加密进程在后台启动。 若要监视进度，可以使用 [SQL Server Management Studio](./sql-database-connect-query-ssms.md) 连接到 SQL 数据库并查询 [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017) 视图的 encryption_state 列。 状态为 3 指示数据库已加密。

## <a name="enable-sql-database-auditing-if-necessary"></a>启用 SQL 数据库审核（必要时）

Azure SQL 数据库审核跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。 审核可帮助保持遵从法规、了解数据库活动，以及深入了解可以指明潜在安全违规的偏差和异常。 请按照以下步骤操作，为 SQL 数据库创建默认审核策略：

1. 从左侧菜单中选择“SQL 数据库”，并单击“SQL 数据库”页上的数据库。

2. 在“设置”边栏选项卡中，选择“审核和威胁检测”。 请注意，服务器级审核已禁用，可以单击“查看服务器设置”链接，在此上下文中查看或修改服务器审核设置。

    ![“审核”边栏选项卡](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. 若要启用不同于在数据库一级指定的审核类型（或位置），请将“审核”设置为“开”，再选择“Blob”作为“审核类型”。 如果启用了服务器 Blob 审核，数据库配置的审核将与服务器 Blob 审核并存。

    ![启用审核](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. 选择“存储详细信息”打开“审核日志存储”边栏选项卡。 选择日志要保存到的 Azure 存储帐户以及保留期（超过此期限的旧日志会被删除），然后单击底部的“确定”。

   > [!TIP]
   > 请对所有已审核的数据库使用同一存储帐户，以充分利用审核报告模板。

5. 单击“ **保存**”。

> [!IMPORTANT]
> 如果希望自定义审核事件，可通过 PowerShell 或 REST API 实现 - 请参阅 [SQL 数据库审核](sql-database-auditing.md)，了解详细信息。
>

## <a name="enable-sql-database-threat-detection"></a>启用 SQL 数据库威胁检测

威胁检测提供新的安全层，在发生异常活动时会提供安全警报，让客户检测潜在威胁并做出响应。 用户可以使用 SQL 数据库审核来探查可疑事件，判断这些可疑事件是否是因为有人尝试访问、破坏或利用数据库中的数据而生成的。 不必是安全专家，也不需要管理先进的安全监视系统，就能使用威胁检测轻松解决数据库的潜在威胁。
威胁检测会检测异常的数据库活动，指出潜在的 SQL 注入企图。 SQL 注入是 Internet 上常见的 Web 应用程序安全问题之一，用于攻击数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。

1. 导航到要监视的 SQL 数据库的“配置”边栏选项卡。 在“设置”边栏选项卡中，选择“审核和威胁检测”。

    ![导航窗格](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. 在“审核和威胁检测”配置边栏选项卡中，将审核设置为“打开”，随后会显示威胁检测设置。

3. 将威胁检测设置为“打开”。

4. 配置在检测到异常数据库活动时需要接收安全警报的电子邮件列表。

5. 在“审核和威胁检测”边栏选项卡中单击“保存”，以保存新的或更新的审核和威胁检测策略。

    ![导航窗格](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    如果检测到异常数据库活动，便会立即发送电子邮件通知。 电子邮件将提供可疑安全事件的相关信息，包括异常活动的性质、数据库名称、服务器名称和事件时间。 此外，还会提供可能原因和建议操作的相关信息，帮助调查和缓解数据库的潜在威胁。 后续步骤将引导完成收到此类电子邮件后所需执行的操作：

    ![威胁检测电子邮件](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. 在电子邮件中，单击“Azure SQL 审核日志”链接以启动 Azure 门户，并显示发生可疑事件前后的相关审核记录。

    ![审核记录](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. 单击审核记录可以查看有关可疑数据库活动的详细信息，例如 SQL 语句、失败原因和客户端的 IP。

    ![记录详细信息](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. 在“审核记录”边栏选项卡中，单击“在 Excel 中打开”以打开预配置的 Excel 模板，以导入发生可疑事件前后的审核日志，并运行更深入的分析。

    > [!NOTE]
    > 在 Excel 2010 或更高版本中，需要配置 Power Query 和“快速合并”设置。

    ![在 Excel 中打开记录](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. 若要配置“快速合并”设置 - 请在“POWER QUERY”功能区选项卡中，选择“选项”以显示“选项”对话框。 选择“隐私”部分，并选择第二个选项 -“忽略隐私级别并潜在地改善性能”：

    ![Excel 快速合并](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. 要加载 SQL 审核日志，请确保设置选项卡中的参数设置正确，选择“数据”功能区，并单击“全部刷新”按钮。

    ![Excel 参数](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. 结果会显示在“SQL 审核日志”工作表中，使你能够对检测到的异常活动运行更深入的分析，并缓解应用程序中安全事件造成的影响。

## <a name="next-steps"></a>后续步骤

学习本教程后，只需几个简单的步骤，即可大大提升数据库抵御恶意用户或未经授权的访问的能力。  你已了解如何：

> [!div class="checklist"]
> - 为服务器和/或数据库设置防火墙规则
> - 使用安全连接字符串连接到数据库
> - 为 Azure SQL 配置 Azure Active Directory 管理员
> - 管理用户访问权限
> - 通过加密保护数据
> - 启用 SQL 数据库审核
> - 启用 SQL 数据库威胁检测

请转到下一教程，了解如何实施分布式数据库。

> [!div class="nextstepaction"]
>[实现地理分散的数据库](sql-database-implement-geo-distributed-database.md)
