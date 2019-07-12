---
title: 威胁检测的 Vm 和 Azure 安全中心中的服务器 |Microsoft Docs
description: 本主题介绍了 VM 和服务器中的警报提供 Azure 安全中心。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 5487b4f49f5dbf7b968cd45d40555c69b54c329a
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571577"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>为 Vm 和 Azure 安全中心中的服务器威胁检测

本主题介绍不同类型的检测方法和警报可为 Vm 和服务器与以下操作系统。 有关受支持版本的列表，请参阅[平台和支持的 Azure 安全中心功能](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)。

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

安全中心集成 Azure 服务来监视和保护基于 Windows 的计算机。  安全中心提供易于使用的形式显示的通知和修正建议从所有这些服务。

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Azure 安全中心通过集成与 Windows Defender 高级威胁防护 (ATP) 扩展其云工作负荷保护平台。 这提供了全面的终结点检测和响应 （edr 规范） 功能。

> [!NOTE]
> Windows Server Defender ATP 传感器上的架到 Azure 安全中心的 Windows 服务器上自动启用。

当 Windows Defender ATP 服务器检测到的威胁时，将触发警报。 安全中心仪表板上显示警报。 从仪表板中，您可以切换到 Windows Defender ATP 控制台执行详细的调查，以发现攻击的范围。 有关 Windows Defender ATP 服务器详细信息，请参阅[载入服务器到 Windows Defender ATP 服务](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

### 故障转储分析 <a nanme="windows-dump"></a>

当软件故障时，故障转储可捕获故障时的部分内存。

崩溃可能已由恶意软件或包含恶意软件。 若要避免被安全产品检测到，各种形式的恶意软件使用 fileless 攻击，从而避免了磁盘或编写的加密软件组件写入到磁盘。 这种攻击很难检测使用传统的基于磁盘的方法。

但是，可以通过内存分析检测到这种攻击。 通过分析故障转储中的内存，安全中心可以检测到攻击用于利用软件漏洞、 访问机密数据以及偷偷存留在受攻击计算机的技术。 这是由安全中心后端对主机的最小性能影响。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**发现的代码注入**|代码注入是将可执行模块插入到正在运行的进程或线程中。 此技术用于访问数据的恶意软件，而成功隐藏自身，以阻止要找到和删除。 <br/>此警报指示故障转储中存在注入模块。 为了区分恶意和非恶意注入模块，安全中心会检查注入的模块是否符合可疑行为的配置文件。|
|**发现可疑的代码段**|指示已使用非标准方法，例如反射注入和进程替换，分配的代码段。 警报还提供代码段的已处理，以提供上下文的功能的其他特征和行为的报告的代码段。|
|**发现 Shellcode**|Shellcode 是在恶意软件利用软件漏洞之后运行的有效负载。<br/>此警报指示故障转储分析检测到可执行代码表现出通常由恶意有效负载的行为。 虽然非恶意软件还可以执行此行为，但并不常见的正常的软件开发实践。|

### Fileless 攻击检测 <a nanme="windows-fileless"></a>

在 Azure 中，我们经常看到针对客户的终结点的 fileless 攻击。

若要避免检测，fileless 攻击将恶意有效负载注入到内存中。 攻击者有效负载已泄露的进程的内存中持久保存并执行各种类型的恶意活动。

使用 fileless 攻击检测时，自动的内存取证技术标识 fileless 攻击工具包、 技术和行为。 此解决方案会定期扫描您的计算机在运行时，并直接从安全关键的进程的内存中提取见解。

它会查找利用该漏洞、 代码注入和执行恶意有效负载的证据。 Fileless 攻击检测生成详细的安全警报，以便加快警报会审、 关联和下游响应时间。 这种方法是基于事件的 EDR 解决方案提供了更高版本的检测覆盖范围的补充。

> [!NOTE]
> 你可以通过下载模拟 Windows 警报[Azure 安全中心 Playbook](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef):安全警报，然后按照提供的指导

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**检测到的 fileless 攻击技术**|下面指定的进程的内存包含 fileless 攻击工具包：Meterpreter。 Fileless 攻击工具包通常没有将会显示在文件系统上，使传统防病毒检测变得困难。|

### <a name="further-reading"></a>延伸阅读

有关示例和有关安全中心检测的详细信息：

* [Azure 安全中心如何自动执行网络攻击的检测](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure 安全中心如何检测漏洞使用管理工具](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

安全中心收集的审核记录在使用 Linux 计算机**审核**、 一个最常见的 Linux 的审核框架。 审核的优点的周围至今已有很长的时间和生活在主线内核。 

### Linux 审核警报和 Microsoft Monitoring Agent (MMA) 的集成 <a name="linux-auditd"></a>

审核系统包含的内核级子系统，它是负责监视系统调用，由给定的规则集，对它们进行筛选并为其消息写入到的套接字。 安全中心集成，从审核包内 Microsoft Monitoring Agent (MMA) 的功能。 此集成，在所有支持的 Linux 分发，而无需任何系统必备组件中的审核事件集合。  

收集、 丰富，审核记录，并聚合到使用 Linux MMA 代理的事件。 安全中心不断致力于添加新的分析，该利用 Linux 信号以检测恶意行为在云中和本地 Linux 计算机。 类似于 Windows 功能，这些分析跨可疑进程、 可疑登录尝试次数、 内核模块加载和其他活动，指示计算机已受攻击，或者已经违例。  

下面是分析的几个示例，演示如何跨攻击生命周期的不同阶段。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**进程以异常方式访问 SSH 授权的密钥文件**|类似于已知的恶意软件活动的方法中访问 SSH 授权的密钥文件。 此访问权限可能指示攻击者尝试获取永久性访问的计算机|
|**检测到的持久性尝试**|主机数据分析检测到已安装为单用户模式启动脚本。 <br/>由于很少合法的任何进程所需在该模式下执行，这可能表示攻击者已将恶意进程添加到每个运行级别以保证持久性。|
|**检测到的计划任务的操作**|主机数据分析检测到计划任务的可能的操作。 攻击者通常将计划的任务添加到计算机，它们已破坏获得持久性。|
|**可疑的文件时间戳修改**|主机数据分析检测到可疑的时间戳修改。 攻击者通常时间戳将从现有合法文件复制到新的工具来避免这些新拖放文件检测|
|**向 sudoers 组添加新用户**|主机数据分析检测到用户已添加到 sudoers 组，从而使其成员可以使用高特权运行命令。|
|**可能会利用此漏洞的 dhcp 客户端中的 DynoRoot 漏洞**|主机数据分析检测到与父进程的 dhclient 脚本执行的异常的命令。|
|**检测到可疑的内核模块**|主机数据分析检测到正在加载了内核模块作为一个共享的对象文件。 这可能是合法活动，或指示一个在计算机已遭到破坏。|
|**使用检测到的数字货币挖掘关联的进程**|主机数据分析检测到通常与数字货币挖掘关联的进程的执行|
|**潜在的端口转发到外部 IP 地址**|主机数据分析检测到端口转发到外部 IP 地址的启动。|

> [!NOTE]
> 可以通过下载来模拟 Windows 警报[Azure 安全中心 Playbook:安全警报](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)，然后按照提供的指导。


有关详细信息，请参阅以下文章：  

* [利用 Azure 安全中心可以检测遭到入侵的 Linux 计算机的攻击](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure 安全中心可以检测新出现的漏洞在 Linux 中](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 