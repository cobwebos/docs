---
title: 发现管理终结点 IP 地址
titleSuffix: Azure SQL Managed Instance
description: 了解如何获取 Azure SQL 托管实例管理终结点公共 IP 地址并验证其内置防火墙保护
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 40a44fe46cf38c633380c4c353960cc4e11f2f3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708716"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>确定管理终结点 IP 地址-Azure SQL 托管实例 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例虚拟群集包含 Azure 用于管理操作的管理终结点。 管理终结点在网络级别受内置防火墙保护，在应用程序级别受相互证书验证保护。 你可以确定管理终结点的 IP 地址，但无法访问此终结点。

若要确定管理 IP 地址，请对 SQL 托管实例 FQDN 执行[DNS 查找](/windows-server/administration/windows-commands/nslookup)： `mi-name.zone_id.database.windows.net` 。 这样会返回类似 `trx.region-a.worker.vnet.database.windows.net` 的 DNS 条目。 然后可以在删除了“.vnet”的此 FQDN 上进行 DNS 查找。 这样会返回管理 IP 地址。 

如果你将替换为 SQL 托管实例的 DNS 条目，此 PowerShell 代码将为你完成所有 \<MI FQDN\> 操作： `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

有关 SQL 托管实例和连接的详细信息，请参阅[AZURE sql 托管实例连接体系结构](connectivity-architecture-overview.md)。
