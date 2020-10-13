---
title: 发现管理终结点 IP 地址
titleSuffix: Azure SQL Managed Instance
description: 了解如何获取 Azure SQL 托管实例管理终结点公共 IP 地址并验证其内置防火墙保护
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: a9a2b904bd7526f00a8f8a5d013be0c1e42e38a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617360"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>确定管理终结点 IP 地址 - Azure SQL 托管实例 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例虚拟群集包含一个管理终结点，可供 Azure 用来执行管理操作。 管理终结点在网络级别受内置防火墙保护，在应用程序级别受相互证书验证保护。 你可以确定管理终结点的 IP 地址，但无法访问此终结点。

若要确定管理 IP 地址，请对 SQL 托管实例 FQDN 执行 [DNS 查找](/windows-server/administration/windows-commands/nslookup) ： `mi-name.zone_id.database.windows.net` 。 这样会返回类似 `trx.region-a.worker.vnet.database.windows.net` 的 DNS 条目。 然后可以在删除了“.vnet”的此 FQDN 上进行 DNS 查找。 这样会返回管理 IP 地址。 

如果将 \<MI FQDN\> 替换为 SQL 托管实例的 DNS 条目，则此 PowerShell 代码会为你执行所有操作：`mi-name.zone_id.database.windows.net`：
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

有关 SQL 托管实例和连接的详细信息，请参阅 [Azure SQL 托管实例连接体系结构](connectivity-architecture-overview.md)。
