---
title: 使用托管标识访问 Azure SQL 数据库 - Azure 流分析
description: 本文介绍如何使用托管标识对 Azure 流分析作业的 Azure SQL DB 输出进行身份验证。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595836"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>使用托管标识访问 Azure 流分析作业的 Azure SQL 数据库（预览）

Azure 流分析支持对 Azure SQL 数据库输出接收器进行[托管标识身份验证](../active-directory/managed-identities-azure-resources/overview.md)。 托管标识消除了基于用户的身份验证方法的如下限制：发生密码更改或用户令牌过期（每隔 90 天过期）时需要重新进行身份验证。 当你不再需要手动进行身份验证时，流分析部署可以完全自动化。

托管标识是 Azure Active Directory 中注册的表示给定流分析作业的托管应用程序。 托管应用程序用于对目标资源进行身份验证。 本文介绍如何通过 Azure 门户为流分析作业的 Azure SQL 数据库输出启用托管标识。

## <a name="prerequisites"></a>先决条件

使用此功能需满足以下条件：

- 有 Azure 流分析作业。

- 有 Azure SQL 数据库资源。

## <a name="create-a-managed-identity"></a>创建托管标识

首先，创建 Azure 流分析作业的托管标识。

1. 在 [Azure 门户](https://portal.azure.com)中，打开 Azure 流分析作业。

1. 从左侧导航菜单中，选择“配置”下的“托管标识”。 然后，选中“使用系统分配的托管标识”旁的框，然后选择“保存”。

   ![选择系统分配的托管标识](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   在 Azure Active Directory 中，为流分析作业标识创建服务主体。 新建标识的生命周期将由 Azure 管理。 删除流分析作业时，Azure 会自动删除关联的标识（即服务主体）。 

1. 保存配置后，服务主体的对象 ID (OID) 将列为主体 ID，如下所示： 

   ![显示为主体 ID 的对象 ID](./media/sql-db-output-managed-identity/principal-id.png)

   服务主体与流分析作业同名。 例如，如果作业的名称是 MyASAJob，则服务主体的名称也是 MyASAJob。

## <a name="select-an-active-directory-admin"></a>选择 Active Directory 管理员

创建托管标识后，选择 Active Directory 管理员。

1. 导航到 Azure SQL 数据库资源，并选择数据库所处的 SQL Server。 可以在资源概述页上的“服务器名称”旁找到 SQL Server 名称。 

1. 在“设置”下，选择“Active Directory 管理员”。 然后选择“设置管理员”。 

   ![Active Directory 管理员页](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. 在“Active Directory 管理员”页中，搜索将成为 SQL Server 管理员的某个用户或组，并单击“选择”。  

   ![添加 Active Directory 管理员](./media/sql-db-output-managed-identity/add-admin.png)

1. 在“Active Directory 管理员”页中，选择“保存”。 更改管理员的过程只需要几分钟。  

## <a name="create-a-database-user"></a>创建数据库用户

接下来，在 SQL 数据库中创建包含的数据库用户，该用户将映射到 Azure Active Directory 标识。 包含的数据库用户在 master 数据库中没有登录名，但它映射到与数据库关联的目录中的标识。 Azure Active Directory 标识可以是单独的用户帐户，也可以是组。 在这种情况下，你需要为流分析作业创建包含的数据库用户。 

1. 使用 SQL Server Management Studio 连接 SQL 数据库。 “用户名”是具有 ALTER ANY USER 权限的 Azure Active Directory 用户。 在 SQL Server 上设置的管理员是一个示例。 使用“Azure Active Directory - 通用且具有 MFA”身份验证。 

   ![连接到 SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   服务器名称 `<SQL Server name>.database.windows.net` 在不同区域可能不同。 例如，中国地区应使用 `<SQL Server name>.database.chinacloudapi.cn`。
 
   可以通过转到“选项”>“连接属性”>“连接到数据库”来指定特定的 SQL 数据库。  

   ![SQL Server 连接属性](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. 首次连接时，可能会遇到以下窗口：

   ![新建防火墙规则窗口](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. 如果是这样，请在 Azure 门户中转到 SQL Server 资源。 在“安全”部分下，打开“防火墙和虚拟网络”页。 
   1. 使用任意规则名称添加新规则。
   1. 使用“新建防火墙规则”窗口中的“发件人”IP 地址作为“开始 IP”。
   1. 使用“新建防火墙规则”窗口中的“收件人”IP 地址作为“结束 IP”。 
   1. 选择“保存”，并尝试再次从 SQL Server Management Studio 进行连接。 

1. 连接后，创建包含的数据库用户。 下面的 SQL 命令将创建一个包含的数据库用户，其名称与你的流分析作业相同。 请确保在 ASA_JOB_NAME 两侧加上括号。 使用以下 T-SQL 语法并运行查询。 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

## <a name="grant-stream-analytics-job-permissions"></a>授予流分析作业权限

流分析作业具有来自托管标识的权限，可以对 SQL 数据库资源执行 CONNECT 命令。 最有可能的情况是，允许流分析作业运行 SELECT 等命令。 可以使用 SQL Server Management Studio 将这些权限授予流分析作业。 有关详细信息，请参阅 [GRANT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15) 参考。

或者，你可以在 SQL Server Management Studio 中右键单击 SQL 数据库，然后选择“属性”>“权限”。 从“权限”菜单中，你可以看到之前添加的流分析作业，可以根据需要手动授予或拒绝权限。

## <a name="create-an-azure-sql-database-output"></a>创建 Azure SQL 数据库输出

配置托管标识后，便可以将 Azure SQL 数据库作为输出添加到流分析作业。

1. 返回到你的流分析作业，然后在“作业拓扑”下导航到“输出”页。 

1. 选择“添加”>“SQL 数据库”。 在 SQL 数据库输出接收器的输出属性窗口中，从“身份验证模式”下拉列表选择“托管标识”。

1. 填写其余的属性。 若要详细了解如何创建 SQL 数据库输出，请参阅[使用流分析创建 SQL 数据库输出](stream-analytics-define-outputs.md#sql-database)。 完成后，选择“保存”。 

## <a name="next-steps"></a>后续步骤

* [了解 Azure 流分析的输出](stream-analytics-define-outputs.md)
* [从 Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)
