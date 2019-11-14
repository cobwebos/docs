---
title: include 文件
description: include 文件
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: d0ede337f2ae17f28cd02df7ed35919bd9955e7a
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048567"
---
本文包含有关 Azure 虚拟机的安全建议。 按照这些建议来帮助满足我们的共享责任模型中所述的安全义务。 这些建议还有助于提高 web 应用解决方案的整体安全性。 若要详细了解 Microsoft 如何满足服务提供商的责任，请参阅[云计算的共享责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

本文的某些建议可以由 Azure 安全中心自动解决。 Azure 安全中心是 Azure 中资源的第一道防线。 它会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后，建议如何解决漏洞。 有关详细信息，请参阅[Azure 安全中心中的安全建议](../articles/security-center/security-center-recommendations.md)。

有关 Azure 安全中心的常规信息，请参阅[什么是 Azure 安全中心？](../articles/security-center/security-center-intro.md)。

## <a name="general"></a>一般信息

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 构建自定义 VM 映像时，应用最新的更新。 | 在创建映像之前，请为操作系统以及将作为映像一部分的所有应用程序安装最新的更新。  | - |
| 使 VM 保持最新。 | 可以使用 Azure 自动化中的[更新管理](../articles/automation/automation-update-management.md)解决方案来管理 azure 中的 Windows 和 Linux 计算机的操作系统更新。 | [是](../articles/security-center/security-center-apply-system-updates.md) |
| 备份 Vm。 | [Azure 备份](../articles/backup/backup-overview.md)可帮助保护应用程序数据，并且具有最低的操作成本。 应用程序错误可能会损坏数据，人为错误可能会将 bug 引入应用程序。 Azure 备份可保护运行 Windows 和 Linux 的 Vm。 | - |
| 使用多个 Vm 以提高恢复能力和可用性。 | 如果 VM 运行必须高度可用的应用程序，请使用多个 Vm 或[可用性集](../articles/virtual-machines/windows/manage-availability.md)。 | - |
| 采用业务连续性和灾难恢复（BCDR）策略。 | Azure Site Recovery 使你可以从为支持业务连续性而设计的不同选项中进行选择。 它支持不同的复制和故障转移方案。 有关详细信息，请参阅[关于 Site Recovery](../articles/site-recovery/site-recovery-overview.md)。 | - |

## <a name="data-security"></a>数据安全

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 加密操作系统磁盘。 | [Azure 磁盘加密](../articles/security/azure-security-disk-encryption-overview.md)有助于加密 Windows 和 LINUX IaaS VM 磁盘。 如果没有必要的密钥，则无法读取加密磁盘的内容。 磁盘加密保护存储的数据免受未经授权的访问，如果磁盘已被复制，则可能会出现这种情况。| [是](../articles/security-center/security-center-apply-disk-encryption.md) |
| 对数据磁盘进行加密。 | [Azure 磁盘加密](../articles/security/azure-security-disk-encryption-overview.md)有助于加密 Windows 和 LINUX IaaS VM 磁盘。 如果没有必要的密钥，则无法读取加密磁盘的内容。 磁盘加密保护存储的数据免受未经授权的访问，如果磁盘已被复制，则可能会出现这种情况。| -  |
| 限制安装的软件。 | 将安装的软件限制为成功应用解决方案所需的软件。 此准则有助于降低解决方案的受攻击面。 | - |
| 使用防病毒或反恶意软件。 | 在 Azure 中，可以使用安全供应商（例如 Microsoft、Symantec、走向微和 Kaspersky 等）的反恶意软件。 此软件可帮助保护 Vm 免受恶意文件、广告软件和其他威胁的侵害。 你可以根据应用程序工作负荷部署 Microsoft 反恶意软件。 使用基本的默认安全配置或高级自定义配置。 有关详细信息，请参阅[适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](../articles/security/azure-security-antimalware.md)。 | - |
| 安全地存储密钥和机密。 | 通过向应用程序所有者提供安全的集中管理的选项，简化机密和密钥的管理。 此管理可降低意外泄露或泄露的风险。 Azure Key Vault 可以安全地将密钥存储在已通过 FIPS 140-2 级别2认证的硬件安全模块（Hsm）中。 如果需要使用 FIPs 140.2 级别3来存储密钥和机密，可以使用[Azure 专用 HSM](../articles/dedicated-hsm/overview.md)。 | - |

## <a name="identity-and-access-management"></a>标识和访问管理 

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 集中 VM 身份验证。 | 你可以使用[Azure Active Directory 身份验证](../articles/active-directory/develop/authentication-scenarios.md)来集中 Windows 和 Linux vm 的身份验证。 | - |

## <a name="monitoring"></a>监视

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 监视你的 Vm。 | 你可以使用[用于 VM 的 Azure Monitor](../articles/azure-monitor/insights/vminsights-overview.md)来监视 Azure vm 和虚拟机规模集的状态。 VM 性能问题可能会导致服务中断，从而违反可用性安全原则。 | - |

## <a name="networking"></a>联网

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 限制对管理端口的访问。 | 攻击者会扫描公共云 IP 范围中的开放管理端口，并尝试 "简单" 的攻击（例如常见密码）和已知的未修补漏洞。 你可以使用[实时（JIT） VM 访问](../articles/security-center/security-center-just-in-time.md)锁定到 Azure vm 的入站流量，降低攻击的暴露程度，同时在需要时轻松连接到 vm。 | - |
| 限制网络访问。 | 可以通过网络安全组限制网络访问并控制公开的终结点数。 有关详细信息，请参阅[创建、更改或删除网络安全组](../articles/virtual-network/manage-network-security-group.md)。 | - |

## <a name="next-steps"></a>后续步骤

请与应用程序提供商联系，了解其他安全要求。 有关开发安全应用程序的详细信息，请参阅[安全开发文档](../articles/security/fundamentals/abstract-develop-secure-apps.md)。
