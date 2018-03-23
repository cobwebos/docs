---
title: Azure SQL 数据库托管实例自定义 DNS | Microsoft Docs
description: 本主题介绍使用 Azure SQL 数据库托管实例的自定义 DNS 的配置选项。
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 993ff518a1987581ad7de4797611f104080c2c2f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置自定义 DNS

Azure SQL 数据库托管实例（预览版）必须在 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 内部署。 有几个方案将服务器链接到云或混合环境中的其他 SQL 实例，需要从托管实例解析专用主机名。 在这种情况下，需要在 Azure 中配置自定义 DNS。 由于托管实例使用同一 DNS 使其内部工作，因此虚拟网络 DNS 配置必须与托管实例兼容。 

要使自定义 DNS 配置与托管实例兼容，需要完成以下步骤： 
- 配置自定义 DNS 将请求转发到 Azure DNS 
- 为 VNet 设置自定义 DNS 作为主 DNS，设置 Azure DNS 作为辅助 DNS 
- 将自定义 DNS 注册为主 DNS，并将 Azure DNS 注册为辅助 DNS

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>配置自定义 DNS 将请求转发到 Azure DNS 

若要在 Windows Server 2016 上配置 DNS 转发，请使用以下步骤： 

1. 在“服务器管理器”中，单击“工具”，然后单击“DNS”。 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. 双击“转发器”。

   ![转发器](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. 单击“编辑”。 

   ![转发器列表](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. 输入 Azure 的递归解析程序 IP 地址，如 168.63.129.16。

   ![递归解析程序 IP 地址](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>将自定义 DNS 设置为主 DNS，并将 Azure DNS 设置为辅助 DNS 
 
Azure VNet 上的 DNS 配置需要输入 IP 地址，因此，请按照以下步骤使用静态 IP 地址配置托管 DNS 服务器的 Azure VM： 

1. 在 Azure 门户中，打开自定义 DNS VM 网络接口。

   ![网络接口](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. 在“IP 配置”部分中。 选择 IP 配置 

   ![IP 配置](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. 将专用 IP 地址设置为静态。 记下 IP 地址（此屏幕截图上的 10.0.1.5） 

   ![静态](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>将自定义 DNS 注册为主 DNS，并将 Azure DNS 注册为辅助 DNS 

1. 在 Azure 门户中，找到 VNet 的自定义 DNS 选项。

   ![自定义 DNS 选项](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. 切换到“自定义”并输入自定义 DNS 服务器 IP 地址以及 Azure 的递归解析程序 IP 地址，如 168.63.129.16。 

   ![自定义 DNS 选项](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > 如果没有在 DNS 列表中设置 Azure 的递归解析程序，会导致托管实例进入错误状态。 从该状态恢复可能需要使用符合要求的网络策略在 VNet 中创建新实例、创建实例级数据并还原数据库。 请参阅 [VNet 配置](sql-database-managed-instance-vnet-configuration.md)。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例](sql-database-managed-instance.md)
- 有关创建 VNet，然后创建托管实例的示例，请参阅[创建托管实例](sql-database-managed-instance-tutorial-portal.md)。
- 有关配置 VNet 的信息，请参阅[托管实例的 VNet 配置](sql-database-managed-instance-vnet-configuration.md)
