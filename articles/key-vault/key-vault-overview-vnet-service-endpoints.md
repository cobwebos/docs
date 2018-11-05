---
ms.assetid: ''
title: Azure Key Vault 的 VNET 服务终结点 |Microsoft Docs
description: Key Vault 的虚拟网络服务终结点概述
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: 6e0029e051e418bc54471284547329a0b0a2e9cd
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50246648"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault 的虚拟网络服务终结点

通过 Key Vault 的虚拟网络服务终结点可将访问限制为指定虚拟网络和/或一系列 IPv4（Internet 协议版本 4）地址范围。 任何从外部连接到密钥保管库的调用方都无法访问这些资源。 如果客户已选择加入以允许“受信任的 Microsoft 服务”，例如 Office 365 Exchange Online、Office 365 SharePoint Online、Azure 计算、Azure 资源管理器，Azure 备份等，则来自这些服务的连接可通过防火墙。 当然，此类调用方仍需提供有效的 AAD 令牌，并且必须具有执行所请求的操作的权限（配置为访问策略）。 详细了解有关[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的技术详情。

## <a name="usage-scenarios"></a>使用方案

可以将 [Key Vault 防火墙和虚拟网络](key-vault-network-security.md)配置为默认拒绝访问来自所有网络的流量（包括 Internet 流量）。 可以向来自特定 Azure 虚拟网络和/或公共 Internet IP 地址范围的流量授予访问权限，为应用程序构建安全的网络边界。

> [!NOTE]
> Key Vault 防火墙和虚拟网络规则仅适用于密钥保管库[数据平面](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)。 Key Vault 控制平面操作（例如密钥保管库创建、删除、修改操作，设置访问策略，设置防火墙和虚拟网络规则）不受防火墙和虚拟网络规则的影响。

例如，
* 如果使用 Key Vault 存储加密密钥、应用程序机密、证书，并希望阻止从公共 Internet 访问密钥保管库。
* 你希望限制访问密钥保管库，以便只有你的应用程序或指定的少部分主机才能连接到密钥保管库
* 你有一个在 Azure 虚拟网络 (VNET) 中运行的应用程序，并且此 VNET 中限制了所有的入站和出站流量。 应用程序仍需连接到密钥保管库，以获取机密或证书，或者使用加密密钥。

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>配置 Key Vault 防火墙和虚拟网络

以下是配置防火墙和虚拟网络所需的步骤。 无论使用哪种界面（PowerShell、CLI、Azure 门户）设置防火墙和虚拟网络规则，采用的步骤都相同。
1. 虽然没有强制要求，但强烈建议采取的操作：启用[密钥保险库日志记录](key-vault-logging.md)以查看详细的访问日志。 当防火墙和虚拟网络规则阻止访问密钥保管库时，此操作有助于进行诊断。
2. 为目标虚拟网络和子网启用“密钥保管库的服务终结点”
3. 为密钥保管库设置防火墙和虚拟网络规则，仅限特定虚拟网络、子网和 IPv4 地址范围能够访问该密钥保管库。
4. 如果需要所有受信任的 Microsoft 服务都能够访问此密钥保管库，则需启用该选项，允许“受信任的 Azure 服务”连接到密钥保管库。

请参阅[配置 Azure Key Vault 防火墙和虚拟网络](key-vault-network-security.md)了解详细的分布说明。

> [!IMPORTANT]
> 防火墙规则生效后，只在调用方请求来自允许的虚拟网络或 IPV4 地址范围时，才能执行所有 Key Vault [数据平面](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)操作。 （若要了解有关管理平面与数据平面的更多信息，请阅读[此文](../key-vault/key-vault-secure-your-key-vault.md#management-plane-access-control)。这也适用于从 Azure 门户访问密钥保管库。 虽然用户可从 Azure 门户浏览到密钥保管库，但如果其客户端计算机不在允许列表中，则可能无法列出密钥/机密/证书。 这也会影响其他 Azure 服务的“Key Vault 选取器”。 如果防火墙规则阻止了用户的客户端计算机，则用户可以查看密钥保管库列表，但不能查看列表密钥。


> [!NOTE]
> * 最多允许 127 条 VNET 规则和 127 条 IPv4 规则。 
> * 不支持使用“/31”或“/32”前缀大小的小型地址范围。 这些范围应使用单独的 IP 地址规则配置。
> * IP 网络规则仅适用于公共 IP 地址。 IP 规则不允许为专用网络保留的 IP 地址范围（如 RFC 1918 中所定义）。 专用网络包括以 *10.*\*、 *172.16.*\* 和 *192.168.*\* 开头的地址。 
> * 目前仅支持 IPv4 地址。

## <a name="trusted-services"></a>受信服务
以下是允许访问密钥保管库的受信服务列表（前提是启用了“允许受信服务”选项）。

|受信服务|使用方案|
| --- | --- |
|Azure 虚拟机部署服务|[将证书从客户托管的 Key Vault 部署到 VM](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Azure 资源管理器模板部署服务|[在部署期间传递安全值](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Azure 磁盘加密卷加密服务|允许在 VM 部署期间访问 BitLocker 密钥 (Windows VM) 或 DM 密码 (Linux VM) 和密钥加密密钥，以启用 [Azure 磁盘加密](../security/azure-security-disk-encryption.md)|
|Azure 备份|允许使用 [Azure 备份](../backup/backup-introduction-to-azure-backup.md)在 Azure VM 备份期间备份和还原相关密钥和机密|
|Exchange Online 和 SharePoint Online|允许使用[客户密钥](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697)访问服务加密的客户密钥。|
|Azure 信息保护|允许访问 [Azure 信息保护](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)的租户密钥。|
|应用服务|[通过 Key Vault 部署 Azure Web 应用证书](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|Azure SQL|[使用 Azure SQL 数据库和数据仓库的“创建自己的密钥”支持进行透明数据加密](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Azure 存储|[在 Azure Key Vault 中使用客户托管密钥进行存储服务加密](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Azure Data Lake Store|[在 Azure Data Lake Store 中使用客户托管密钥进行数据加密](../data-lake-store/data-lake-store-encryption.md)|



> [!NOTE]
> 相关密钥保管库访问策略必须设置为允许相应的服务访问密钥保管库。

## <a name="next-steps"></a>后续步骤

* [保护密钥保管库](key-vault-secure-your-key-vault.md)
* [配置 Azure Key Vault 防火墙和虚拟网络](key-vault-network-security.md)
