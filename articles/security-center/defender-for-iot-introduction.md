---
title: 适用于 IoT 的 Azure Defender
description: 关于适用于 IoT 的 Azure Defender
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448349"
---
# <a name="introduction-to-azure-defender-for-iot"></a>适用于 IoT 的 Azure Defender 简介

实现统一的安全管理，并跨混合云工作负载和 Azure IoT 解决方案实现端到端的威胁检测和分析。

适用于 IoT 的 Azure Defender 提供：

- **资产发现和网络映射**，包括有关设备制造商、设备类型以及设备在网络上的通信方式等详细信息
- **漏洞管理**，包括有关 CVE、开放端口和未经授权的 internet 连接的信息
- **持续威胁监视**，并提供实时警报，用于指示任何异常或未经授权的活动，例如目标攻击或恶意软件

可阅读[专用文档](https://docs.microsoft.com/azure/asc-for-iot/overview)，了解完整详细信息。

## <a name="availability"></a>可用性
|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|需要 [Azure Defender](security-center-pricing.md)|
|所需角色和权限：|对计算机网络安全组的写入权限|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>适用于 IoT 的 Azure Defender 可以保护哪些设备？
通过整合安全中心、Azure Defender 和 CyberX 无代理技术的功能，可以保护：

- 通过 IoT 中心连接的绿色领域设备。 这使组织能够使用一个统一的解决方案同时保护新式 internet 本机设备和传统的 ICS/SCADA 设备，从而加快其 IoT 计划的进展。
    - 加入新设备，同时在所有工作负载（叶设备、Microsoft Edge 设备、IOT 中心）中应用安全策略，确保符合安全标准，并提升整体安全性。

- 制造业、“构建管理系统”(BMS)、生命科学、能源和水利设施、石油和天然气以及物流等运营技术 (OT) 环境中使用的非托管棕色领域设备。 
    - 为了保护非托管设备，适用于 IoT 的 Azure Defender 使用本地传感器来实现被动的、非侵害性的网络流量分析 (NTA)，对 OT 环境不产生任何性能影响。 
    - 它还包含对专用 IoT/OT 协议的深入理解，同时结合了已获专利的 IoT/OT 感知行为分析和机器学习，从而无需配置任何规则或签名，就能实现在几分钟或几小时（而不是几天或几周）内完成典型部署。 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>适用于 IoT 的 Azure Defender 与 Azure Sentinel 集成
为实现统一 IT/OT 安全监视和管理，适用于 IoT 的 Azure Defender 与 [Azure Sentinel](../sentinel/overview.md) 进行了集成。

SecOps 团队可以：

- 通过 Sentinel 随附的特定于 OT 的 SOAR 操作手册快速检测和响应 IoT/OT 威胁
- 受益于由 Microsoft 内部 IoT/OT 威胁智能团队 Section 52 提供的持续更新的特定于 IoT/OT 的威胁情报
- 将用于 IoT 的 Azure Defender 与现有 SOC 工作流和第三方安全工具（如 Splunk、IBM QRadar 和 ServiceNow）集成


## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 安全中心适用于 IoT 的 Azure Defender。 有关详细信息，请参阅：

- [适用于 IoT 的 Azure 安全中心简介](../asc-for-iot/overview.md)
