---
title: 保护 Azure SQL Edge
description: 了解 Azure SQL Edge 中的安全性。
keywords: SQL Edge, 安全性
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 17e3e8dca1c03f9783c0ca94350bb8a4ba5aca64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933622"
---
# <a name="securing-azure-sql-edge"></a>保护 Azure SQL Edge

随着 IoT 和 Edge 计算在各个行业中的采用的增加，设备数量不断增加，从这些设备生成的数据也在不断增加。 增加的数据量和设备终结点数量给数据和设备的安全性带来了极大的挑战。 

Azure SQL Edge 提供了多个特性和功能，可以使用它们相对简单地在 SQL Server 数据库中保护 IoT 数据。 Azure SQL Edge 是使用为 Microsoft SQL Server 和 Azure SQL 提供支持的同一 SQL 引擎构建的，具有相同的安全功能，可以更轻松地将相同的安全策略和做法从云扩展到边缘。

正如 Microsoft SQL Server 和 Azure SQL 一样，可以将保护 Azure SQL Edge 部署视为一系列步骤，它涉及四个方面：平台、身份验证、对象（包括数据）以及访问系统的应用程序。 

## <a name="platform-and-system-security"></a>平台和系统安全性

Azure SQL Edge 的平台包括物理 docker 主机、主机上的操作系统，以及将物理设备连接到应用程序和客户端的网络系统。 

实现平台安全首先要防止未经授权的用户访问网络。 一些最佳做法包括但不限于：
- 实施防火墙规则，以确保贯彻组织的安全策略。 
- 确保物理设备上的操作系统应用了所有最新的安全更新。 
- 指定和限制用于 Azure SQL Edge 的主机端口
- 确保向承载 Azure SQL Edge 数据的所有数据卷应用正确的访问控制。 

有关 Azure SQL Edge 网络协议和 TDS 终结点的详细信息，请参阅[网络协议和 TDS 终结点](https://docs.microsoft.com//previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105))。

## <a name="authentication-and-authorization"></a>身份验证和授权 

### <a name="authentication"></a>身份验证  
身份验证是证明用户所声明身份的过程。 Azure SQL Edge 当前仅支持 `SQL Authentication` 机制。

- *SQL 身份验证*：

    SQL 身份验证是指使用用户名和密码连接到 Azure SQL Edge 时对用户进行身份验证。 在 SQL Edge 部署过程中，必须指定 SQL **sa** 登录密码。 之后，服务器管理员可以创建额外的 SQL 登录和用户，以允许用户使用用户名和密码进行连接。

    有关在 SQL Edge 中创建和管理登录名和用户的详细信息，请参阅[创建登录名](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login)和[创建数据库用户](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-database-user)。

### <a name="authorization"></a>授权   

授权是指在 Azure SQL Edge 中的数据库中向用户分配权限，并确定允许用户执行的操作。 权限控制通过将用户帐户添加到[数据库角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)并向这些角色分配数据库级权限来实现，也可以通过授予用户特定的[对象级权限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)来实现。 有关详细信息，请参阅[登录名和用户](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)。

最佳做法是根据需要创建自定义角色。 将用户添加到具有完成其作业功能所需的最低权限的角色中。 请勿直接将权限分配给用户。 服务器管理员帐户是内置的 db_owner 角色的成员，该角色具有广泛权限，只应将其授予部分具有管理职责的用户。 对于应用程序，请使用 [EXECUTE AS](https://docs.microsoft.com/sql/t-sql/statements/execute-as-clause-transact-sql) 来指定被调用模块的执行上下文，或者使用权限受限的[应用程序角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles)。 此做法可确保连接到数据库的应用程序具有应用程序所需的最低权限。 按这些最佳做法操作也有助于职责分离。

## <a name="database-object-security"></a>数据库对象安全性

主体是获得了 SQL Edge 访问权限的个体、组和进程。 “安全对象”是服务器、数据库和数据库包含的对象。 每个安全对象都拥有一组权限，可对这些权限进行配置以减少外围应用。 下表包含有关主体和安全对象的信息。

|有关以下方面的信息|请参阅|  
|---------------------------|---------|  
|服务器和数据库用户、角色与进程|[主体数据库引擎](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|服务器和数据库对象安全性|[安全对象](https://docs.microsoft.com/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>加密和证书  
 
加密并不解决访问控制问题。 不过，它可以通过限制数据丢失来增强安全性，即使在访问控制失效的罕见情况下也能如此。 例如，在数据库主机配置有误且恶意用户获取了敏感数据（如信用卡号）的情况下，如果被盗信息已加密，则此信息将毫无用处。 下表包含有关 Azure SQL Edge 中的加密的详细信息。  
  
|有关以下方面的信息|请参阅|  
|---------------------------|---------|  
|实现安全连接|[加密连接](https://docs.microsoft.com/sql/linux/sql-server-linux-encrypted-connections)|  
|加密函数|[加密函数 (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|静态数据加密|[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> 针对 [Linux 上的 SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-security-overview) 介绍的安全限制也适用于 Azure SQL Edge。 


> [!NOTE]
> Azure SQL Edge 未包括 mssql-conf 实用工具。 包括加密相关配置在内的所有配置都需要通过 [mssql.conf 文件](configure.md#configure-by-using-an-mssqlconf-file)或[环境变量](configure.md#configure-by-using-environment-variables)来执行。 


与 Azure SQL 和 Microsoft SQL Server 类似，Azure SQL Edge 提供了相同的机制来创建和使用证书，以增强对象和连接安全性。 有关详细信息，请参阅 [CREATE CERTIFICATE (TRANSACT-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql)。


## <a name="application-security"></a>应用程序安全性

### <a name="client-programs"></a>客户端程序

Azure SQL Edge 安全性最佳做法包括编写安全的客户端应用程序。 有关如何在网络层保护客户端应用程序安全的详细信息，请参阅 [Client Network Configuration](https://docs.microsoft.com/sql/database-engine/configure-windows/client-network-configuration)。

### <a name="sql-server-security-catalog-views-and-functions"></a>SQL Server 安全性目录视图和函数  
 安全信息显示在多个视图和函数（已针对性能和效用进行了优化）中。 下表包含有关安全性视图和函数的信息。  
  
|函数和视图|链接|  
|---------------------------|---------|  
|安全性目录视图，可返回数据库级别和服务器级别权限、主体、角色等方面的信息。 此外，还有提供加密密钥、证书和凭据相关信息的目录视图。|[安全性目录视图 (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|安全函数，返回有关当前用户、权限和架构的信息。|[安全函数 (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/security-functions-transact-sql)|  
|安全性动态管理视图。|[与安全性相关的动态管理视图和函数 (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>审核 

Azure SQL Edge 提供了与 SQL Server 相同的审核机制。 有关详细信息，请参阅 [SQL Server 审核（数据库引擎）](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)。


## <a name="next-steps"></a>后续步骤

- [安全功能入门](https://docs.microsoft.com/sql/linux/sql-server-linux-security-get-started)
- [以非 root 用户身份运行 Azure SQL Edge](configure.md#run-azure-sql-edge-as-non-root-user)
- [用于 IoT 的 Azure 安全中心](https://docs.microsoft.com/azure/asc-for-iot/overview)

