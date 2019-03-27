---
title: 在 Azure SQL 数据库中确保单一数据库或入池数据库的安全 | Microsoft Docs
description: 了解在 Azure SQL 数据库中保护单一数据库或入池数据库的技术和功能。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: d6f14a7cdcb77c1ca47d0f79f587e0bf3606b5d5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893265"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>教程：保护单一数据库或入池数据库

Azure SQL 数据库允许你通过以下方式确保单一数据库或入池数据库中数据的安全：

- 使用防火墙规则限制访问
- 使用需要标识的身份验证机制
- 将授权与基于角色的成员身份和权限配合使用
- 启用安全功能

> [!NOTE]
> 可以根据 [Azure SQL 数据库托管实例](sql-database-managed-instance-index.yml)和[连接体系结构](sql-database-managed-instance-connectivity-architecture.md)中的说明，使用网络安全规则和专用终结点来确保托管实例上的 Azure SQL 数据库的安全。

只需几个简单的步骤，即可增强数据库安全。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> - 创建服务器级和数据库级防火墙规则
> - 配置 Azure Active Directory (AD) 管理员
> - 通过 SQL 身份验证、Azure AD 身份验证和安全的连接字符串管理用户访问
> - 启用安全功能，例如高级数据安全、审核、数据掩码和加密

若要了解详细信息，请参阅 [Azure SQL 数据库安全概述](/azure/sql-database/sql-database-security-index)和[功能](sql-database-security-overview.md)这两篇文章。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保具备以下先决条件：

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Azure SQL 服务器和数据库
  - 通过 [Azure 门户](sql-database-single-database-get-started.md)、[CLI](sql-database-cli-samples.md) 或 [PowerShell](sql-database-powershell-samples.md) 创建它们

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

若要完成本教程中的所有步骤，请登录到 [Azure 门户](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>创建防火墙规则

SQL 数据库受 Azure 中的防火墙保护。 默认情况下，将拒绝与服务器和数据库的所有连接，来自其他 Azure 服务的连接除外。 若要了解详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](sql-database-firewall-configure.md)。

将“允许访问 Azure 服务”设置为“关闭”即可启用最安全的配置。 然后，为需要连接的资源（例如 Azure VM 或云服务）创建一个[保留 IP（经典部署）](../virtual-network/virtual-networks-reserved-public-ip.md)，仅允许该 IP 地址通过防火墙进行访问。 如果使用[资源管理器](/azure/virtual-network/virtual-network-ip-addresses-overview-arm)部署模型，则每个资源都需要一个专用的公共 IP 地址。

> [!NOTE]
> 通过端口 1433 进行的 SQL 数据库通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到 Azure SQL 数据库服务器，除非管理员打开端口 1433。

### <a name="set-up-sql-database-server-firewall-rules"></a>设置 SQL 数据库服务器防火墙规则

服务器级 IP 防火墙规则适用于同一 SQL 数据库服务器中的所有数据库。

若要设置服务器级防火墙规则，请执行以下操作：

1. 在 Azure 门户中，选择左侧菜单中的“SQL 数据库”，然后在“SQL 数据库”页面上选择数据库。

    ![服务器防火墙规则](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > 确保复制完全限定的服务器名称（例如 *yourserver.database.windows.net*），以便稍后在本教程中使用。

1. 在“概览”页上，选择“设置服务器防火墙”。 此时会打开数据库服务器的“防火墙设置”页。

   1. 在工具栏上选择“添加客户端 IP”，将当前的 IP 地址添加到新的防火墙规则。 此规则可以针对单个 IP 地址或一系列 IP 地址打开端口 1433。 选择“保存”。

      ![设置服务器防火墙规则](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. 选择“确定”，关闭“防火墙设置”页。

现可使用指定的 IP 地址或 IP 地址范围连接到服务器中的任何数据库。

> [!IMPORTANT]
> 默认情况下，已在“允许访问 Azure 服务”下为所有 Azure 服务启用通过 SQL 数据库防火墙进行访问的功能。 选择“关闭”即可禁止访问所有 Azure 服务。

### <a name="setup-database-firewall-rules"></a>设置数据库防火墙规则

数据库级防火墙规则仅适用于单个数据库。 数据库在服务器故障转移期间会保留这些规则。 数据库级防火墙规则只能使用 Transact-SQL (T-SQL) 语句进行配置，而且只能在配置了服务器级防火墙规则后进行配置。

若要设置数据库级防火墙规则，请执行以下操作：

1. 例如，使用 [SQL Server Management Studio](./sql-database-connect-query-ssms.md) 连接到数据库。

1. 在“对象资源管理器”中，右键单击数据库，并选择“新建查询”。

1. 在查询窗口中添加此语句，将 IP 地址修改为公共 IP 地址：

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. 在工具栏上选择“执行”，创建防火墙规则。

> [!NOTE]
> 也可使用 [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) 命令在 SSMS 中创建服务器级防火墙规则，不过必须连接到 *master* 数据库。

## <a name="create-an-azure-ad-admin"></a>创建 Azure AD 管理员

确保使用相应的 Azure Active Directory (AD) 托管域。 若要选择 AD 域，请在 Azure 门户的右上角操作。 此过程确认是否对 Azure AD 和托管 Azure SQL 数据库或数据仓库的 SQL Server 使用了同一订阅。

   ![选择-AD](./media/sql-database-security-tutorial/8choose-ad.png)

若要设置 Azure AD 管理员，请执行以下操作：

1. 在 Azure 门户的“SQL Server”页上，选择“Active Directory 管理员”。接下来选择“设置管理员”。

    ![选择 active directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > 需要是“公司管理员”或“全局管理员”才能执行此任务。

1. 在“添加管理员”页上搜索并选择 AD 用户或组，然后选择“选择”。 将会列出 Active Directory 的所有成员和组，灰显的条目不能用作 Azure AD 管理员。 请参阅 [Azure AD 功能和限制](sql-database-aad-authentication.md#azure-ad-features-and-limitations)。

    ![选择管理员](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > 基于角色的访问控制 (RBAC) 仅适用于门户，不会传播到 SQL Server。

1. 在“Active Directory 管理员”页顶部，选择“保存”。

    更改管理员的过程可能需要几分钟时间。 新管理员会出现在“Active Directory 管理员”框中。

> [!NOTE]
> 设置 Azure AD 管理员时，此新的管理员名称（用户或组）不能作为 SQL Server 身份验证用户存在于 *master* 数据库中。 如果存在，则设置会失败，并会回退所做的更改，指示该管理员名称已存在。 由于此 SQL Server 身份验证用户不是 Azure AD 的一部分，因此使用 Azure AD 身份验证连接用户的任何尝试都会失败。

若要了解如何配置 Azure AD，请参阅：

- [将本地标识与 Azure AD 集成](../active-directory/hybrid/whatis-hybrid-identity.md)
- [将自己的域名添加到 Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure now supports federation with Windows Server AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)（Microsoft Azure 现在支持与 Windows Server AD 联合）
- [管理 Azure AD 目录](../active-directory/fundamentals/active-directory-administer.md)
- [使用 PowerShell 管理 Azure AD](/powershell/azure/overview?view=azureadps-2.0)
- [混合标识所需的端口和协议](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>管理数据库访问

管理数据库访问的方法是：将用户添加到数据库，或者允许用户使用安全的连接字符串进行访问。 连接字符串用于外部应用程序。 若要了解详细信息，请参阅 [Azure SQL 访问控制](sql-database-control-access.md)和 [AD 身份验证](sql-database-aad-authentication.md)。

若要添加用户，请选择数据库身份验证类型：

- **SQL 身份验证**，使用用户名和密码登录，仅适用于服务器中存在特定数据库的情况

- **Azure AD 身份验证**，使用 Azure AD 托管的标识

### <a name="sql-authentication"></a>SQL 身份验证

若要通过 SQL 身份验证来添加用户，请执行以下操作：

1. 例如，使用 [SQL Server Management Studio](./sql-database-connect-query-ssms.md) 连接到数据库。

1. 在“对象资源管理器”中，右键单击数据库，然后选择“新建查询”。

1. 在查询窗口中输入以下命令：

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. 在工具栏上选择“执行”，以便创建用户。

1. 默认情况下，用户可连接到数据库，但没有读取或写入数据的权限。 若要授予这些权限，请在新的查询窗口中执行以下命令：

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> 在数据库级别创建非管理员帐户，除非这些帐户需要执行创建新用户之类的管理员任务。

### <a name="azure-ad-authentication"></a>Azure AD 身份验证

Azure Active Directory 身份验证要求将数据库用户作为包含的用户来创建。 包含的数据库用户映射到与数据库关联的 Azure AD 目录中的标识，且在 *master* 数据库中没有登录名。 Azure AD 标识可以用于单个用户，也可以用于一个组。 有关详细信息，请参阅[包含的数据库用户 - 使你的数据库可移植](https://msdn.microsoft.com/library/ff929188.aspx)，并查看 [Azure AD 教程](./sql-database-aad-authentication-configure.md)，了解如何使用 Azure AD 进行身份验证。

> [!NOTE]
> 不能使用 Azure 门户创建数据库用户（管理员除外）。 Azure RBAC 角色不会传播到 SQL Server、数据库或数据仓库。 它们只能用于管理 Azure 资源，不会应用于数据库权限。
>
> 例如，“SQL Server 参与者”角色不会授予连接到数据库或数据仓库的访问权限。 必须使用 T-SQL 语句在数据库中授予此权限。

> [!IMPORTANT]
> 不支持在 T-SQL 的 `CREATE LOGIN` 和 `CREATE USER` 语句中将特殊字符（例如冒号 `:` 或与号 `&`）用作用户名的一部分。

若要通过 Azure AD 身份验证来添加用户，请执行以下操作：

1. 使用至少带 *ALTER ANY USER* 权限的 Azure AD 帐户连接到 Azure SQL Server。

1. 在“对象资源管理器”中，右键单击数据库，并选择“新建查询”。

1. 在查询窗口中输入以下命令，并将 `<Azure_AD_principal_name>` 修改为 Azure AD 用户的主体名称，或者修改为 Azure AD 组的显示名称：

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Azure AD 用户在数据库元数据中标记为类型 `E (EXTERNAL_USER)`，而组则标记为类型 `X (EXTERNAL_GROUPS)`。 有关详细信息，请参阅 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)。

### <a name="secure-connection-strings"></a>安全的连接字符串

若要确保客户端应用程序与 SQL 数据库的连接安全且已加密，必须将连接字符串配置为：

- 请求加密的连接
- 不信任服务器证书

使用传输层安全性 (TLS) 建立连接，减少中间人攻击的风险。 连接字符串按数据库提供，并已预先配置为支持客户端驱动程序，例如 ADO.NET、JDBC、ODBC、PHP。 有关 TLS 和连接的信息，请参阅 [TLS 注意事项](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)。

若要复制安全的连接字符串，请执行以下操作：

1. 在 Azure 门户中，选择左侧菜单中的“SQL 数据库”，然后在“SQL 数据库”页面上选择数据库。

1. 在“概览”页上，选择“显示数据库连接字符串”。

1. 选择一个驱动程序选项卡，复制完整的连接字符串。

    ![ADO.NET 连接字符串](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>启用安全功能

Azure SQL 数据库提供可以通过 Azure 门户访问的安全功能。 这些功能适用于数据库和服务器，数据掩码功能除外，该功能只能在数据库上使用。 若要了解详细信息，请参阅[高级数据安全](sql-database-advanced-data-security.md)、[审核](sql-database-auditing.md)、[动态数据掩码](sql-database-dynamic-data-masking-get-started.md)和[透明数据加密](transparent-data-encryption-azure-sql.md)。

### <a name="advanced-data-security"></a>高级数据安全

高级数据安全功能可检测出现的潜在威胁，并提供有关异常活动的安全警报。 用户可使用审核功能探查这些可疑事件，确定事件背后的目的是否是要访问、破坏或利用数据库中的数据。 系统也会为用户提供安全概览，其中包括漏洞评估以及数据发现和分类工具。

> [!NOTE]
> 例如，SQL 注入就是一种威胁，攻击者会在应用程序输入中注入恶意 SQL。 应用程序随后就会在不知情的情况下执行恶意 SQL，允许攻击者访问，导致数据库中的数据被破坏或修改。

若要启用高级数据安全，请执行以下操作：

1. 在 Azure 门户中，选择左侧菜单中的“SQL 数据库”，然后在“SQL 数据库”页面上选择数据库。

1. 在“概览”页上，选择“服务器名称”链接。 此时会打开数据库服务器页。

1. 在“SQL Server”页上找到“安全性”部分，然后选择“高级数据安全”。

   1. 在“高级数据安全”下选择“打开”，以便启用此功能。 选择用于保存漏洞评估结果的存储帐户。 再选择“保存”。

      ![导航窗格](./media/sql-database-security-tutorial/threat-settings.png)

      也可配置电子邮件，以便接收安全警报、存储详细信息和威胁检测类型。

1. 返回到数据库的“SQL 数据库”页，在“安全性”部分选择“高级数据安全”。 在此处可以找到适用于数据库的各种安全指示器。

    ![威胁状态](./media/sql-database-security-tutorial/threat-status.png)

如果检测到异常活动，系统会向你发送有关该事件的电子邮件。 其中包含活动性质、数据库、服务器、事件时间、可能原因以及建议的操作，方便你调查并缓解潜在威胁。 如果收到此类电子邮件，请选择“Azure SQL 审核日志”链接，以便启动 Azure 门户并查看发生事件时的相关审核记录。

   ![威胁检测电子邮件](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>审核

此审核功能跟踪数据库事件并将事件写入 Azure 存储、Azure Monitor 日志或写入事件中心。 审核可确保一切符合法规、了解数据库活动，以及深入了解那些可能表明存在潜在安全违规的偏差和异常。

若要启用审核，请执行以下操作：

1. 在 Azure 门户中，选择左侧菜单中的“SQL 数据库”，然后在“SQL 数据库”页面上选择数据库。

1. 在“安全性”部分，选择“审核”。

1. 在“审核”设置下，设置以下值：

   1. 将“审核”设置为“打开”。

   1. 选择下述任意项作为“审核日志目标”：

       - **存储**，一个 Azure 存储帐户，可以在其中保存事件日志，并可将其作为 *.xel* 文件下载

          > [!TIP]
          > 请对所有已审核的数据库使用同一存储帐户，以充分利用审核报告模板。

       - **Log Analytics**，可以自动存储事件，这些事件用于查询或者在将来用于分析

           > [!NOTE]
           > 需要使用 **Log Analytics 工作区**来支持高级功能，例如分析、自定义警报规则，以及 Excel 或 Power BI 导出。 在没有工作区的情况下，只能使用查询编辑器。

       - **事件中心**，可以路由需要在其他应用程序中使用的事件

   1. 选择“保存”。

      ![审核设置](./media/sql-database-security-tutorial/audit-settings.png)

1. 现在可以选择“查看审核日志”，以便查看数据库事件数据。

    ![审核记录](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> 请参阅 [SQL 数据库审核](sql-database-auditing.md)，了解如何使用 PowerShell 或 REST API 来进一步自定义审核事件。

### <a name="dynamic-data-masking"></a>动态数据掩码

数据掩码功能会自动隐藏数据库中的敏感数据。

若要启用数据掩码功能，请执行以下操作：

1. 在 Azure 门户中，选择左侧菜单中的“SQL 数据库”，然后在“SQL 数据库”页面上选择数据库。

1. 在“安全性”部分选择“动态数据掩码”。

1. 在“动态数据掩码”设置下选择“添加掩码”，以便添加掩码规则。 Azure 会自动填充可供选择的数据库架构、表和列。

    ![掩码设置](./media/sql-database-security-tutorial/mask-settings.png)

1. 选择“保存”。 现在会对所选信息进行掩码处理以维护隐私。

    ![掩码示例](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>透明数据加密

加密功能会自动加密静态数据，无需对访问加密数据库的应用程序做任何更改。 新数据库会默认启用加密。 也可使用 SSMS 和 [Always Encrypted](sql-database-always-encrypted.md) 功能来加密数据。

若要启用或验证加密，请执行以下操作：

1. 在 Azure 门户中，选择左侧菜单中的“SQL 数据库”，然后在“SQL 数据库”页面上选择数据库。

1. 在“安全性”部分，选择“透明数据加密”。

1. 必要时，将“数据加密”设置为“打开”。 选择“保存”。

    ![透明数据加密](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> 若要查看加密状态，请使用 [SSMS](./sql-database-connect-query-ssms.md) 连接到数据库，然后查询 [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) 视图的 `encryption_state` 列。 状态为 `3` 指示数据库已加密。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何通过简单的几个步骤增强数据库的安全性。 你已了解如何：

> [!div class="checklist"]
> - 创建服务器级和数据库级防火墙规则
> - 配置 Azure Active Directory (AD) 管理员
> - 通过 SQL 身份验证、Azure AD 身份验证和安全的连接字符串管理用户访问
> - 启用安全功能，例如高级数据安全、审核、数据掩码和加密

请转到下一教程，了解如何进行地理分布。

> [!div class="nextstepaction"]
>[实现地理分散的数据库](sql-database-implement-geo-distributed-database.md)
