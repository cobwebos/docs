---
title: 发现托管实例管理终结点
description: 了解如何获取 Azure SQL 数据库托管实例管理终结点公用 IP 地址并验证其内置防火墙保护
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825715"
---
# <a name="determine-the-management-endpoint-ip-address"></a>确定管理终结点 IP 地址

Azure SQL 数据库托管实例虚拟群集包含一个管理终结点，可供 Microsoft 用来执行管理操作。 管理终结点在网络级别受内置防火墙保护，在应用程序级别受相互证书验证保护。 你可以确定管理终结点的 IP 地址，但无法访问此终结点。

若要确定管理 IP 地址，请在托管实例 FQDN `mi-name.zone_id.database.windows.net` 上进行 DNS 查找。 这样会返回类似 `trx.region-a.worker.vnet.database.windows.net` 的 DNS 条目。 然后可以在删除了“.vnet”的此 FQDN 上进行 DNS 查找。 这样会返回管理 IP 地址。 

如果将 \<MI FQDN\> 替换为你的托管实例的 DNS 条目 `mi-name.zone_id.database.windows.net`，则此 PowerShell 会为你执行所有操作：
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

有关托管实例和连接的详细信息，请参阅 [Azure SQL 数据库托管实例连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
