---
title: DNS 别名
description: 应用程序可以连接到 Azure SQL 数据库的服务器名称的别名。 另外，随时都可以更改别名所指向的 SQL 数据库，以方便执行测试和其他操作。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: d208a9b9f8e1cc16e2c72aa825a2daf88ad00176
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86145656"
---
# <a name="dns-alias-for-azure-sql-database"></a>Azure SQL 数据库的 DNS 别名
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL 数据库有一个域名系统 (DNS) 服务器。 PowerShell 和 REST API 允许为[逻辑 SQL 服务器](logical-servers.md)名称[使用相应的调用来创建和管理 DNS 别名](#anchor-powershell-code-62x)。

可以使用 DNS 别名来取代服务器名称。 客户端程序可在其连接字符串中使用别名。 DNS 别名提供一个转换层，用于将客户端程序重定向到不同的服务器。 使用此层无需查找和编辑所有客户端及其连接字符串，因此降低了复杂性。

DNS 别名的常见用途包括：

- 为服务器创建易记的名称。
- 在初始开发期间，别名可以指向测试服务器。 应用程序上线后，你可以修改别名以引用生产服务器。 从测试到生产的转换不需要对连接到服务器的多个客户端的配置进行任何修改。
- 假设只将应用程序中的唯一数据库移到了另一个服务器。 可以修改别名，而无需修改多个客户端的配置。
- 在区域服务中断期间，你可以使用异地还原在不同的服务器和区域中恢复数据库。 你可以修改现有别名以指向新服务器，以便现有客户端应用程序可以重新连接到它。

## <a name="domain-name-system-dns-of-the-internet"></a>Internet 的域名系统 (DNS)

Internet 依赖于 DNS。 DNS 将友好名称转换为服务器的名称。

## <a name="scenarios-with-one-dns-alias"></a>使用一个 DNS 别名的方案

假设你需要将系统切换到新的服务器。 在过去，需要查找并更新每个客户端程序中的每个连接字符串。 而现在，如果连接字符串使用 DNS 别名，则只需更新别名属性。

Azure SQL 数据库的 DNS 别名功能有助于实现以下方案：

### <a name="test-to-production"></a>测试到生产

在开始开发客户端程序时，可让这些程序在其连接字符串中使用 DNS 别名。 将别名属性指向服务器的测试版本。

以后，当有新的系统在生产环境中上线时，可将别名属性更新为指向生产服务器。 不需要对客户端程序进行任何更改。

### <a name="cross-region-support"></a>跨区域支持

灾难恢复可将服务器转移到不同的地理区域。 对于使用 DNS 别名的系统，不需要查找并更新所有客户端的所有连接字符串。 可将别名更新为指向现在正在托管 Azure SQL 数据库的新服务器。

## <a name="properties-of-a-dns-alias"></a>DNS 别名的属性

以下属性适用于服务器的每个 DNS 别名：

- 唯一的名称：像服务器名称一样，创建的每个别名在所有服务器上保持唯一。
- 需要服务器：除非 DNS 别名恰好引用一个服务器，并且该服务器存在，否则无法创建该别名。 更新的别名必须始终恰好引用一个现有服务器。
  - 删除某个服务器时，Azure 系统也会删除指向该服务器的所有 DNS 别名。
- 不受限于任一区域：DNS 别名不受限于某一区域。 可以将任何 DNS 别名更新为指向位于任何地理区域中的服务器。
  - 但是，将别名更新为引用另一台服务器时，这两台服务器必须位于同一个 Azure 订阅中。
- *权限：* 管理 DNS 别名的用户必须拥有“服务器参与者”权限或更高权限。 有关详细信息，请参阅 [Azure 门户中基于角色的访问控制入门](../../role-based-access-control/overview.md)。

## <a name="manage-your-dns-aliases"></a>管理 DNS 别名

可以使用 REST API 和 PowerShell cmdlet 以编程方式管理 DNS 别名。

### <a name="rest-apis-for-managing-your-dns-aliases"></a>用于管理 DNS 别名的 REST API

以下网页中提供了 REST API 的文档：

- [Azure SQL 数据库 REST API](https://docs.microsoft.com/rest/api/sql/)

另外，GitHub 中也提供了 REST API：

- [Azure SQL 数据库 DNS 别名 REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>用于管理 DNS 别名的 PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> 仍然支持 PowerShell Azure 资源管理器模块，但是所有未来的开发都是针对 Az.Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

可以使用 PowerShell cmdlet 来调用 REST API。

以下文档介绍了用于管理 DNS 别名的 PowerShell cmdlet 代码示例：

- [用于管理 Azure SQL 数据库 DNS 别名的 PowerShell](dns-alias-powershell-create.md)

代码示例中使用的 cmdlet 如下：

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)：在 Azure SQL 数据库服务系统中创建新的 DNS 别名。 该别名指代服务器 1。
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)：获取并列出分配给服务器 1 的所有 DNS 别名。
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias)：将别名根据配置引用的服务器名称从“服务器 1”修改为“服务器 2”。
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)：使用别名从服务器 2 删除 DNS 别名。

## <a name="limitations-during-preview"></a>预览期间的限制

目前，DNS 别名存在以下限制：

- 延迟最长为 2 分钟：最长需要 2 分钟才能更新或删除 DNS 别名。
  - 不管延迟时间有多短，别名都会使客户端连接立即停止引用旧服务器。
- DNS 查找：目前，检查给定 DNS 别名引用哪台服务器的唯一权威方法是执行 [DNS 查找](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup)。
- _不支持表审核：_ 在已对数据库启用了表审核的服务器上，无法使用 DNS 别名。
  - 表审核已弃用。
  - 我们建议改用 [Blob 审核](../../azure-sql/database/auditing-overview.md)。

## <a name="related-resources"></a>相关资源

- [使用 Azure SQL 数据库实现业务连续性的概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)，其中包括灾难恢复方案。
- [Azure REST API 参考](https://docs.microsoft.com/rest/api/azure/)
- [服务器 DNS 别名 API](https://docs.microsoft.com/rest/api/sql/serverdnsaliases)

## <a name="next-steps"></a>后续步骤

- [用于管理 Azure SQL 数据库 DNS 别名的 PowerShell](dns-alias-powershell-create.md)
