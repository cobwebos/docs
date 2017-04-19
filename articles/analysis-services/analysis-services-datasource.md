---
title: "数据源连接 | Microsoft Docs"
description: "介绍 Azure Analysis Services 中数据模型的数据源连接。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/14/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: a287ebd634a9305229424d0efea266146f88a952
ms.openlocfilehash: 234032630cb3911deb7c7d32cfc4963ad6aee43f
ms.lasthandoff: 01/18/2017


---
# <a name="datasource-connections"></a>数据源连接
连接到某些数据源时，Azure Analysis Services 中的数据模型可能需要不同的数据提供程序。 在某些情况下，使用本机提供程序（如 SQL Server Native Client (SQLNCLI11)）连接到数据源的表格模型可能返回错误。

例如，在具有连接到云数据源（如 Azure SQL 数据库）的内存中数据模型或直接查询数据模型时，如果使用了 SQLOLEDB 之外的本机提供程序，则可能会看到错误消息：**“提供程序‘SQLNCLI11.1’未注册”**。

或者，在某个 DirectQuery 模型连接到本地数据源时，如果使用了本机提供程序，则可能会看到错误消息：**“创建 OLE DB 行集时出错。‘LIMIT’附近的语法不正确”**。

## <a name="data-source-providers"></a>数据源提供程序
连接到云或本地的数据源时，内存中或直接查询数据模型支持以下数据源提供程序：

### <a name="cloud"></a>云
| **数据源** | **内存中** | **直接查询** |
|  --- | --- | --- |
| Azure SQL 数据仓库 |用于 SQL Server 的 .NET Framework 数据提供程序 |用于 SQL Server 的 .NET Framework 数据提供程序 |
| Azure SQL 数据库 |用于 SQL Server 的 .NET Framework 数据提供程序 |用于 SQL Server 的 .NET Framework 数据提供程序 | |

### <a name="on-premises-via-gateway"></a>本地（通过网关）
|**数据源** | **内存中** | **直接查询** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |用于 SQL Server 的 .NET Framework 数据提供程序 |
| SQL Server |Microsoft OLE DB Provider for SQL Server |用于 SQL Server 的 .NET Framework 数据提供程序 | |
| SQL Server |用于 SQL Server 的 .NET Framework 数据提供程序 |用于 SQL Server 的 .NET Framework 数据提供程序 | |
| Oracle |Microsoft OLE DB Provider for Oracle |用于 .Net 的 Oracle 数据提供程序 | |
| Oracle |用于 .Net 的 Oracle 数据提供程序 |用于 .Net 的 Oracle 数据提供程序 | |
| Teradata |OLE DB Provider for Teradata |用于 .Net 的 Teradata 数据提供程序 | |
| Teradata |用于 .Net 的 Teradata 数据提供程序 |用于 .Net 的 Teradata 数据提供程序 | |
| 分析平台系统 |用于 SQL Server 的 .NET Framework 数据提供程序 |用于 SQL Server 的 .NET Framework 数据提供程序 | |

> [!NOTE]
> 请确保在使用本地网关时安装了 64 位提供程序。
> 
> 

将本地 SQL Server Analysis Services 表格模型迁移到 Azure Analysis Services 时，可能需要更改提供程序。

**指定数据源提供程序**

1. 在 SSDT >“表格模型浏览器” > “数据源”中，右键单击数据源连接，然后单击“编辑数据源”。
2. 在“编辑连接”中，单击“高级”，打开“高级属性”窗口。
3. 在“设置高级属性” > “提供程序”中，选择适当的提供程序。

## <a name="impersonation"></a>模拟
某些情况下可能需要指定其他模拟帐户。 可在 SSDT 或 SSMS 中指定模拟帐户。

对于本地数据源：

* 如果使用 SQL 身份验证，则模拟应为服务帐户。
* 如果使用 Windows 身份验证，请设置 Windows 用户/密码。 对于 SQL Server，只有内存中数据模型才支持带有特定模拟帐户的 Windows 身份验证。

对于云数据源：

* 如果使用 SQL 身份验证，则模拟应为服务帐户。

## <a name="next-steps"></a>后续步骤
如有本地数据源，请务必安装[本地网关](analysis-services-gateway.md)。 若要深入了解如何在 SSDT 或 SSMS 中管理服务器，请参阅[管理服务器](analysis-services-manage.md)。


