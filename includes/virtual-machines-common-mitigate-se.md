---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: be8c3d3be4410d15ba132a24a417e7a7b0418352
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620262"
---
**上次文档更新**:14 2019 年 5 太平洋标准时间上午 10:00。

发现一种称为推理执行旁道攻击的[新型 CPU 漏洞](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)，这使想要了解其详情的客户向我们提出了问题。  

Microsoft 已在我们的所有云服务中部署了缓解措施。 运行 Azure 并将客户工作负荷相互隔离的基础结构是受保护的。 这意味着使用同一基础结构的潜在攻击者无法使用这些漏洞攻击你的应用程序。

Azure 尽可能使用[内存保留维护](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot)，这样可以尽量降低对客户的影响并且不需要重启。 Azure 会在对主机进行系统级更新时继续利用这些方法，对客户进行保护。

若要详细了解如何将安全性集成到 Azure 的各个方面，请访问 [Azure 安全文档](https://docs.microsoft.com/azure/security/)站点。 

> [!NOTE] 
> 自本文档首次发布以来，已发现此漏洞类型的多个变体。 Microsoft 会继续不遗余力地为客户提供保护和指导。 我们会继续发布更多的修复程序，同时也会更新此页面。 
> 
> 上，2019 年 5 月 14 日[Intel 披露](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html)一组新的推理执行端通道漏洞称为 Microarchitectural 数据采样 (MDS 请参阅 Microsoft 安全指南[ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013))，它已分配多个 CVEs: 
> - CVE-2018年-11091-Microarchitectural 数据采样，无缓存内存 (MDSUM)
> - CVE-2018年-12126-Microarchitectural 存储缓冲区数据采样 (MSBDS) 
> - CVE-2018年-12127-Microarchitectural 负载端口数据采样 (MLPDS)
> - CVE-2018年-12130-Microarchitectural 填充缓冲区数据采样 (MFBDS)
>
> 此漏洞影响 Intel® Core® 处理器和 Intel® Xeon® 处理器。  Microsoft Azure 已发布的操作系统更新和部署新微代码，因为它可由 Intel，在我们的全套产品来保护我们客户免受这些新的漏洞。   Azure 密切使用 Intel 测试和验证新微代码，在平台上正式发行之前。 
>
> **正在运行不受信任的客户在其 VM 中的代码**需要采取措施来保护用户免受这些漏洞，阅读下面的其他所有推理执行旁道漏洞 (Microsoft 安全公告 ADV 指南[180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)， [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)，并[190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013))。
>
> 其他客户应评估的深度防御这些漏洞，并考虑其选择的配置安全性和性能影响。



## <a name="keeping-your-operating-systems-up-to-date"></a>使操作系统保持最新

虽然无需 OS 更新即可将你在 Azure 上运行的应用程序与其他 Azure 客户隔离，但使软件保持最新始终是最佳做法。 最新的 Windows 安全汇总包含了针对多个推理执行旁道漏洞的缓解措施。 类似地，Linux 发行版发布了多个修补这些漏洞的更新。 下面是我们推荐的操作来更新你的操作系统：

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

如果正在运行不受信任的代码，则可以启用 VM 或云服务内的其他安全功能。 并行情况下，请确保你的操作系统是最新版本以启用虚拟机或云服务内的安全功能

### <a name="windows"></a>Windows 

目标操作系统必须为最新才能启用这些额外安全功能。 虽然许多推理执行旁道缓解措施是默认启用的，但此处所述的额外功能必须手动启用，并且可能会造成性能影响。 


**步骤 1：禁用在 VM 上的超线程**-VM 将需要禁用超线程或移动到的非超线程的 VM 大小超线程上运行不受信任的代码的客户。 若要检查 VM 是否具有启用超线程，请参阅下面的脚本使用 Windows 命令行从 VM 中。

类型`wmic`进入交互式界面。 然后键入以下若要查看的物理和逻辑在 VM 上的处理器。

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

如果逻辑处理器数大于物理处理器 （核心），则启用超线程。  如果运行的超线程虚拟机，请[联系 Azure 支持](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)获取禁用超线程。  禁用超线程，则一旦**支持将要求在完整的 VM 重启**。 


**步骤 2**：在步骤 1 到并行，按照中的说明[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)若要验证保护启用了使用[SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell 模块。

> [!NOTE]
> 如果以前下载过此模块，则需安装最新版本。
>


PowerShell 脚本的输出应具有以下值来验证已启用保护，以应对这些漏洞：

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

如果输出显示`MDS mitigation is enabled: False`，请[联系 Azure 支持](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)可用缓解选项。



**步骤 3**：若要启用内核虚拟地址隐藏 (KVAS) 和分支目标注入 (BTI) OS 支持，请按照中的说明[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)若要启用保护使用`Session Manager`注册表项。 必须重启。


**步骤 4**：对于正在使用的部署[嵌套虚拟化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)（D3 和 E3 仅）：这些说明适用于使用作为 HYPER-V 主机的 VM。

1.  按照中的说明[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)若要启用保护使用`MinVmVersionForCpuBasedMitigations`注册表项。
2.  将虚拟机监控程序计划程序类型设置为`Core`按照的说明[此处](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)。


### <a name="linux"></a>Linux

<a name="linux"></a>在其中启用额外安全功能集要求目标操作系统已彻底更新。 某些缓解措施会默认启用。 以下部分介绍的功能是默认关闭的，以及/或者是依赖于硬件支持（微代码）的。 启用这些功能可能造成性能影响。 如需进一步的说明，请参阅操作系统提供商的文档。


**步骤 1：禁用在 VM 上的超线程**-超线程，VM 将需要禁用超线程或移动到非超线程的 VM 上运行不受信任的代码的客户。  若要检查是否正在运行超线程虚拟机，请运行`lspcu`命令在 Linux VM 中。 

如果`Thread(s) per core = 2`，则已启用超线程。 

如果`Thread(s) per core = 1`，则已禁用超线程。 

 
为 vm 启用超线程的输出示例： 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0,2,4,6
Off-line CPU(s) list:  1,3,5,7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

如果运行的超线程虚拟机，请[联系 Azure 支持](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)获取禁用超线程。  请注意:禁用超线程，则一旦**支持将要求在完整的 VM 重启**。


**步骤 2**：若要针对的任何缓解推理执行旁道漏洞，下面请参阅操作系统提供商的文档：   
 
- [Redhat 和 CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 

## <a name="next-steps"></a>后续步骤

本文指导如何对以下会影响许多现代处理器的推理执行旁道攻击：

[Spectre Meltdown](https://portal.msrc.microsoft.com/security-guidance/advisory/ADV180002):
- CVE-2017年-5715-分支目标注入 (BTI)  
- CVE-2017-5754-内核页表隔离 (KPTI)
- CVE-2018年-3639 – 推理存储区绕过 (KPTI) 
 
[L1 终端故障 (L1TF)](https://portal.msrc.microsoft.com/security-guidance/advisory/ADV180018):
- CVE-2018年-3615-Intel 软件防护扩展 (Intel SGX)
- CVE-2018年-3620-操作系统 (OS) 和系统管理模式 (SMM)
- CVE 2018 3646 – 影响 Virtual Machine Manager (VMM)

[Microarchitectural 数据采样](https://portal.msrc.microsoft.com/security-guidance/advisory/ADV190013): 
- CVE-2018年-11091-Microarchitectural 数据采样，无缓存内存 (MDSUM)
- CVE-2018年-12126-Microarchitectural 存储缓冲区数据采样 (MSBDS)
- CVE-2018年-12127-Microarchitectural 负载端口数据采样 (MLPDS)
- CVE-2018年-12130-Microarchitectural 填充缓冲区数据采样 (MFBDS)








