---
title: Azure 中的 SQL Server 的安全注意事项 | Microsoft Docs
description: 本主题提供有关保护 Azure 虚拟机中运行的 SQL Server 的一般指导。
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: jroth
ms.openlocfilehash: b91638b6b76675711150323bee4aa9ad9d9a73d1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Azure 虚拟机中的 SQL Server 的安全注意事项

本主题包括用于帮助与 Azure 虚拟机 (VM) 中的 SQL Server 实例建立安全访问的总体安全准则。

Azure 遵守多个行业法规和标准，使你能够使用在虚拟机中运行的 SQL Server 构建合规的解决方案。 有关 Azure 合规性的信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/)。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>控制对 SQL VM 的访问

创建 SQL Server 虚拟机时，请考虑如何谨慎控制谁有权访问计算机和 SQL Server。 一般情况下，应该采取以下措施：

- 将 SQL Server 访问权限限制为需要它的应用程序和客户端。
- 遵照最佳做法来管理用户帐户和密码。

以下部分提供有关如何实施这些要点的建议。

## <a name="secure-connections"></a>安全连接

使用库映像创建 SQL Server 虚拟机时，可以使用“SQL Server 连接”选项来选择“本地(VM 内部)”、“专用(虚拟网络内部)”或“公共(Internet)”。

![SQL Server 连接](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

为了获得最佳安全性，请为方案选择限制性最强的选项。 例如，如果要运行的应用程序需要访问同一个 VM 的 SQL Server，则“本地”是最安全的选项。 如果要运行的 Azure 应用程序需要访问 SQL Server，则“专用”选项只能保护与指定的 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)中的 SQL Server 之间的通信。 如果需要使用“公共(Internet)”选项访问 SQL Server VM，请确保遵照本主题中的其他最佳做法，以减小受攻击面。

在门户中选择的选项使用 VM [网络安全组](../../../virtual-network/virtual-networks-nsg.md) (NSG) 上的入站安全规则来允许或拒绝发往虚拟机的网络流量。 可以修改或创建新的入站 NSG 规则，以允许发往 SQL Server 端口（默认为 1433）的流量。 此外，还可以指定被允许通过此端口通信的特定 IP 地址。

![网络安全组规则](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

除了用于限制网络流量的 NSG 规则以外，还可以在虚拟机上使用 Windows 防火墙。

如果在经典部署模型中使用终结点，可以在不需要使用时删除虚拟机上的任何终结点。 有关在终结点上使用 ACL 的说明，请参阅[管理终结点上的 ACL](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint)。 使用 Resource Manager 的 VM 并不需要 ACL。

最后，请考虑针对 Azure 虚拟机中的 SQL Server 数据库引擎实例启用加密连接。 使用签名证书配置 SQL Server 实例。 有关详细信息，请参阅[为数据库引擎启用加密连接](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)和[连接字符串语法](https://msdn.microsoft.com/library/ms254500.aspx)。

## <a name="use-a-non-default-port"></a>使用非默认端口

SQL Server 默认在已知端口 1433 上侦听。 为了提高安全性，请将 SQL Server 配置为在非默认端口（例如 1401）上侦听。 如果在 Azure 门户中预配 SQL Server 库映像，可以在“SQL Server 设置”边栏选项卡中指定此端口。

若要在预配后配置此端口，可以使用两个选项：

- 对于 Resource Manager VM，可以从 VM 概述边栏选项卡中选择“SQL Server 配置”。 这会提供一个用于更改端口的选项。

  ![在门户中更改 TCP 端口](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- 对于经典 VM 或者未使用门户预配的 SQL Server VM，可以通过远程连接到 VM 来手动配置端口。 有关配置步骤，请参阅[将服务器配置为在特定的 TCP 端口上侦听](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)。 如果使用这种手动方法，则还需要添加一个 Windows 防火墙规则来允许该 TCP 端口上的传入流量。

> [!IMPORTANT]
> 如果 SQL Server 端口向公共 Internet 连接开放，则指定非默认端口是一种很好的做法。

当 SQL Server 在非默认端口上侦听时，必须在连接时指定该端口。 例如，假设存在这种情况：服务器 IP 地址为 13.55.255.255，SQL Server 在端口 1401 上侦听。 若要连接到 SQL Server，应在连接字符串中指定 `13.55.255.255,1401`。

## <a name="manage-accounts"></a>管理帐户

我们都不希望攻击者能够轻松猜出帐户名或密码。 以下提示可以提供帮助：

- 创建一个未命名为 **Administrator** 的唯一本地管理员帐户。

- 对所有帐户使用复杂的强密码。 有关如何创建强密码的详细信息，请参阅[创建强密码](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password)一文。

- 默认情况下，Azure 在 SQL Server 虚拟机安装期间选择 Windows 身份验证。 因此，禁用 **SA** 登录名，并由安装程序分配密码。 我们不建议使用或启用 **SA** 登录名。 如果必须使用 SQL 登录名，请使用以下策略之一：

  - 创建一个具有 **sysadmin** 成员身份且名称唯一的 SQL 帐户。 在门户中预配期间启用“SQL 身份验证”即可实现此目的。

    > [!TIP] 
    > 如果在预配期间未启用“SQL 身份验证”，则必须手动将身份验证模式更改为“SQL Server 和 Windows 身份验证模式”。 有关详细信息，请参阅[更改服务器身份验证模式](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode)。

  - 如果必须使用 **SA** 登录名，请在预配后启用该登录名，并分配一个新的强密码。

## <a name="follow-on-premises-best-practices"></a>遵照本地最佳做法

除了本主题中所述的做法以外，我们还建议在适当的情况下查看并实施传统的本地安全做法。 有关详细信息，请参阅 [SQL Server 安装的安全注意事项](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>后续步骤

如果还对性能最佳实践感兴趣，请参阅 [Azure 虚拟机中 SQL Server 的性能最佳实践](virtual-machines-windows-sql-performance.md)。

有关其他与在 Azure VM 中运行 SQL Server 相关的主题，请参阅 [Azure 虚拟机上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)。

