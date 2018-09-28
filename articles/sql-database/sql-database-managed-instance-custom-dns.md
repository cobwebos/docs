---
title: Azure SQL 数据库托管实例自定义 DNS | Microsoft Docs
description: 本主题介绍使用 Azure SQL 数据库托管实例的自定义 DNS 的配置选项。
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 09/23/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 2d1bb7e8522da32dd33933261ea41b578f8afac1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949479"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置自定义 DNS

Azure SQL 数据库托管实例必须在 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 中部署。 有几个方案（即，将服务器链接到云或混合环境中的其他 SQL 实例）需要从托管实例解析专用主机名。 在这种情况下，需要在 Azure 中配置自定义 DNS。 由于托管实例使用同一 DNS 使其内部工作，因此虚拟网络 DNS 配置必须与托管实例兼容。 

要使自定义 DNS 配置与托管实例兼容，需要： 
- 配置自定义 DNS 服务器，以便它能够解析公共域名 
- 将 Azure 递归解析程序 DNS IP 地址 168.63.129.16 放在虚拟网络 DNS 列表末尾 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>设置自定义 DNS 服务器配置

1. 在 Azure 门户中，找到 VNet 的自定义 DNS 选项。

   ![自定义 DNS 选项](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. 切换到“自定义”并输入自定义 DNS 服务器 IP 地址以及 Azure 的递归解析程序 IP 地址 168.63.129.16。 

   ![自定义 DNS 选项](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > 如果没有在 DNS 列表中设置 Azure 的递归解析程序，会导致托管实例进入错误状态。 从该状态恢复可能需要使用符合要求的网络策略在 VNet 中创建新实例、创建实例级数据并还原数据库。 请参阅 [VNet 配置](sql-database-managed-instance-vnet-configuration.md)。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例](sql-database-managed-instance.md)
- 有关演示如何新建托管实例的教程，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
- 有关为托管实例配置 VNet 的信息，请参阅[托管实例的 VNet 配置](sql-database-managed-instance-vnet-configuration.md)
