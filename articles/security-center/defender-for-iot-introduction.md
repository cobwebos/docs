---
title: 适用于 IoT 的 Azure Defender
description: 了解用于 IoT 的 Azure Defender
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67bb01092cb6171ae56b8ab8b308e0c2ea3bece0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934605"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Azure Defender for IoT 简介

统一安全管理，并跨混合云工作负荷和 Azure IoT 解决方案实现端到端的威胁检测和分析。

Azure Defender for IoT 提供：

- **资产发现和网络映射**，包括设备制造商、设备类型等详细信息，以及设备在网络上的通信方式
- **漏洞管理**，包括有关标识符、开放端口和未授权 internet 连接的信息
- **持续威胁监视**，其中包含实时警报，指明任何异常或未经授权的活动，例如目标攻击或恶意软件

[专用文档](https://docs.microsoft.com/azure/asc-for-iot/overview)中提供了完整的详细信息。

## <a name="availability"></a>可用性
|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|计价|需要 [Azure Defender](security-center-pricing.md)|
|必需的角色和权限：|计算机的 Nsg 的写入权限|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权 (US Gov、中国 Gov、其他 Gov) |
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>哪些设备可以保护 Azure Defender 的 IoT？
通过结合安全中心、Azure Defender 和 CyberX 无代理技术的功能，你可以确保安全：

- 通过 IoT 中心连接的**领域设备**。 这使组织能够通过使用单个统一的解决方案保护新式的 internet 本机设备和传统的 ICS/SCADA 设备，加快其 IoT 计划。
    - 载入新设备，并跨工作负荷 (叶设备、Microsoft Edge 设备、IoT 中心) 中应用安全策略，确保符合安全标准和改进的安全状况。

- 在运营技术中使用的**非托管要重建 mqtt 设备** (于) 环境（例如制造、构建管理系统 (BMS) 、生命科学、能源和水设施、石油 & 气体和后勤）。 
    - 为了保护非托管设备，Azure Defender for IoT 使用本地传感器来实现被动的、非侵害性的网络流量分析 (NTA) ，对对该环境的性能没有影响。 
    - 它还包含对专用 IoT/OT 协议的深入了解-结合了已获专利、IoT/OT 的行为分析和机器学习，无需配置任何规则或签名，导致在几分钟或几小时内部署典型部署，而不是几天或几周。 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>用于 IoT 与 Azure Sentinel 集成的 azure Defender
若要启用统一 IT/执行安全监视和管理，Azure Defender IoT 与 [Azure Sentinel](../sentinel/overview.md)集成。

SecOps 团队可以：

- 通过 Sentinel 随附的特定之忠诚度行动手册快速检测和响应 IoT/OT 威胁
- 受益于由 Microsoft 内部 IoT/OT 威胁智能团队的52节提供的持续更新的 IoT/特定威胁情报
- 将用于 IoT 的 Azure Defender 与现有 SOC 工作流和第三方安全工具（如 Splunk、IBM QRadar 和 ServiceNow）集成


## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure 安全中心中获取用于 IoT 的 Azure Defender。 有关详细信息，请参见:

- [适用于 IoT 的 Azure 安全中心简介](../asc-for-iot/overview.md)
