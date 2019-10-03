---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: b13b809b04f6cf878d68311b756ed2ca826f9697
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935308"
---
**上次文档更新时间**：太平洋标准时间8月9日 2019 10:00。

发现一种称为推理执行旁道攻击的[新型 CPU 漏洞](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)，这使想要了解其详情的客户向我们提出了问题。  

Microsoft 已在我们的所有云服务中部署了缓解措施。 运行 Azure 并将客户工作负荷相互隔离的基础结构是受保护的。 这意味着使用同一基础结构的潜在攻击者无法使用这些漏洞攻击你的应用程序。

Azure 尽可能使用[内存保留维护](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot)，这样可以尽量降低对客户的影响并且不需要重启。 Azure 会在对主机进行系统级更新时继续利用这些方法，对客户进行保护。

若要详细了解如何将安全性集成到 Azure 的各个方面，请访问 [Azure 安全文档](https://docs.microsoft.com/azure/security/)站点。 

> [!NOTE] 
> 自本文档首次发布以来，已发现此漏洞类型的多个变体。 Microsoft 会继续不遗余力地为客户提供保护和指导。 我们会继续发布更多的修复程序，同时也会更新此页面。 
> 
> 在 2019 年 5 月 14 日，[Intel 披露了](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html)一组新的推理执行旁道漏洞，称为“微体系结构数据采样”（即 MDS，请参阅 Microsoft 安全指南 [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)），它已被分配多个 CVE： 
> - CVE-2019-11091 - 微体系结构数据采样不可缓存内存 (MDSUM)
> - CVE-2018-12126 - 微体系结构存储缓冲区数据采样 (MSBDS) 
> - CVE-2018-12127 - 微体系结构负载端口数据采样 (MLPDS)
> - CVE-2018-12130 - 微体系结构填充缓冲区数据采样 (MFBDS)
>
> 此漏洞影响 Intel® Core® 处理器和 Intel® Xeon® 处理器。  Microsoft Azure 发布了操作系统更新, 并且正在部署新的微码, 因为它是由 Intel 提供的, 因此在整个我们的公司中, 为客户提供这些新漏洞的防护。   Azure 正在与 Intel 密切合作，以便在平台上正式发布新的微代码之前对其进行测试和验证。 
>
> **正在 VM 中运行不受信任的代码的客户**需要通过阅读下面的信息来了解有关所有推理执行旁道漏洞的更多指南（Microsoft 安全公告 ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)、[180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018) 和 [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)），从而采取措施来免受这些漏洞影响。
>
> 其他客户应从“深度防御”角度评估这些漏洞，并考虑其所选配置的安全性和性能影响。



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

如果运行的是不受信任的代码，可以在 VM 或云服务中启用额外的安全功能。 与此同时，请确保操作系统是最新版本，以便可以在 VM 或云服务内启用安全功能

### <a name="windows"></a>Windows 

目标操作系统必须为最新才能启用这些额外安全功能。 虽然许多推理执行旁道缓解措施是默认启用的，但此处所述的额外功能必须手动启用，并且可能会造成性能影响。 


**步骤 1：在 VM 上禁用超线程功能** - 在超线程 VM 上运行不受信任的代码的客户将需要禁用超线程功能或转换到非超线程的 VM 大小。 有关超线程 VM 大小（其中 vCPU 与核心的比率为 2:1）的列表，请参阅[此文档](https://docs.microsoft.com/azure/virtual-machines/windows/acu)。 若要从 VM 中使用 Windows 命令行检查 VM 是否启用了超线程功能，请参考下面的脚本。

键入 `wmic` 以进入交互式界面。 然后键入以下命令来查看 VM 上的物理和逻辑处理器数量。

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

如果逻辑处理器数量大于物理处理器（核心）数量，则超线程功能已启用。  如果运行的是超线程 VM，请[联系 Azure 支持](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以禁用超线程功能。  在超线程功能已禁用后，**支持人员将要求完全重启 VM**。 请参阅[核心计数](#core-count)以了解 VM 核心计数减少的原因。


**步骤 2**：在执行步骤 1 的同时，请按照 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中的说明，使用 [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell 模块验证是否已启用了保护。

> [!NOTE]
> 如果以前下载过此模块，则需安装最新版本。
>


PowerShell 脚本的输出应具有以下值才能确认已针对这些漏洞启用了保护：

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

如果输出显示 `MDS mitigation is enabled: False`，请[联系 Azure 支持](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以了解可用的缓解选项。



**步骤 3**：若要启用内核虚拟地址隐藏 (KVAS) 和分支目标注入 (BTI) OS 支持，请按照 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中的说明，使用 `Session Manager` 注册表项启用保护。 必须重启。


**步骤 4**：对于正在使用[嵌套虚拟化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)的部署（仅限 D3 和 E3）：这些说明适用于用作 HYPER-V 主机的 VM。

1.  按照 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 中的说明，使用 `MinVmVersionForCpuBasedMitigations` 注册表项启用保护。
2.  可以按照[此处](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)的说明将虚拟机监控程序计划程序类型设置为 `Core`。


### <a name="linux"></a>Linux

<a name="linux"></a>在其中启用额外安全功能集要求目标操作系统已彻底更新。 某些缓解措施会默认启用。 以下部分介绍的功能是默认关闭的，以及/或者是依赖于硬件支持（微代码）的。 启用这些功能可能造成性能影响。 如需进一步的说明，请参阅操作系统提供商的文档。


**步骤 1：在 VM 上禁用超线程功能** - 在超线程 VM 上运行不受信任的代码的客户将需要禁用超线程功能或转换到非超线程VM。  有关超线程 VM 大小（其中 vCPU 与核心的比率为 2:1）的列表，请参阅[此文档](https://docs.microsoft.com/azure/virtual-machines/linux/acu)。 若要检查是否正在运行超线程 VM，请在 Linux VM 中运行 `lscpu` 命令。 

如果 `Thread(s) per core = 2`，则已启用超线程功能。 

如果 `Thread(s) per core = 1`，则已禁用超线程功能。 

 
启用了超线程功能的 VM 的输出示例： 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

如果运行的是超线程 VM，请[联系 Azure 支持](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以禁用超线程功能。  在超线程功能已禁用后，**支持人员将要求完全重启 VM**。 请参阅[核心计数](#core-count)以了解 VM 核心计数减少的原因。



**步骤 2**：若要防范以下任何推理执行端通道漏洞, 请参阅操作系统提供商的文档:   
 
- [Redhat 和 CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>核心计数

创建超线程 VM 时，Azure 会为每个核心分配 2 个线程，这些线程称为 vCPU。 禁用超线程功能时，Azure 会删除一个线程并向上显示单线程核心（物理核心）。 vCPU 与 CPU 的比率为 2:1，因此禁用超线程功能后，VM 中的 CPU 计数将似乎减少了一半。 例如，D8_v3 VM 是在 8 个 vCPU（每个核心 2 个线程 x 4 个核心）上运行的超线程 VM。  禁用超线程功能后，CPU 将减至 4 个物理核心（每个核心 1 个线程）。 

## <a name="next-steps"></a>后续步骤

对于会影响许多现代处理器的推理执行旁道攻击，本文提供了相应指南：

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002)：
- CVE-2017-5715 - 分支目标注入 (BTI)  
- CVE-2017-5754 - 内核页表隔离 (KPTI)
- CVE-2018-3639 –忽略推理存储区 (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows 内核信息– Spectre 变体1的变体
 
[L1 终端故障 (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018)：
- CVE-2018-3615 - Intel 软件防护扩展 (Intel SGX)
- CVE-2018-3620 - 操作系统 (OS) 和系统管理模式 (SMM)
- CVE-2018-3646 –影响 Virtual Machine Manager (VMM)

[微体系结构数据采样](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013)： 
- CVE-2019-11091 - 微体系结构数据采样不可缓存内存 (MDSUM)
- CVE-2018-12126 - 微体系结构存储缓冲区数据采样 (MSBDS)
- CVE-2018-12127 - 微体系结构负载端口数据采样 (MLPDS)
- CVE-2018-12130 - 微体系结构填充缓冲区数据采样 (MFBDS)








