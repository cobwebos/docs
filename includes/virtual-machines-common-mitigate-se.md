---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 130cc66831b25621cb022eb19005c624fcd71b9e
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2018
ms.locfileid: "40105500"
---
**文档上次更新时间**：太平洋标准时间 2018 年 8 月 14 日上午 10:00。

发现一种称为推理执行旁道攻击的[新型 CPU 漏洞](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)，这使想要了解其详情的客户向我们提出了问题。  

Microsoft 已在我们的所有云服务中部署了缓解措施。 运行 Azure 并将客户工作负荷相互隔离的基础结构是受保护的。 这意味着使用同一基础结构的潜在攻击者无法使用这些漏洞攻击你的应用程序。

Azure 尽可能使用[内存保留维护](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#memory-preserving-maintenance)，这样可以尽量降低对客户的影响并且不需要重启。 Azure 会在对主机进行系统级更新时继续利用这些方法，对客户进行保护。

若要详细了解如何将安全性集成到 Azure 的各个方面，请访问 [Azure 安全文档](https://docs.microsoft.com/azure/security/)站点。 

> [!NOTE] 
> 自本文档首次发布以来，已发现此漏洞类型的多个变体。 Microsoft 会继续不遗余力地为客户提供保护和指导。 我们会继续发布更多的修复程序，同时也会更新此页面。 
> 
> 2018 年 8 月 14 日，业界发现新的称之为 [L1 终端故障](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (L1TF) 的推理执行旁道漏洞，此漏洞已被分配了多个 CVE（[CVE-2018-3615、CVE-2018-3620 和 CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)）。 此漏洞影响 Intel® Core® 处理器和 Intel® Xeon® 处理器。 Microsoft 已在我们的所有云服务中部署了缓解措施，强化客户之间的隔离。 请阅读下面的内容，以获取有关如何防范 L1TF 漏洞和旧漏洞的更多指南（[Spectre 变体 2 CVE-2017-5715 和 Meltdown 变体 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)）。
>  






## <a name="keeping-your-operating-systems-up-to-date"></a>使操作系统保持最新

虽然无需 OS 更新即可将你在 Azure 上运行的应用程序与其他 Azure 客户隔离，但使软件保持最新始终是最佳做法。 最新的 Windows 安全汇总包含了针对多个推理执行旁道漏洞的缓解措施。 类似地，Linux 发行版发布了多个修补这些漏洞的更新。 建议执行下面的操作来更新操作系统：

| 产品/服务 | 建议的操作  |
|----------|---------------------|
| Azure 云服务  | 启用[自动更新](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal)，或确保运行最新的来宾 OS。 |
| Azure Linux 虚拟机 | 安装操作系统提供商的更新。 有关详细信息，请查看本文档后面部分的 [Linux](#linux)。 |
| Azure Windows 虚拟机  | 安装最新的安全汇总。
| 其他 Azure PaaS 服务 | 使用这些服务的客户不需要执行任何操作。 Azure 会自动使你的操作系统版本保持最新。 |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>附加指南（如果正在运行不受信任的代码） 

那些允许不受信任的用户执行任意代码的客户可能会希望在其 Azure 虚拟机或云服务中实施一些额外的安全功能。 这些功能可防范多个推理执行漏洞所描述的进程内泄露矢量。

建议实施额外安全功能的示例方案：

- 允许不信任的代码在 VM 中运行。  
    - 例如，允许某一客户上传你随后要在应用程序内执行的二进制文件或脚本。 
- 允许不信任的用户使用权限低的帐户登录到 VM。   
    - 例如，允许权限低的用户使用远程桌面或 SSH 登录到某个 VM。  
- 允许不受信任的用户访问通过嵌套虚拟化实现的虚拟机。  
    - 例如，你控制 Hyper-V 主机，但却将 VM 分配给不受信任的用户。 

所实施方案不涉及不受信任代码的客户不需启用这些额外安全功能。 

## <a name="enabling-additional-security"></a>启用额外安全性 

可以在 VM 或云服务中启用额外安全功能。

### <a name="windows"></a>Windows 

目标操作系统必须为最新才能启用这些额外安全功能。 虽然许多推理执行旁道缓解措施是默认启用的，但此处所述的额外功能必须手动启用，并且可能会造成性能影响。 

**步骤 1**：[与 Azure 支持部门联系](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)，要求其向你的虚拟机公开已更新的固件（微代码）。 

**步骤 2**：启用内核虚拟地址隐藏 (KVAS) 和分支目标注入 (BTI) OS 支持。 按照 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中的说明通过 `Session Manager` 注册表项启用保护。 必须重启。 

**步骤 3**：对于使用[嵌套虚拟化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)的部署（仅 D3 和 E3）：这些说明在用作 Hyper-V 主机的 VM 中应用。 

1. 按照 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中的说明通过 `MinVmVersionForCpuBasedMitigations` 注册表项启用保护。  
 
1. 可以按照[此处](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)的说明将虚拟机监控程序计划程序类型设置为“核心”。 

**步骤 4**：按照 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中的说明，验证是否已使用 [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell 模块启用保护。 

> [!NOTE]
> 如果以前下载过此模块，则需安装最新版本。
>

所有 VM 应该显示：

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>在其中启用额外安全功能集要求目标操作系统已彻底更新。 某些缓解措施会默认启用。 以下部分介绍的功能是默认关闭的，以及/或者是依赖于硬件支持（微代码）的。 启用这些功能可能造成性能影响。 如需进一步的说明，请参阅操作系统提供商的文档。
 
**步骤 1**：[与 Azure 支持部门联系](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)，要求其向你的虚拟机公开已更新的固件（微代码）。
 
**步骤 2**：根据操作系统提供商的文档启用分支目标注入 (BTI) OS 支持，以便缓解 CVE-2017-5715（Spectre 变体 2）的影响。 
 
**步骤 3**：根据操作系统提供商的文档启用内核页表隔离 (KPTI)，以便缓解 CVE-2017-5754（Meltdown 变体 3）的影响。 
 
操作系统提供商提供更多信息：  
 
- [Redhat 和 CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [Suse](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅 [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)（确保 Azure 客户免受 CPU 漏洞影响）。
