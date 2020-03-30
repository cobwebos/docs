---
title: 托管实例自定义 DNS
description: 本主题介绍使用 Azure SQL 数据库托管实例的自定义 DNS 的配置选项。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247072"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置自定义 DNS

Azure SQL 数据库托管实例必须在 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 中部署。 有几个方案（例如，数据库邮件，将服务器链接到云或混合环境中的其他 SQL 实例）需要从托管实例解析专用主机名。 在这种情况下，需要在 Azure 中配置自定义 DNS。 

由于托管实例对内部工作使用同一 DNS，因此需要对自定义 DNS 服务器进行配置，使之能够解析公共域名。

> [!IMPORTANT]
> 始终对邮件服务器、SQL Server 实例和其他服务使用完全限定的域名 (FQDN)，即使它们位于专用 DNS 区域内也是如此。 例如，请对邮件服务器使用 `smtp.contoso.com`，因为 `smtp` 无法正确解析。 创建引用同一虚拟网络中的 SQL VM 的链接服务器或复制也需要 FQDN 和默认 DNS 后缀。 例如，`SQLVM.internal.cloudapp.net` 。 有关详细信息，请参阅[使用自己的 DNS 服务器的名称解析](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)。

> [!IMPORTANT]
> 更新虚拟网络 DNS 服务器不会立即影响托管实例。 托管实例 DNS 配置在 DHCP 租约过期或平台升级后（以先发生者为准）后更新。 **建议用户在创建第一个托管实例之前，先设置其虚拟网络 DNS 配置。**

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例](sql-database-managed-instance.md)
- 有关演示如何新建托管实例的教程，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
- 有关为托管实例配置 VNet 的信息，请参阅[托管实例的 VNet 配置](sql-database-managed-instance-connectivity-architecture.md)
