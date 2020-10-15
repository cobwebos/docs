---
title: 组件 & 必备组件
description: Azure Defender for IoT 服务先决条件入门所需的所有内容的详细信息。
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
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089173"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Azure Defender for IoT 必备组件

本文介绍 IoT 服务的 Defender 的不同组件、需要开始的内容，并说明有助于了解服务的基本概念。

## <a name="minimum-requirements"></a>最低要求

- 基于 CyberX 技术的 IoT 和设备的无代理监视 () 
    - 支持通过 SPAN 端口监视流量的网络交换机
    - 适用于 NTA 传感器的硬件设备，有关详细信息，请参阅 [认证的硬件](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - 仅在为定义已提交的设备进行安装的过程中，才能 (Azure 订阅 **参与者** 角色) 
    - IoT 中心 (适用于云连接管理的免费或标准层) **参与者** 角色 () 
- 通过 Azure IoT 中心管理的托管 IoT 设备的安全性
    - IoT 中心 (标准层) **参与者** 角色
    - IoT 中心：应启用 **Azure Defender iot** 功能切换
    - 有关设备级别安全模块支持  
        - 用于 IoT 代理的 Defender 支持不断增长的设备和平台列表，请参阅 [支持的平台列表](how-to-deploy-agent.md)


## <a name="supported-service-regions"></a>支持的服务区域

有关详细信息，请参阅 [IoT 中心支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) 。 

Defender for IoT 将所有欧洲地区的所有流量路由到西欧的区域数据中心，并将所有剩余区域路由到美国中部区域数据中心。

## <a name="next-steps"></a>后续步骤

- 阅读 Azure IoT 安全 [概述](overview.md)
- 了解如何 [启用该服务](quickstart-onboard-iot-hub.md)
- 阅读 [用于 IoT 的 DEFENDER 常见问题解答](resources-frequently-asked-questions.md)
- 了解如何 [了解用于 IoT 警报的 Defender](concept-security-alerts.md)
