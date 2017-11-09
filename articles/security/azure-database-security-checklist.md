---
title: "Azure 数据库安全性清单 | Microsoft Docs"
description: "本文提供有关 Azure 数据库安全性的一组清单。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: 5047635555a6b4592c0714677c2b942e50bad344
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-security-checklist"></a>Azure 数据库安全性清单

为了帮助提高安全性，Azure 数据库包括大量可用于限制和控制访问的内置安全控件。

其中包括：

-   防火墙，可用于创建[防火墙规则](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure)，以便根据IP 地址限制连接，
-   可从 Azure 门户访问的服务器级防火墙
-   可从 SSMS 访问的数据库级防火墙规则
-   使用安全连接字符串保护数据库连接
-   使用访问管理
-   数据加密
-   SQL 数据库审核
-   SQL 数据库威胁检测

## <a name="introduction"></a>介绍
云计算需使用许多应用程序用户、数据库管理员和程序员不熟悉的新安全范例。 由于这个原因，一些组织对于是否要出于安全风险因素实现云基础结构以进行数据管理犹豫不决。 但是，通过更好地了解 Microsoft Azure 和 Microsoft Azure SQL 数据库中内置的安全功能，可极大减缓这方面的担忧。

## <a name="checklist"></a>清单
查看此清单之前，建议阅读 [Azure 数据库安全性最佳做法](https://docs.microsoft.com/en-us/azure/security/azure-database-security-best-practices)一文。 了解最佳做法后，便能够充分利用此清单。 然后，可使用此清单确保解决重要的 Azure 数据库安全性问题。


|清单类别| 说明|
| ------------ | -------- |
|**保护数据**||
| <br> 动态加密/传输中加密| <ul><li>[传输层安全性](https://docs.microsoft.com/en-us/windows-server/security/tls/transport-layer-security-protocol)，用于数据移动到网络时的数据加密。</li><li>数据库要求来自于客户端的通信是基于 [TDS（表格格式数据流）](https://msdn.microsoft.com/en-in/library/dd357628.aspx)协议、通过 TLS（传输层安全性）实现的安全通信。</li></ul> |
|<br>静态加密| <ul><li>[透明数据加密](http://go.microsoft.com/fwlink/?LinkId=526242)，适用于非活动数据以任何数字形式和物理方式存储时的情况。</li></ul>|
|**控制访问**||  
|<br> 数据库访问 | <ul><li>[身份验证](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access)（Azure Active Directory 身份验证），AD 身份验证使用 Azure Active Directory 管理的标识。</li><li>[授权](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access)，授予用户必需的最低权限。</li></ul> |
|<br>应用程序访问| <ul><li>[行级别安全性](https://msdn.microsoft.com/library/dn765131)（使用安全策略，同时基于用户的标识、角色或执行上下文来限制行级别访问）。</li><li>[动态数据掩码](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started)（使用“权限和策略”，通过对非特权用户模糊化敏感数据来限制此类数据的泄露）</li></ul>|
|**主动监视**||  
| <br>跟踪和检测| <ul><li>[审核](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing)跟踪数据库事件，并将事件写入 [Azure 存储帐户](https://docs.microsoft.com/en-us/azure/storage/storage-create-storage-account)中的审核日志/活动日志。</li><li>使用 [Azure Monitor 活动日志](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)跟踪 Azure 数据库运行状况。</li><li>[威胁检测](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection)会检测异常的数据库活动，指出数据库有潜在的安全威胁。 </li></ul> |
|<br>Azure 安全中心| <ul><li>[数据监视](https://docs.microsoft.com/en-us/azure/security-center/security-center-enable-auditing-on-sql-databases)，使用 Azure 安全中心作为 SQL 和其他 Azure 服务的集中式安全监视解决方案。</li></ul>|     

## <a name="conclusion"></a>结束语
Azure 数据库是一个可靠的数据库平台，提供满足众多组织要求和法规遵从要求的整套安全功能。 通过控制对数据的物理访问，结合透明数据加密、单元格级加密或行级别安全性使用各种文件级、列级或行级数据安全选项，可轻松保护数据。 此外，Always Encrypted 支持针对加密的数据执行操作，简化应用程序更新的过程。 反过来，访问 SQL 数据库活动的审核日志可以提供所需的信息来帮助自己了解何时以何种方法访问了数据。

## <a name="next-steps"></a>后续步骤
只需几个简单的步骤，即可大大提升数据库抵御恶意用户或未经授权的访问的能力。 在本教程中，学习：

- 为服务器和/或数据库设置[防火墙规则](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure)。
- 通过[加密](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/sql-server-encryption)保护数据。
- 启用 [SQL 数据库审核](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing)。

