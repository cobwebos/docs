---
title: "Azure Analysis Services 中支持的数据源 | Microsoft Docs"
description: "介绍 Azure Analysis Services 中数据模型支持的数据源。"
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
ms.date: 08/15/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: a0fe91568d747148b3940e9c90db15481c765a9c
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017

---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services 中支持的数据源
Azure Analysis Services 服务器支持连接到云中的数据源和组织中的本地数据源。 始终添加其他受支持的数据源。 请经常返回查看。 

当前支持以下数据源：

| 云  |
|---|
| Azure Blob 存储*  |
| Azure SQL 数据库  |
| Azure 数据仓库 |


| 本地  |   |   |   |
|---|---|---|---|
| Access 数据库  | 文件夹* | Oracle Database  | Teradata 数据库 |
| Active Directory*  | JSON 文档*  | Postgre SQL 数据库*  |XML 表* |
| Analysis Services  | 二进制文件中的行*  | SAP HANA*  |
| 分析平台系统  | MySQL 数据库  | SAP Business Warehouse*  | |
| Dynamics CRM*  | OData 源*  | SharePoint*  |
| Excel 工作簿  | ODBC 查询  | SQL 数据库  |
| Exchange*  | OLE DB  | Sybase 数据库  |

\* 仅限表格 1400 模型。 

> [!IMPORTANT]
> 连接到本地数据源需要将[本地数据网关](analysis-services-gateway.md)安装在环境中的计算机上。

## <a name="data-providers"></a>数据提供程序

连接到某些数据源时，Azure Analysis Services 中的数据模型可能需要不同的数据提供程序。 在某些情况下，使用本机提供程序（如 SQL Server Native Client (SQLNCLI11)）连接到数据源的表格模型可能返回错误。

对于连接到云数据源（如 Azure SQL 数据库）的数据模型，如果使用了 SQLOLEDB 之外的本机提供程序，则可能会看到错误消息：“提供程序‘SQLNCLI11.1’未注册”。 或者，在某个 DirectQuery 模型连接到本地数据源时，如果使用了本机提供程序，则可能会看到错误消息：**“创建 OLE DB 行集时出错。‘LIMIT’附近的语法不正确”**。

连接到云或本地的数据源时，内存中或 DirectQuery 数据模型支持以下数据源提供程序：

### <a name="cloud"></a>云
| **数据源** | **内存中** | **DirectQuery** |
|  --- | --- | --- |
| Azure SQL 数据仓库 |用于 SQL Server 的 .NET Framework 数据提供程序 |用于 SQL Server 的 .NET Framework 数据提供程序 |
| Azure SQL 数据库 |用于 SQL Server 的 .NET Framework 数据提供程序 |用于 SQL Server 的 .NET Framework 数据提供程序 | |

### <a name="on-premises-via-gateway"></a>本地（通过网关）
|**数据源** | **内存中** | **DirectQuery** |
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
如有本地数据源，请务必安装[本地网关](analysis-services-gateway.md)。   
若要深入了解如何在 SSDT 或 SSMS 中管理服务器，请参阅[管理服务器](analysis-services-manage.md)。


