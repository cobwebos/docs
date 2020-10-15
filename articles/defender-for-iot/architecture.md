---
title: 用于 IoT 体系结构的 Azure Defender
description: 了解用于 IoT 体系结构和信息流的 Azure Defender。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: rkarlin
ms.openlocfilehash: 4189cb8628db58343c816535836af82825014b7e
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096311"
---
# <a name="azure-defender-for-iot-architecture"></a>用于 IoT 体系结构的 Azure Defender

本文介绍了适用于 IoT 解决方案的 Defender 的功能系统体系结构。

## <a name="defender-for-iot-components"></a>用于 IoT 组件的 Defender

用于 IoT 的 Defender 同时连接到 Azure 云和本地组件。 此解决方案旨在实现具有多个远程位置的大型和地理位置的分布式环境中的可伸缩性。 此解决方案按国家/地区、区域、业务部门或区域启用多层分布式体系结构。 

Azure Defender for IoT 包含以下组件： 

**云连接的部署**

- 用于 IoT 传感器 VM 或设备的 Azure Defender
- Azure 门户云管理和集成到 Azure Sentinel
- 本地管理控制台，用于本地站点管理
- Embedded security agent (可选) 

**气流 (脱机) 部署**

- 用于 IoT 传感器 VM 或设备的 Azure Defender
- 本地站点管理的本地管理控制台


![用于 IoT 体系结构的 Defender](./media/architecture/defender-iot-security-architecture.png)

### <a name="azure-defender-for-iot-sensors"></a>用于 IoT 传感器的 Azure Defender

用于 IoT 传感器的 Defender 发现并持续监视网络设备。 传感器使用被动 (无代理) 在 IoT 和设备上进行监视来收集 ICS 网络流量。 
 
无代理技术专门为 IoT 和 OT 网络构建，可在连接到网络的几分钟内深入了解 IoT 和风险。 它在网络和网络设备上的性能不会受到任何影响，因为它的网络流量分析 (NTA) 方法。 
 
利用获得专利、IoT 和 fileless 的行为分析和第7层层级数据包检查 (DPI) ，它可让你进一步分析基于传统的基于签名的解决方案，以根据异常或未经授权的活动即时检测高级 IoT 和 OT 威胁 (如恶意软件) 。 
  
用于 IoT 传感器的 Defender 连接到 SPAN 端口或网络分流点，并立即开始执行 IoT 和网络流量的 DPI。 
 
直接在传感器上进行数据收集、处理、分析和警报。 这使得它非常适合用于带宽较低或连接延迟较高的位置，因为只有元数据会传输到管理控制台。

该传感器包括五个分析检测引擎。 引擎将根据实时和预先录制的流量分析触发警报。 可用引擎如下： 

#### <a name="protocol-violation-detection-engine"></a>协议冲突检测引擎
协议冲突检测引擎识别违反 ICS 协议规范的数据包结构和字段值的使用情况，例如： Modbus exception 和已过时的函数代码警报。

#### <a name="policy-violation-detection-engine"></a>策略违规检测引擎
使用机器学习时，策略违规检测引擎会向用户通知基线行为的任何偏差，如未授权使用特定的函数代码、对特定对象的访问权限或对设备配置的更改。 例如： DeltaV 软件版本已更改，未授权的 PLC 编程警报。 具体而言，策略违规引擎使用称为 "工业有限状态建模" (IFSM) 的专利方法，将 ICS 网络作为状态和转换的确定性序列进行建模。 策略违反检测引擎建立了 ICS 网络的基准，因此，平台需要更短的学习时间来构建网络基线，而不是最初为其开发而不是针对网络开发的常规数学方法或分析。

#### <a name="industrial-malware-detection-engine"></a>工业恶意软件检测引擎
工业恶意软件检测引擎标识指示已知恶意软件（如 Conficker、黑色能量、Havex、WannaCry、NotPetya 和 Triton）是否存在的行为。 

#### <a name="anomaly-detection-engine"></a>异常情况检测引擎
异常检测引擎) 通信和行为检测到异常的计算机到计算机 (M2M。 通过将 ICS 网络作为确定性状态和转换的序列建模，平台需要比最初为其开发的常规数学方法或分析更短的学习周期，而不是。 它还能更快地检测到异常，且最小误报。 异常情况检测引擎警报包括过多 SMB 登录尝试，而 PLC 扫描检测到警报。

#### <a name="operational-incident-detection"></a>操作事件检测
操作事故检测检测到一些操作问题，如间歇连接性，这可能表示设备出现故障的前期迹象。 例如，设备被怀疑断开连接 (无响应) ，并已向 Siemens S7 停止 PLC 命令发送警报。


### <a name="management-consoles"></a>管理控制台
跨混合环境管理用于 IoT 的 Azure Defender 是通过两个管理门户完成的： 
- 传感器控制台
- 本地管理控制台
- Azure 门户

#### <a name="sensor-console"></a>传感器控制台
传感器检测显示在传感器控制台中，可以在网络图中查看、调查和分析传感器，还可以在广泛的报告中查看、调查和分析传感器，如风险评估报告、数据挖掘查询和攻击向量。 你还可以使用控制台来查看和处理传感器引擎检测到的威胁、将信息转发给第三方系统、管理用户等。

![Defender for IoT 传感器控制台](./media/architecture/sensor-console.png)

#### <a name="on-premises-management-console"></a>本地管理控制台
本地管理控制台使安全运营中心 (SOC) 操作员能够管理和分析从多个传感器聚合到一个仪表板的警报，并提供有关 OT 网络运行状况的总体视图。

此体系结构在 SOC 级别提供全面统一的网络视图、优化的警报处理和控制运营网络安全，确保决策和风险管理保持不变。

除了多租户、监视、数据分析和集中式传感器远程控制外，管理控制台还为每个远程设备提供额外的系统维护工具 (如警报排除) 和完全自定义的报告功能。 此可伸缩体系结构支持站点级别的本地管理、区域级别和 SOC 内的全局管理。

可以为高可用性配置部署管理控制台，它提供了一个备份控制台，该控制台定期接收恢复所需的所有配置文件的备份。 如果主控制台出现故障，则本地站点管理设备将自动故障转移以与备份控制台同步，以保持可用性而不中断。

#### <a name="azure-portal"></a>Azure 门户

Azure 中的 IoT 门户 Defender 用于帮助你：·  购买解决方案设备·  安装和更新软件·  将传感器载入 Azure ·  更新威胁智能包

## <a name="embedded-security-agent-built-in-mode"></a>Embedded security agent：内置模式

在 **内置** 模式下，在你选择启用 iot 中心中的 " **安全** " 选项时，会启用 "用于 iot 的 Defender"。 内置模式提供实时监视、建议和警报，提供单步设备可见性和无与伦比的安全性。 内置模式不需要在任何设备上安装代理，并对记录的活动使用高级分析来分析和保护现场设备和 IoT 中心。

## <a name="embedded-security-agent-enhanced-mode"></a>Embedded security agent：增强模式

在 **增强** 模式下，在 IoT 中心启用 **安全** 选项并在设备上安装用于 IoT 设备代理的 Defender 后，代理会从设备收集、聚合和分析原始安全事件。 原始安全事件可能包括 IP 连接、进程创建、用户登录和其他安全相关信息。 用于 IoT 设备代理的 Defender 还处理事件聚合，以帮助避免高网络吞吐量。 代理具有高度可自定义性，使你可以将其用于特定的任务，例如，仅在最快的 SLA 中发送重要信息，或者将广泛的安全信息和上下文聚合到更大的段，以避免服务成本更高。

设备代理和其他应用程序使用 **azure 发送安全消息 SDK** 将安全信息发送到 Azure IoT 中心。 IoT 中心获取此信息，并将其转发到 IoT 服务的 Defender。

一旦启用了用于 IoT 服务的 Defender，IoT 中心还将发送其所有内部数据，供 Defender 用于分析。 此数据包括设备云操作日志、设备标识和中心配置。 所有这些信息都有助于创建用于 IoT 分析管道的 Defender。

用于 IoT 分析的 Defender 还可以从 Microsoft 和 Microsoft 合作伙伴的各种来源接收其他威胁情报流。 适用于 IoT 整个分析管道的 Defender 适用于在服务 (上进行的每个客户配置，例如自定义警报和发送安全消息 SDK) 的使用。

使用分析管道，将所有信息流结合起来，以生成可操作的建议和警报。 该管道包含由安全研究人员和专家创建的自定义规则，以及从标准设备行为和风险分析搜索偏差的机器学习模型。

用于 IoT 的 Defender 建议和警报 (分析管道输出) 写入每个客户的 Log Analytics 工作区。 如果工作区中包含原始事件以及警报和建议，则可以使用检测到的可疑活动的确切详细信息来深入探讨调查和查询。

## <a name="next-steps"></a>后续步骤

本文介绍了用于 IoT 解决方案的基本体系结构和工作流。 若要详细了解先决条件、如何开始和在 IoT 中心启用安全解决方案，请参阅以下文章：

- [服务先决条件](service-prerequisites.md)
- [入门](getting-started.md)
- [配置解决方案](quickstart-configure-your-solution.md)
- [启用 IoT 中心中的安全性](quickstart-onboard-iot-hub.md)
- [Defender for IoT 常见问题](resources-frequently-asked-questions.md)
- [Defender for IoT 安全警报](concept-security-alerts.md)
