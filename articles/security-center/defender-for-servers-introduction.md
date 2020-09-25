---
title: 适用于服务器的 Azure Defender-优点和功能
description: 了解适用于服务器的 Azure Defender 的优势和功能。
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d0e75ab8a2e4d853fa2edc66174502dd3a7930e9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301663"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Azure Defender for server 简介

适用于服务器的 Azure Defender 为 Windows 和 Linux 计算机添加威胁检测和高级防御功能。

对于 Windows，Azure Defender 与 Azure 服务集成，以监视和保护基于 Windows 的计算机。 安全中心以易用的格式提供来自所有这些服务的警报和修正建议。

对于 Linux，Azure Defender 通过使用 **审核**（一个最常见的 linux 审核框架）从 Linux 计算机收集审核记录。 auditd 驻留在主线内核中。 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Azure Defender 对于服务器有哪些好处？

Azure Defender for server 提供的威胁检测和保护功能包括：

- **Vm 的漏洞评估扫描** -Azure 安全中心随附的漏洞扫描程序由 Qualys 提供支持。 

    Qualys "扫描程序" 是在 Azure 虚拟机中实时识别漏洞的一项领先工具。 你无需具备 Qualys 许可证，甚至还不需要 Qualys 帐户 - 所有操作都在安全中心内无缝执行。 [了解详细信息](deploy-vulnerability-assessment-vm.md)。

- 实时** (JIT) VM 访问**-威胁参与者主动使用打开的管理端口（如 RDP 或 SSH）寻找可访问的计算机。 你的所有虚拟机都是潜在的攻击目标。 VM 在被成功入侵后将会用作进一步攻击环境中资源的入口点。

    为服务器启用 Azure Defender 时，可以使用实时 VM 访问权限，以将入站流量锁定到 Vm，降低遭受攻击的可能性，同时提供轻松访问 Vm （如果需要）。 [了解详细信息](just-in-time-explained.md)。

- **文件完整性监视 (fim) ** 文件完整性监视 (fim) ，也称为更改监视，检查操作系统、应用程序软件的文件和注册表，以及其他可能指示攻击的更改。 将使用比较方法来确定当前文件状态是否不同于上次扫描该文件时的状态。 可以利用这种比较来确定文件是否发生了有效或可疑的修改。

    为服务器启用 Azure Defender 时，可以使用 FIM 来验证 Windows 文件、Windows 注册表和 Linux 文件的完整性。 [了解详细信息](security-center-file-integrity-monitoring.md)。

- **自适应应用程序控制 (AAC) ** 自适应应用程序控件是一种智能和自动化的解决方案，用于为计算机定义已知安全应用程序的允许列表。

    启用并配置自适应应用程序控件后，如果任何应用程序运行的不是您定义为安全的应用程序，您将收到安全警报。 [了解详细信息](security-center-adaptive-network-hardening.md)。

- **自适应网络强化 (ANH) ** -应用网络安全组 (NSG) 来筛选进出资源的流量，从而改善网络安全状况。 但是，仍然可能存在一些这样的情况：通过 NSG 流动的实际流量是所定义 NSG 规则的子集。 在这些情况下，可以根据实际流量模式强化 NSG 规则，从而进一步改善安全状况。

    自适应网络强化为进一步强化 NSG 规则提供了建议。 它使用机器学习算法，这种算法会将实际流量、已知受信任的配置、威胁情报和其他泄露标志都考虑在内，然后提供仅允许来自特定 IP/端口元组的流量的建议。 [了解详细信息](security-center-adaptive-network-hardening.md)。

- **与 Microsoft Defender 高级威胁防护 (ATP) 与 Microsoft Defender 高级威胁防护 (仅限 Windows) ** -Azure Defender 与 Microsoft Defender 高级威胁防护 (atp) 集成。 两者共同提供全面的终结点检测和响应 (EDR) 功能。 [了解详细信息](security-center-wdatp.md)。

    > [!IMPORTANT]
    > 使用安全中心的 Windows 服务器上已自动启用 Microsoft Defender ATP 传感器。

    Microsoft Defender ATP 在检测到威胁时会触发警报。 警报显示在 "安全中心" 中。 在安全中心，你还可以透视到 Microsoft Defender ATP 控制台，并执行详细的调查以发现攻击范围。 有关 Microsoft Defender ATP 的详细信息，请参阅[将服务器加入 Microsoft Defender ATP 服务](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

- **Docker 主机强化** -Azure 安全中心识别 IaaS Linux vm 或运行 Docker 容器的其他 Linux 计算机上托管的非托管容器。 安全中心会持续评估这些容器的配置。 然后，它会将其与 Internet 安全中心 (CIS) 的 Docker 基准进行比较。 安全中心包含 CIS 的 Docker 基准的完整规则集，并会在容器不符合控件标准的情况下发出警报。 [了解详细信息](harden-docker-hosts.md)。

- **Fileless 攻击检测 (仅限 Windows) ** 攻击会将恶意有效负载注入到内存中，以避免通过基于磁盘的扫描技术检测。 然后，攻击者的负载会在遭到攻击的进程中保持不变，并执行各种恶意活动。

  自动内存取证技术使用无文件攻击检测来识别无文件攻击工具包、方法和行为。 此解决方案会定期在运行时扫描计算机，并直接从进程内存中提取见解。 适用于 Linux 的特定见解包括以下内容的标识： 

  - 众所周知的工具包和加密挖掘软件 

  - 外壳代码是一小段代码，通常用作利用软件漏洞的有效负载。

  - 进程内存中注入的恶意可执行文件

  Fileless 攻击检测会生成详细的安全警报，其中包含具有其他进程元数据的说明，例如网络活动。 这加快了警报会审、相关和下游响应时间。 此方法补充基于事件的 EDR 解决方案，并提供更高的检测范围。

  有关无文件攻击检测警报的详细信息，请参阅[警报参考表](alerts-reference.md#alerts-windows)。

- **Linux 审核警报和 Log Analytics 代理集成仅 (linux) ** -审核系统包含一个内核级别子系统，该子系统负责监视系统调用。 该子系统会按照指定的规则集筛选这些调用，并将针对这些调用生成的消息写入到套接字。 安全中心在 Log Analytics 代理中集成了 auditd 包的功能。 通过这种集成，无需满足任何先决条件，就能在所有受支持的 Linux 发行版中收集 auditd 事件。

    可以使用适用于 Linux 的 Log Analytics 代理收集、扩充 auditd 记录并将其聚合到事件中。 安全中心会持续添加新分析功能，这些功能可以使用 Linux 信号来检测云和本地 Linux 计算机上的恶意行为。 类似于 Windows 中的功能，这些分析功能可以检测各种可疑进程、可疑登录企图、内核模块加载操作和其他活动。 这些活动可能表示计算机正在受到攻击或已遭入侵。  

    有关 Linux 警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-linux)。


## <a name="simulating-alerts"></a>模拟警报

可以通过下载以下行动手册之一来模拟警报：

- 对于 Windows： [Azure 安全中心操作手册：安全警报](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- 对于 Linux： [Azure 安全中心操作手册： Linux 检测](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf)。




## <a name="next-steps"></a>后续步骤

本文介绍了用于服务器的 Azure Defender。 

如需相关材料，请参阅以下文章： 

- 无论警报是由安全中心生成，还是由安全中心从其他安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。

- > [!div class="nextstepaction"]
    > [启用 Azure Defender](security-center-pricing.md)