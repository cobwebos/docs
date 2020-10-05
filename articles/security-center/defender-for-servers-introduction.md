---
title: 用于存储的 Azure Defender - 优点和功能
description: 了解适用于服务器的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8757399329f3a9bd9f4d7b914b12b2a0f7e85603
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448284"
---
# <a name="introduction-to-azure-defender-for-servers"></a>适用于服务器的 Azure Defender 简介

适用于服务器的 Azure Defender 添加了适用于 Windows 和 Linux 计算机的威胁检测和高级防护功能。

针对 Windows，Azure Defender 与 Azure 服务集成，可以监视和保护基于 Windows 的计算机。 安全中心以易用的格式提供来自所有这些服务的警报和修正建议。

安全中心使用 **auditd**（最常见的 Linux 审核框架之一）从 Linux 计算机收集审核记录。 auditd 驻留在主线内核中。 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>适用于服务器的 Azure Defender 有哪些优点？

适用于服务器的 Azure Defender 提供的威胁检测和保护功能包括：

- **VM 漏洞评估扫描** - Azure 安全中心随附的漏洞扫描程序由 Qualys 提供支持。 

    Qualys 的扫描程序是一种用于实时识别 Azure 虚拟机中的漏洞的领先工具。 你无需具备 Qualys 许可证，甚至还不需要 Qualys 帐户 - 所有操作都在安全中心内无缝执行。 [了解详细信息](deploy-vulnerability-assessment-vm.md)。

- **实时 (JIT) VM 访问** - 威胁制造者会主动搜寻设有开放管理端口（如 RDP 或 SSH）的可访问计算机。 你的所有虚拟机都是潜在的攻击目标。 VM 在被成功入侵后将会用作进一步攻击环境中资源的入口点。

    启用适用于服务器的 Azure Defender 时，可以使用实时 VM 访问功能来锁定发往 VM 的入站流量，降低遭受攻击的可能性，同时在需要时还允许轻松连接到 VM。 [了解详细信息](just-in-time-explained.md)。

- **文件完整性监视 (FIM)** - 文件完整性监视 (FIM) 也称为更改监视，它可以检查操作系统、应用程序软件和其他组件的文件和注册表，确定它们是否发生了可能表示遭受攻击的更改。 将使用比较方法来确定当前文件状态是否不同于上次扫描该文件时的状态。 可以利用这种比较来确定文件是否发生了有效或可疑的修改。

    启用适用于服务器的 Azure Defender 时，可以使用 FIM 来验证 Windows 文件、Windows 注册表和 Linux 文件的完整性。 [了解详细信息](security-center-file-integrity-monitoring.md)。

- **自适应应用程序控制 (AAC)** - 自适应应用程序控制是一种智能和自动化的解决方案，用于为计算机定义已知安全的应用程序的允许列表。

    当启用并配置了自适应应用程序控件后，如果有任何程序运行，而该程序不是你定义的安全应用程序，你会收到安全警报。 [了解详细信息](security-center-adaptive-application.md)。

- **自适应网络强化 (ANH)** - 应用网络安全组 (NSG) 来筛选往返资源的流量，改善网络安全状况。 但是，仍然可能存在一些这样的情况：通过 NSG 流动的实际流量是所定义 NSG 规则的子集。 在这些情况下，可以根据实际流量模式强化 NSG 规则，从而进一步改善安全状况。

    自适应网络强化为进一步强化 NSG 规则提供了建议。 它使用机器学习算法，这种算法会将实际流量、已知受信任的配置、威胁情报和其他泄露标志都考虑在内，然后提供仅允许来自特定 IP/端口元组的流量的建议。 [了解详细信息](security-center-adaptive-network-hardening.md)。

- **与 Microsoft Defender 高级威胁防护 (ATP) 集成（仅限 Windows）** - Azure Defender 与 Microsoft Defender 高级威胁防护 (ATP) 集成。 两者共同提供全面的终结点检测和响应 (EDR) 功能。 [了解详细信息](security-center-wdatp.md)。

    > [!IMPORTANT]
    > 使用安全中心的 Windows 服务器上已自动启用 Microsoft Defender ATP 传感器。

    Microsoft Defender ATP 在检测到威胁时会触发警报。 警报在安全中心显示。 在安全中心中，还可以透视 Microsoft Defender ATP 控制台，并执行详细调查来发现攻击范围。 有关 Microsoft Defender ATP 的详细信息，请参阅[将服务器加入 Microsoft Defender ATP 服务](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

- **Docker 主机强化** - Azure 安全中心会标识 IaaS Linux VM 上或运行 Docker 容器的其他 Linux 计算机上承载的非托管容器。 安全中心会持续评估这些容器的配置。 然后，它会将其与 Internet 安全中心 (CIS) 的 Docker 基准进行比较。 安全中心包含 CIS 的 Docker 基准的完整规则集，并会在容器不符合控件标准的情况下发出警报。 [了解详细信息](harden-docker-hosts.md)。

- **无文件攻击检测（仅限 Windows）** - 无文件攻击会试图通过将恶意有效负载注入内存中来避免被基于磁盘的扫描技术检测到。 之后，攻击者的有效负载会持久保存在遭到入侵的进程的内存中，执行各种恶意活动。

  自动内存取证技术使用无文件攻击检测来识别无文件攻击工具包、方法和行为。 此解决方案会定期在运行时扫描计算机，并直接从进程的内存中提取见解。 特定见解包括对以下内容的识别： 

  - 常见工具包和加密挖掘软件 

  - Shellcode，这是一小段代码，通常用作利用软件漏洞时的有效负载。

  - 进程内存中注入的恶意可执行文件

  无文件攻击检测功能会生成详细的安全警报，其中包含相关描述和其他进程元数据，例如网络活动数据。 这可以加快警报会审、关联和下游响应时间。 此方法是对基于事件的 EDR 解决方案的补充，并扩大了检测范围。

  有关无文件攻击检测警报的详细信息，请参阅[警报参考表](alerts-reference.md#alerts-windows)。

- **Linux auditd 警报和 Log Analytics 代理集成（仅 Linux）** - auditd 系统包含一个负责监视系统调用的内核级子系统。 该子系统会按照指定的规则集筛选这些调用，并将针对这些调用生成的消息写入到套接字。 安全中心在 Log Analytics 代理中集成了 auditd 包的功能。 通过这种集成，无需满足任何先决条件，就能在所有受支持的 Linux 发行版中收集 auditd 事件。

    可以使用适用于 Linux 的 Log Analytics 代理收集、扩充 auditd 记录并将其聚合到事件中。 安全中心会持续添加新分析功能，这些功能可以使用 Linux 信号来检测云和本地 Linux 计算机上的恶意行为。 类似于 Windows 中的功能，这些分析功能可以检测各种可疑进程、可疑登录企图、内核模块加载操作和其他活动。 这些活动可能表示计算机正在受到攻击或已遭入侵。  

    有关 Linux 警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-linux)。


## <a name="simulating-alerts"></a>模拟警报

可以通过下载以下行动手册之一来模拟警报：

- 对于 Windows：[Azure 安全中心 Playbook：安全警报](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- 对于 Linux：[Azure 安全中心 Playbook：Linux 检测](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf)来模拟 Linux 警报。




## <a name="next-steps"></a>后续步骤

本文介绍了适用于服务器的 Azure Defender。 

如需相关材料，请参阅以下文章： 

- 无论警报是由安全中心生成，还是由安全中心从其他安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。

- > [!div class="nextstepaction"]
    > [启用 Azure Defender](security-center-pricing.md)