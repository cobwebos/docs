---
title: 隔离网络上的 Azure 磁盘加密
description: 本文提供了有关 Microsoft Azure Linux Vm 的磁盘加密的故障排除提示。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: aa0dc204a017e2d40eb3952a9ede0755127f8de2
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970659"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>隔离网络上的 Azure 磁盘加密

如果连接受到防火墙、代理要求或网络安全组 (NSG) 设置的限制，扩展执行所需任务的能力可能会受到干扰。 此干扰可能会导致出现类似于“VM 上未提供扩展状态”的状态消息。

## <a name="package-management"></a>包管理

Azure 磁盘加密取决于多个组件，这些组件通常作为启用了 ADE 的一部分进行安装（如果尚未存在）。 在防火墙之后或在其他情况下与 Internet 隔离时，必须预先安装这些包或在本地使用它们。

下面是每个分发所需的包。 有关支持的发行版和卷类型的完整列表，请参阅[支持的 vm 和操作系统](disk-encryption-overview.md#supported-vms-and-operating-systems)。

- **Ubuntu 14.04、16.04、18.04**： lsscsi、psmisc、at、cryptsetup-bin、python-parted、python-6、procps
- **CentOS 7.2-7.7**： lsscsi，psmisc，lvm2，uuid，at，patch，cryptsetup，cryptsetup-重新加密，pyparted，procps，util-linux
- **CentOS 6.8**： lsscsi，psmisc，lvm2，uuid，at，cryptsetup-重新加密，pyparted，python-6
- **RedHat 7.2-7.7**： lsscsi，psmisc，lvm2，uuid，at，patch，cryptsetup，cryptsetup-重新加密，procps-ng，util-linux
- **RedHat 6.8**： lsscsi，psmisc，lvm2，uuid，at，patch，cryptsetup-重新加密
- **openSUSE 42.3，SLES 12-SP4，12-SP3**： lsscsi，cryptsetup

在 Red Hat 上，如果需要代理，则必须确保正确设置订阅管理器和 yum。 有关详细信息，请参阅[如何排除有关订阅管理器和 yum 的问题](https://access.redhat.com/solutions/189533)。  

手动安装包时，还必须在发布新版本时手动升级包。

## <a name="network-security-groups"></a>网络安全组
应用的任何网络安全组设置仍必须允许终结点满足磁盘加密的已记录的网络配置先决条件。  请参阅[Azure 磁盘加密：网络要求](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure 磁盘加密与 Azure AD （以前的版本）

如果[对 Azure AD （以前的版本）使用 Azure 磁盘加密](disk-encryption-overview-aad.md)，则需要为所有发行版手动安装[Azure Active Directory 库](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)（除了[上面列出](#package-management)的适用于发行版的包）。

使用 [Azure AD 凭据](disk-encryption-linux-aad.md)启用加密时，目标 VM 必须允许连接到 Azure Active Directory 终结点和密钥保管库终结点。 当前 Azure Active Directory 身份验证终结点在 [Office 365 URL 和 IP 地址范围](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges)文档中的第 56 和 59 节中进行维护。 在有关如何[访问防火墙保护下的 Azure 密钥保管库](../../key-vault/key-vault-access-behind-firewall.md)的文档中提供了密钥保管库说明。

### <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务 

虚拟机必须能够访问[Azure 实例元数据服务](instance-metadata-service.md)终结点，该终结点使用众所周知的不可路由的 IP 地址（`169.254.169.254`），该地址只能从 VM 内部访问。  不支持将本地 HTTP 流量更改为此地址的代理配置（例如，添加 X 转发的标头）。

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 磁盘加密故障排除](disk-encryption-troubleshooting.md)的更多步骤
- [Azure 静态数据加密](../../security/fundamentals/encryption-atrest.md)
