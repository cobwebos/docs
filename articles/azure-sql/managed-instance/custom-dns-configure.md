---
title: 自定义 DNS
titleSuffix: Azure SQL Managed Instance
description: 本主题介绍使用 Azure SQL 托管实例的自定义 DNS 的配置选项。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 2ba5794ba647c28cde3b54a1afdfbd0201b23e8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84706148"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>为 Azure SQL 托管实例配置自定义 DNS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

必须在 Azure[虚拟网络（VNet）](../../virtual-network/virtual-networks-overview.md)中部署 azure SQL 托管实例。 有几个方案（例如，数据库邮件，将服务器链接到云或混合环境中的其他 SQL Server 实例）需要从 SQL 托管实例解析专用主机名。 在这种情况下，需要在 Azure 中配置自定义 DNS。 

由于 SQL 托管实例为其内部工作原理使用相同的 DNS，因此请配置自定义 DNS 服务器，使其能够解析公共域名。

> [!IMPORTANT]
> 对于邮件服务器，请始终使用完全限定的域名（FQDN），对于 SQL Server 实例，始终使用完全限定的域名（FQDN），即使它们在专用 DNS 区域内也是如此。 例如，请对邮件服务器使用 `smtp.contoso.com`，因为 `smtp` 无法正确解析。 创建引用同一虚拟网络中 SQL Server Vm 的链接服务器或复制还需要 FQDN 和默认 DNS 后缀。 例如，`SQLVM.internal.cloudapp.net`。 有关详细信息，请参阅[使用自己的 DNS 服务器的名称解析](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)。

> [!IMPORTANT]
> 更新虚拟网络的 DNS 服务器不会立即影响 SQL 托管实例。 在 DHCP 租约过期或平台升级之后（以先发生者为准），SQL 托管实例 DNS 配置将更新。 **建议用户先设置其虚拟网络 DNS 配置，然后再创建第一个托管实例。**

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是 AZURE SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 有关演示如何创建新的托管实例的教程，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关为托管实例配置 VNet 的信息，请参阅[托管实例的 vnet 配置](connectivity-architecture-overview.md)。
