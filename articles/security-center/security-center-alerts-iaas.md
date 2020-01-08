---
title: Azure 安全中心 Vm 和服务器的威胁检测 |Microsoft Docs
description: 本主题介绍 Azure 安全中心提供的 VM 和服务器警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: b101fd30ddbdbf0f8fdf6e02394cb10b9af5f4b0
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666357"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Azure 安全中心 Vm 和服务器的威胁检测

本主题介绍可用于具有以下操作系统的 Vm 和服务器的不同类型的检测方法和警报。 有关支持的版本的列表，请参阅[Azure 安全中心支持的平台和功能](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)。

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows<a name="windows-machines"></a>

Azure 安全中心与 Azure 服务集成，以监视和保护基于 Windows 的计算机。 安全中心以一种易于使用的格式从所有这些服务提供警报和修正建议。

* **Microsoft defender ATP** <a name="windows-atp"></a> -安全中心通过与 Microsoft defender 高级威胁防护（ATP）集成来扩展其云工作负荷保护平台。 这提供了全面的端点检测和响应（EDR）功能。

    > [!NOTE]
    > 使用安全中心的 Windows 服务器上会自动启用 Microsoft Defender ATP 传感器。

    当 Microsoft Defender ATP 检测到威胁时，会触发警报。 警报显示在安全中心仪表板上。 在仪表板中，可以透视到 Microsoft Defender ATP 控制台，并执行详细的调查以发现攻击范围。 有关 Microsoft Defender ATP 的详细信息，请参阅将[服务器载入 Microsoft DEFENDER atp 服务](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

* **故障转储分析** <a name="windows-dump"></a> -当软件崩溃时，崩溃转储在崩溃时捕获部分内存。

    故障可能是恶意软件或包含恶意软件造成的。 为避免被安全产品检测到，各种形式的恶意软件使用 fileless 攻击，这可避免写入磁盘或加密写入磁盘的软件组件。 使用传统的基于磁盘的方法很难检测到这种类型的攻击。

    但是，通过使用内存分析，可以检测到这种攻击。 通过分析故障转储中的内存，安全中心可以检测到攻击所使用的技术。 例如，攻击可能试图利用软件中的漏洞、访问机密数据以及偷偷在受损的计算机中保持不变。 安全中心执行此操作时，对主机性能的影响最小。

    有关故障转储分析警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-crashdump)。

* **Fileless 攻击检测** <a name="windows-fileless"></a> -Fileless 攻击定向到终结点很常见。 为了避免检测，fileless 攻击会将恶意有效负载注入内存。 攻击者负载会在遭到入侵的进程中保持不变，并执行各种恶意活动。

    通过 fileless 攻击检测，自动内存鉴证技术识别 fileless 攻击工具包、技术和行为。 此解决方案会定期在运行时扫描计算机，并直接从安全关键进程的内存中提取见解。

    它找到了利用、代码注入和恶意负载执行的证据。 Fileless 攻击检测生成详细的安全警报，以加速警报会审、相关和下游响应时间。 此方法补充基于事件的 EDR 解决方案，提供更高的检测范围。

    有关 fileless 攻击检测警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-filelessattackdetect)。

> [!NOTE]
> 可以通过下载[Azure 安全中心操作手册：安全警报](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)来模拟 Windows 警报。

## Linux<a name="linux-machines"></a>

安全中心使用**审核**（一个最常见的 Linux 审核框架）从 linux 计算机收集审核记录。 审核位于主线内核中。 

* **Linux 审核警报和 Microsoft Monitoring Agent （MMA）集成** <a name="linux-auditd"></a> -审核系统由用于监视系统调用的内核级别子系统组成。 它按指定的规则集筛选它们，并将其消息写入套接字。 安全中心在 Microsoft Monitoring Agent （MMA）内集成审核包中的功能。 通过这种集成，可在所有受支持的 Linux 发行版中收集审核事件，无需任何先决条件。  

    使用 Linux MMA 代理收集、充实并聚合审核记录到事件中。 安全中心会持续添加使用 Linux 信号检测云和本地 Linux 计算机上的恶意行为的新分析。 类似于 Windows 功能，这些分析跨越可疑进程、可疑登录尝试、内核模块加载和其他活动。 这些活动可以指示计算机受到攻击或已被破坏。  

    以下是跨攻击生命周期的各个阶段的分析的几个示例。

    有关 Linux 警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-linux)。

> [!NOTE]
> 可以通过下载[Azure 安全中心操作手册： Linux 检测](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)来模拟 linux 警报。

 
 ## <a name="next-steps"></a>后续步骤

有关安全中心检测的示例和详细信息，请参阅：

* [Azure 安全中心如何自动检测网络攻击](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure 安全中心如何使用管理工具检测漏洞](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [利用 Azure 安全中心来检测遭受的 Linux 计算机遭受攻击](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure 安全中心可以检测到 Linux 中的新兴漏洞](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)