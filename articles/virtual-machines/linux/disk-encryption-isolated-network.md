---
title: 隔离网络上的 Azure 磁盘加密
description: 本文介绍 Linux Vm 上 Microsoft Azure 磁盘加密的故障排除提示。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 1157a18954c30e1f1d93aa33dbea6104491b5353
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396445"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>隔离网络上的 Azure 磁盘加密

如果连接受到防火墙、代理要求或网络安全组 (NSG) 设置的限制，扩展执行所需任务的能力可能会受到干扰。 此干扰可能会导致出现类似于“VM 上未提供扩展状态”的状态消息。

## <a name="package-management"></a>包管理

Azure 磁盘加密取决于多个组件，这些组件通常在启用 ADE 的过程中安装（如果这些组件尚不存在）。 位于防火墙之后或在其他情况下与 Internet 隔离时，这些包必须预先安装或在本地提供。

下面是每次发行所需的包。 有关支持的发行版和卷类型的完整列表，请参阅[支持的 VM 和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)。

- **Ubuntu 14.04、16.04、18.04**：lsscsi、psmisc、at、cryptsetup-bin、python-parted、python-six、procps
- **CentOS 7.2 - 7.7**：lsscsi、psmisc、lvm2、uuid、at、patch、cryptsetup、cryptsetup-reencrypt、pyparted、procps-ng、util-linux
- **CentOS 6.8**：lsscsi、psmisc、lvm2、uuid、at、cryptsetup-reencrypt、pyparted、python-six
- **RedHat 7.2-7.7**： lsscsi，psmisc，lvm2，uuid，at，patch，cryptsetup，cryptsetup-重新加密，procps-ng，util-linux
- **RedHat 6.8**： lsscsi，psmisc，lvm2，uuid，at，patch，cryptsetup-重新加密
- **openSUSE 42.3，SLES 12-SP4，12-SP3**： lsscsi，cryptsetup

在 Red Hat 上，如果需要代理，则必须确保正确设置订阅管理器和 yum。 有关详细信息，请参阅[如何排除有关订阅管理器和 yum 的问题](https://access.redhat.com/solutions/189533)。  

手动安装包时，还必须在发布新版本时手动升级包。

## <a name="network-security-groups"></a>网络安全组
应用的任何网络安全组设置仍必须允许终结点满足所述的与磁盘加密相关的网络配置先决条件。  请参阅 [Azure 磁盘加密：网络要求](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure 磁盘加密与 Azure AD (早期版本) 

如果将 [Azure 磁盘加密与 (早期版本) 的 Azure AD 一起 ](disk-encryption-overview-aad.md)使用，则需要为所有发行版 (手动安装 [Azure Active Directory 库](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) ，如 [上面所列](#package-management)) 所列。

使用 [Azure AD 凭据](disk-encryption-linux-aad.md)启用加密时，目标 VM 必须允许连接到 Azure Active Directory 终结点和密钥保管库终结点。 当前 Azure Active Directory 身份验证终结点保留在 [Microsoft 365 url 和 IP 地址范围](/microsoft-365/enterprise/urls-and-ip-address-ranges) 文档的56和59部分中。 在有关如何[访问防火墙保护下的 Azure 密钥保管库](../../key-vault/general/access-behind-firewall.md)的文档中提供了密钥保管库说明。

### <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务 

虚拟机必须能够访问 [Azure 实例元数据服务](instance-metadata-service.md) 终结点，该终结点使用众所周知的不可路由的 IP 地址 (`169.254.169.254`) ，该地址只能从 VM 内部访问。  不支持将本地 HTTP 流量更改为此地址的代理配置（例如，添加 X-Forwarded-For 标头）。

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 磁盘加密故障排除](disk-encryption-troubleshooting.md)的更多步骤
- [Azure 静态数据加密](../../security/fundamentals/encryption-atrest.md)
