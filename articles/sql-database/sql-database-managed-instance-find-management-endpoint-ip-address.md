---
title: 发现 Azure SQL 数据库托管实例管理终结点 | Microsoft Docs
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
ms.openlocfilehash: a0d47c61bf84cfb22c767fd09b3feed74f55b7fc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567486"
---
# <a name="determine-the-management-endpoint-ip-address"></a>确定管理终结点 IP 地址

Azure SQL 数据库托管实例虚拟群集包含一个管理终结点，可供 Microsoft 用来执行管理操作。 管理终结点在网络级别受内置防火墙保护，在应用程序级别受相互证书验证保护。 你可以确定管理终结点的 IP 地址，但无法访问此终结点。

若要确定管理 IP 地址, 请在托管实例的 FQDN 上执行 DNS 查找`mi-name.zone_id.database.windows.net`:。 这会返回类似`trx.region-a.worker.vnet.database.windows.net`的 DNS 条目。 然后, 你可以对此 FQDN 执行 DNS 查找, 并删除 "vnet"。 这会返回管理 IP 地址。 

如果将 MI FQDN \<\>替换为托管实例的 DNS 条目, 此 PowerShell 将为你完成所有操作: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

有关托管实例和连接的详细信息，请参阅 [Azure SQL 数据库托管实例连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
