---
title: "与 Azure 搜索的 SQL VM 连接 | Microsoft Docs"
description: "启用加密连接并配置防火墙，支持从 Azure 搜索上的索引器连接到 Azure 虚拟机 (VM) 上的 SQL Server。"
services: search
documentationcenter: 
author: HeidiSteen
manager: pablocas
editor: 
ms.assetid: 46e42e0e-c8de-4fec-b11a-ed132db7e7bc
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 814292d76601452493c1d708c39d42da909036d9
ms.openlocfilehash: 1e436978da0f2fc8c122a1a99bd891fe82929f50


---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>配置从 Azure 搜索索引器到 Azure VM 上 SQL Server 的连接
如[使用索引器将 Azure SQL 数据库连接到 Azure 搜索](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#frequently-asked-questions)中所述，针对 **Azure VM 上的 SQL Server**（或简称 **SQL Azure VM**）创建索引器受 Azure 搜索支持，但首先需要满足一些与安全性相关的先决条件。 

**任务持续时间：**大约 30 分钟，假设已在 VM 上安装了证书。

## <a name="enable-encrypted-connections"></a>启用加密连接
对于所有通过公共 Internet 连接的索引器请求，Azure 搜索都需要使用加密通道。 本部分列出了实现此目的的步骤。

1. 查看证书的属性，验证使用者名称是否是 Azure VM 的完全限定的域名 (FQDN)。 可以使用 CertUtils 等工具或证书管理单元查看属性。 可从 [Azure 门户](https://portal.azure.com/)中 VM 服务边栏选项卡的“基本要素”部分中获取 FQDN（位于“公共 IP 地址/DNS 名称标签”字段中）。
   
   * 对于使用较新的 **Resource Manager** 模板创建的 VM，FQDN 的格式设置为 `<your-VM-name>.<region>.cloudapp.azure.com`。 
   * 对于创建为**经典** VM 的较旧 VM，FQDN 的格式设置为 `<your-cloud-service-name.cloudapp.net>`。 
2. 使用注册表编辑器 (regedit) 将 SQL Server 配置为使用证书。 
   
    尽管 SQL Server 配置管理器通常用于此任务，但不能在此方案中使用它。 它不会查找导入的证书，因为 Azure 上 VM 的 FQDN 与该 VM（它将域标识为本地计算机或已加入到的网络域）确定的 FQDN 不匹配。 名称不匹配时，使用 regedit 指定证书。
   
   * 在 regedit 中，浏览到此注册表项：`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`。
     
     `[MSSQL13.MSSQLSERVER]` 部分因版本和实例名称而异。 
   * 将**证书**密钥的值设置为已导入到 VM 的 SSL 证书的**指纹**。
     
     可通过多种方式获取指纹，有些方式十分有效。 如果你从 MMC 的**证书**管理单元中复制指纹，可能会[如此支持文章中所述](https://support.microsoft.com/kb/2023869/)选取不可见的前导字符，这会导致在尝试连接时出错。 提供了几种更正此问题的解决方法。 最简单的方法是按 Backspace 键退格，然后重新键入指纹的第一个字符，以在 regedit 中删除密钥值字段中的前导字符。 此外，也可以使用其他工具复制指纹。
3. 向服务帐户授予权限。 
   
    请确保向 SQL Server 服务帐户授予 SSL 证书私钥的相应权限。 如果忽略此步骤，SQL Server 将不会启动。 可使用**证书**管理单元或 **CertUtils** 执行此任务。
4. 重新启动 SQL Server 服务。

## <a name="configure-sql-server-connectivity-in-the-vm"></a>在 VM 中配置 SQL Server 连接
设置 Azure 搜索所需的加密连接后，Azure VM 上的 SQL Server 内还有一些其他配置步骤。 如果尚未执行这些步骤，下一步是使用以下文章之一完成配置：

* 有关 **Resource Manager** VM，请参阅[使用 Resource Manager 连接到 Azure 上的 SQL Server 虚拟机](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md)。 
* 有关**经典** VM，请参阅[连接到 Azure 上的 SQL Server 虚拟机（经典）](../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md)。

具体而言，查看每个文章中的“通过 Internet 连接”部分。

## <a name="configure-the-network-security-group-nsg"></a>配置网络安全组 (NSG)
若要使其他方可以访问 Azure VM，通常配置 NSG 和相应的 Azure 终结点或访问控制列表 (ACL)。 你可能之前已完成此操作，以允许自己的应用程序逻辑连接到 SQL Azure VM。 这不同于将 Azure 搜索连接到 SQL Azure VM。 

下面的链接提供了有关 VM 部署的 NSG 配置的说明。 使用这些说明，根据其 IP 地址为 Azure 搜索终结点配置 ACL。

> [!NOTE]
> 有关背景知识，请参阅[什么是网络安全组？](../virtual-network/virtual-networks-nsg.md)
> 
> 

* 有关 **Resource Manager** VM，请参阅[如何为 ARM 部署创建 NSG](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)。 
* 有关**经典** VM，请参阅[如何为经典部署创建 NSG](../virtual-network/virtual-networks-create-nsg-classic-ps.md)。

IP 寻址会产生一些挑战，如果你了解问题和潜在解决方法，则可以轻松应对。 剩余部分提供了有关处理 ACL 中与 IP 地址相关的问题的建议。

#### <a name="restrict-access-to-the-search-service-ip-address"></a>限制对搜索服务 IP 地址的访问
我们强烈建议你限制对 ACL 中搜索服务 IP 地址的访问，而不是允许 SQL Azure VM 接受任何连接请求。 通过对搜索服务的 FQDN（例如 `<your-search-service-name>.search.windows.net`）进行 ping 操作，可轻松找到 IP 地址。

#### <a name="managing-ip-address-fluctuations"></a>管理 IP 地址波动
如果你的搜索服务只有一个搜索单位（即一个副本和一个分区），IP 地址将在例程服务重新启动期间发生更改，这会导致搜索服务的 IP 地址的现有 ACL 无效。

避免后续连接错误的一种方法是，在 Azure 搜索中使用多个副本和一个分区。 这样做会增加成本，但也会解决 IP 地址问题。 在 Azure 搜索中，当具有多个搜索单位时，不会更改 IP 地址。

第二种方法是允许连接失败，然后在 NSG 中重新配置 ACL。 一般情况下，IP 地址应每隔几周更改一次。 对于不常执行受控编制索引的客户，此方法可能可行。

第三个可行（但不是特别安全）的方法是指定预配你的搜索服务的 Azure 区域的 IP 地址范围。 将公共 IP 地址分配到 Azure 资源时所依据的 IP 范围列表已在 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)中发布。 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>包括 Azure 搜索门户 IP 地址
如果使用 Azure 门户创建索引器，Azure 搜索门户逻辑还需要在创建期间访问 SQL Azure VM。 可通过对 `stamp2.search.ext.azure.com` 执行 ping 操作找到 Azure 搜索门户 IP 地址。

## <a name="next-steps"></a>后续步骤
完成配置后，你现在可以将 Azure VM 上的 SQL Server 指定为 Azure 搜索索引器的数据源。 有关详细信息，请参阅[使用索引器将 Azure SQL 数据库连接到 Azure 搜索](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。




<!--HONumber=Jan17_HO4-->


