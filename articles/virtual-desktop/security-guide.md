---
title: Windows 虚拟桌面安全性最佳做法-Azure
description: 保持 Windows 虚拟桌面环境安全的最佳做法。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5330c427088734b049b2cb4f7735ac0099a52b47
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966670"
---
# <a name="security-best-practices"></a>安全最佳做法

Windows 虚拟桌面是一种托管虚拟桌面服务，其中包括用于保护组织安全的许多安全功能。 在 Windows 虚拟桌面部署中，Microsoft 代表客户管理部分服务。 此服务具有许多内置的高级安全功能，如 "反向连接"，这降低了从任意位置访问远程桌面所涉及的风险。

本文介绍了可作为管理员使用以保持客户的 Windows 虚拟桌面部署安全所需的其他步骤。

## <a name="security-responsibilities"></a>安全责任

不同于传统的本地虚拟桌面基础结构（VDIs）的云服务是如何处理安全责任的。 例如，在传统的本地 VDI 中，客户将负责安全的各个方面。 但在大多数云服务中，这些责任在客户和公司之间共享。

使用 Windows 虚拟桌面时，务必要了解的是，尽管某些组件已经为你的环境提供了保护，但你需要自行配置其他区域以满足你组织的安全需求。

下面是你在 Windows 虚拟桌面部署中所需的安全要求：

| 安全需求 | 客户负责这一情况吗？ |
|---------------|:-------------------------:|
|标识|是|
|用户设备（移动设备和 PC）|是|
|应用安全性|是|
|会话主机操作系统|是|
|部署配置|是|
|网络控制措施|是|
|虚拟化控制平面|否|
|物理主机|否|
|物理网络|否|
|物理数据中心|否|

客户不负责的安全需要由 Microsoft 处理。

## <a name="azure-security-best-practices"></a>Azure 安全最佳实践

Windows 虚拟桌面是 Azure 中的一种服务。 若要最大程度地提高 Windows 虚拟桌面部署的安全性，应确保同时保护周围的 Azure 基础结构和管理平面。 若要保护你的基础结构，请考虑 Windows 虚拟桌面如何适应更大的 Azure 生态系统。 若要了解有关 Azure 生态系统的详细信息，请参阅[azure 安全最佳实践和模式](../security/fundamentals/best-practices-and-patterns.md)。

本部分介绍保护 Azure 生态系统的最佳实践。

### <a name="enable-azure-security-center"></a>启用 Azure 安全中心

建议为订阅、虚拟机、密钥保管库和存储帐户启用 Azure 安全中心标准。

通过 Azure 安全中心标准版，你可以：

* 管理漏洞。
* 评估与 PCI 等常见框架的符合性。
* 增强环境的整体安全性。

若要了解详细信息，请参阅将[Azure 订阅加入安全中心标准](../security-center/security-center-get-started.md)。

### <a name="improve-your-secure-score"></a>提高安全分数

安全评分提供建议和最佳做法建议，以提高总体安全性。 这些建议的优先顺序可帮助您选择最重要的建议，快速修复选项可帮助您快速解决潜在漏洞。 这些建议还会随着时间的推移进行更新，让你保持最新状态，以便保持你的环境安全。 若要了解详细信息，请参阅[在 Azure 安全中心提高安全分数](../security-center/security-center-secure-score.md)。

## <a name="windows-virtual-desktop-security-best-practices"></a>Windows 虚拟桌面安全性最佳做法

Windows 虚拟桌面具有许多内置的安全控件。 在本部分中，你将了解可用于保护用户和数据安全的安全控件。

### <a name="require-multi-factor-authentication"></a>需要多重身份验证

对于 Windows 虚拟桌面中的所有用户和管理员都需要多重身份验证，可以提高整个部署的安全性。 若要了解详细信息，请参阅[为 Windows 虚拟桌面启用 Azure 多重身份验证](set-up-mfa.md)。

### <a name="enable-conditional-access"></a>启用条件性访问

启用[条件性访问](../active-directory/conditional-access/best-practices.md)可让你在向用户授予对 Windows 虚拟桌面环境的访问权限之前管理风险。 在决定要将访问权限授予哪些用户时，我们建议你还考虑用户是谁、他们登录的方式以及他们使用的设备。

### <a name="collect-audit-logs"></a>收集审核日志

启用审核日志收集可查看与 Windows 虚拟桌面相关的用户和管理活动。 重要审核日志的一些示例如下：

-   [Azure 活动日志](../azure-monitor/platform/activity-log-collect.md)
-   [Azure Active Directory 活动日志](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [会话主机](../azure-monitor/platform/agent-windows.md)
-   [Windows 虚拟桌面诊断日志](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault 日志](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>使用 RemoteApps

选择部署模型时，可以为远程用户提供对整个虚拟桌面的访问权限，也可以仅选择 "应用程序"。 远程应用程序（或 RemoteApps）提供无缝体验，因为用户可以在其虚拟桌面上使用应用。 RemoteApps 仅让用户使用应用程序所公开的一小部分远程计算机来降低风险。

### <a name="monitor-usage-with-azure-monitor"></a>使用 Azure Monitor 监视使用情况

使用[Azure Monitor](https://azure.microsoft.com/services/monitor/)监视 Windows 虚拟桌面服务的使用情况和可用性。 考虑创建 Windows 虚拟桌面服务的[服务运行状况警报](../service-health/alerts-activity-log-service-notifications.md)，以便在出现服务影响事件时接收通知。

## <a name="session-host-security-best-practices"></a>会话主机安全最佳做法

会话主机是在 Azure 订阅和虚拟网络中运行的虚拟机。 Windows 虚拟桌面部署的总体安全性取决于你在会话主机上进行的安全控制。 本部分介绍使你的会话托管安全的最佳做法。

### <a name="enable-endpoint-protection"></a>启用 endpoint protection

为了保护你的部署免受已知的恶意软件的攻击，我们建议在所有会话主机上启用 endpoint protection。 可以使用 Windows Defender 防病毒或第三方程序。 若要了解详细信息，请参阅[VDI 环境中 Windows Defender 防病毒的部署指南](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus)。 

对于 FSLogix 等配置文件解决方案或装载 VHD 文件的其他解决方案，建议排除 VHD 文件扩展名。

### <a name="install-an-endpoint-detection-and-response-product"></a>安装终结点检测和响应产品

建议安装端点检测和响应（EDR）产品来提供高级检测和响应功能。 对于启用了[Azure 安全中心](../security-center/security-center-services.md)的服务器操作系统，安装 EDR 产品将部署 Defender ATP。 对于客户端操作系统，可以将[DEFENDER ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding)或第三方产品部署到这些终结点。

### <a name="enable-threat-and-vulnerability-management-assessments"></a>启用威胁和漏洞管理评估

确定操作系统和应用程序中存在的软件漏洞对于保持您的环境安全至关重要。 Azure 安全中心可帮助你通过服务器操作系统的漏洞评估来确定问题点。 你还可以使用 Defender ATP，为桌面操作系统提供威胁和漏洞管理。 你还可以使用第三方产品，因为我们建议使用 Azure 安全中心和 Defender ATP。

### <a name="patch-software-vulnerabilities-in-your-environment"></a>修补环境中的软件漏洞

确定漏洞后，你必须对其进行修补。 这也适用于虚拟环境，其中包括正在运行的操作系统、在其中部署的应用程序，以及从中创建新计算机的映像。 遵循供应商修补通知通信并及时应用修补程序。 建议每月修补基本映像，以确保新部署的计算机尽可能安全。

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>建立最大非活动时间和断开连接策略

当用户处于非活动状态时将其注销会保留资源并防止未经授权的用户进行访问。 我们建议对用户生产力和资源使用情况进行超时平衡。 对于与无状态应用程序交互的用户，请考虑更严格的策略，这些策略会关闭计算机并保留资源。 断开长时间运行的应用程序（如果用户处于空闲状态，例如模拟或 CAD 呈现）会中断用户的工作，甚至可能需要重新启动计算机。

### <a name="set-up-screen-locks-for-idle-sessions"></a>设置空闲会话的屏幕锁定

你可以通过将 Windows 虚拟桌面配置为在空闲时锁定计算机屏幕并要求身份验证对其进行解锁，来防止不必要的系统访问。

### <a name="establish-tiered-admin-access"></a>建立分层管理访问权限

建议你不要向用户授予对虚拟桌面的管理员访问权限。 如果需要软件包，我们建议你通过 Microsoft 终结点管理器等配置管理实用程序使其可供使用。 在多会话环境下，建议不要让用户直接安装软件。

### <a name="consider-which-users-should-access-which-resources"></a>考虑哪些用户应该访问哪些资源

将会话主机视为现有桌面部署的扩展。 建议使用与环境中的其他桌面相同的方式（如使用网络分段和筛选）来控制对网络资源的访问。 默认情况下，会话主机可以连接到 internet 上的任何资源。 可以通过多种方式来限制流量，包括使用 Azure 防火墙、网络虚拟设备或代理。 如果需要限制流量，请确保添加适当的规则，以便 Windows 虚拟桌面可以正常工作。

### <a name="manage-office-pro-plus-security"></a>管理 Office Pro Plus 安全性

除了保护会话主机以外，还必须确保在其中运行的应用程序的安全。 Office Pro Plus 是在会话主机中部署的最常见的应用程序之一。 为了改进 Office 部署安全性，我们建议你使用[安全策略顾问](/DeployOffice/overview-of-security-policy-advisor)来实现适用于企业的 Microsoft 365 应用。 此工具标识可应用于部署以提高安全性的策略。 安全策略顾问还根据其对安全和工作效率的影响，推荐策略。

### <a name="other-security-tips-for-session-hosts"></a>会话主机的其他安全提示

通过限制操作系统功能，可以增强会话主机的安全性。 下面是一些可以执行的操作：

- 在远程桌面会话中，通过将驱动器、打印机和 USB 设备重定向到用户的本地设备来控制设备重定向。 建议你评估安全要求，并检查是否应禁用这些功能。

- 通过隐藏本地和远程驱动器映射来限制 Windows 资源管理器访问。 这可以防止用户发现有关系统配置和用户的不需要的信息。

- 避免直接对环境中的会话主机进行 RDP 访问。 如果需要直接访问 RDP 以便进行管理或进行故障排除，[请启用实时](../security-center/security-center-just-in-time.md)访问以限制会话主机上的潜在攻击面。

- 授予用户访问本地和远程文件系统时的有限权限。 可以通过确保本地和远程文件系统使用具有最低权限的访问控制列表来限制权限。 这样，用户只能访问他们需要的内容，而不能更改或删除关键资源。

- 防止不需要的软件在会话主机上运行。 可以启用应用保险箱以提高会话主机的安全性，确保只有允许的应用可在主机上运行。

## <a name="next-steps"></a>后续步骤

若要了解如何启用多重身份验证，请参阅[设置多重身份验证](set-up-mfa.md)。