---
title: include 文件
description: include 文件
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71163938"
---
本文包含有关 Azure 虚拟机的安全建议。 实施这些建议将有助于你履行我们的共享职责模型中描述的安全职责，并改进 Web 应用解决方案的总体安全性。 若要详细了解 Microsoft 如何满足服务提供商的责任，请阅读[云计算的共享责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)。

Azure 安全中心会自动监视本文中包含的一些建议。 Azure 安全中心是保护 Azure 中资源的第一道防线。 它会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向您提供有关如何解决这些问题的建议。

- 有关 Azure 安全中心建议的详细信息，请参阅[Azure 安全中心中的安全建议](../articles/security-center/security-center-recommendations.md)。
- 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../articles/security-center/security-center-intro.md)

## <a name="recommendations"></a>建议

| 类别 | 建议 | 注释 | 安全中心 |
|-|-|----|--|
| 常规 | 构建自定义 VM 映像时，请使用最新更新 | 在创建映像之前，请确保已为操作系统以及将作为映像一部分的所有应用程序安装了所有最新的更新。  | - |
| 常规 | 使 Vm 保持最新 | 可以使用 Azure 自动化中的[更新管理](../articles/automation/automation-update-management.md)解决方案来管理 azure 中的 Windows 和 Linux 计算机的操作系统更新 | [是](../articles/security-center/security-center-apply-system-updates.md) |
| 常规 | 备份虚拟机 | [Azure 备份](../articles/backup/backup-overview.md)可帮助保护应用程序数据，但操作成本最低。 应用程序错误可能损坏数据，人为错误可能将 bug 引入应用程序。 借助 Azure 备份，可以保护运行 Windows 和 Linux 的虚拟机。 | - |
| 常规 | 使用多个 Vm 以提高恢复能力和可用性 | 如果 VM 运行需要具有高可用性的应用程序，则应使用多个 Vm 或[可用性集](../articles/virtual-machines/windows/manage-availability.md) | - |
| 常规 | 采用业务连续性和灾难恢复（BCDR）策略 | Azure Site Recovery 使你可以从为支持业务连续性需求而设计的不同选项中进行选择。 它支持不同的复制和故障转移方案。 有关详细信息，请参阅[关于 Site Recovery](../articles/site-recovery/site-recovery-overview.md) | - |
| 标识和访问管理 | 集中 VM 身份验证 | 你可以使用[Azure AD 身份验证](../articles/active-directory/develop/authentication-scenarios.md)来集中 Windows 和 Linux 虚拟机的身份验证。 | - |
| 数据安全 | 加密操作系统磁盘 | [Azure 磁盘加密](../articles/security/azure-security-disk-encryption-overview.md)用于加密 Windows 和 Linux IaaS 虚拟机磁盘。 加密磁盘使内容无法读取，无需密钥。 磁盘加密保护存储的数据免受未经授权的访问，如果磁盘已被复制，则可能会出现这种情况| [是](../articles/security-center/security-center-apply-disk-encryption.md) |
| 数据安全 | 加密数据磁盘 | [Azure 磁盘加密](../articles/security/azure-security-disk-encryption-overview.md)用于加密 Windows 和 Linux IaaS 虚拟机磁盘。 加密磁盘使内容无法读取，无需密钥。 磁盘加密保护存储的数据免受未经授权的访问，如果磁盘已被复制，则可能会出现这种情况| -  |
| 数据安全 | 限制安装的软件 | 将安装的软件限制为成功实现解决方案所需的功能有助于降低解决方案的受攻击面 | - |
| 数据安全 | 使用防病毒/反恶意软件 | 在 Azure 中，可以使用安全供应商（例如 Microsoft、Symantec、走向微和 Kaspersky 等）的反恶意软件。 此软件可帮助保护虚拟机免受恶意文件、广告程序和其他威胁的侵害。 你可以根据应用程序工作负荷部署 Microsoft 反恶意软件保护，其中包含基本的默认安全或高级自定义配置。 有关适用于 Azure 的 Microsoft 反恶意软件的详细信息，请参阅[适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](../articles/security/azure-security-antimalware.md) | - |
| 数据安全 | 安全存储密钥和机密 | 通过使用安全的集中管理选项提供应用程序所有者来简化机密和密钥的管理，从而降低意外泄露或泄露的风险。 Azure Key Vault 可以将密钥安全存储在已通过 FIPS 140-2 级别2认证的硬件安全模块（Hsm）中。 如果需要使用 FIPs 140.2 级别3存储密钥和机密，可以使用[Azure 专用 HSM](../articles/dedicated-hsm/overview.md) | - |
| 网络 | 限制对管理端口的访问 | 攻击者会扫描公共云 IP 范围中的开放管理端口，并尝试 "简单" 的攻击（例如常见密码）和已知的未修补漏洞。 实时[（JIT）虚拟机（VM）访问](../articles/security-center/security-center-just-in-time.md)可用于锁定到 Azure vm 的入站流量，降低攻击的风险，同时提供在需要时轻松连接到 vm 的权限。 | - |
| 网络 | 限制网络访问 | 可以通过网络安全组限制网络访问并控制公开的终结点数。 有关详细信息，请参阅[创建、更改或删除网络安全组](../articles/virtual-network/manage-network-security-group.md) | - |
| 监视 | 监视虚拟机 | 可以使用[azure monitor For vm](../articles/azure-monitor/insights/vminsights-overview.md)来监视 Azure 虚拟机和虚拟机规模集的状态。 VM 性能问题可能会导致服务中断，从而违反可用性安全原则。 | - |

## <a name="next-steps"></a>后续步骤

请咨询应用程序提供商，看是否有其他安全要求。 有关开发安全应用程序的详细信息，请参阅[安全开发文档](../articles/security/fundamentals/abstract-develop-secure-apps.md)。