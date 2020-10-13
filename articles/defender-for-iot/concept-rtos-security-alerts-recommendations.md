---
title: 用于 Azure RTO 内置 & 自定义警报和建议的安全模块
description: 使用 Azure IoT 安全模块-RTO 了解安全警报和建议的补救措施。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cf4924f8a9b97487e64e12ab80df92f2b2a81de2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934481"
---
# <a name="security-module-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Azure RTO 安全警报和建议 (预览的安全模块) 

Azure RTO 安全模块使用高级分析和威胁智能持续分析 IoT 解决方案，以提醒你潜在的恶意活动和可疑的系统修改。 你还可以根据你对预期设备行为和基线的了解来创建自定义警报。

Azure RTO 警报的安全模块可用作潜在的折衷指标，并应进行调查和修正。 Azure RTO 建议的安全模块标识要修正和更新的弱安全状况。 

在本文中，你将找到基于默认范围触发的内置警报和建议列表，并根据预期或基线行为，使用自己的值进行自定义。 

若要详细了解如何在用于 IoT 的 Defender 服务中使用警报自定义，请参阅 [可自定义警报](concept-customizable-security-alerts.md)。 下表详细介绍了使用 Azure RTO 的安全模块时可进行自定义的特定警报和建议。 

## <a name="security-module-for-azure-rtos-supported-security-alerts"></a>Azure RTO 安全模块支持的安全警报

### <a name="device-related-security-alerts"></a>与设备相关的安全警报

|与设备相关的安全警报活动  |警报名称  |
|---------|---------|
|IP 地址| 检测到可疑 IP 地址的通信|
|X.509 设备证书指纹|X.509 设备证书指纹不匹配|
|X.509 证书| X.509 证书已过期|
|SAS 令牌| 过期的 SAS 令牌|
|SAS 令牌| SAS 令牌签名无效|

### <a name="iot-hub-related-security-alerts"></a>与 IoT 中心相关的安全警报

|IoT 中心安全警报活动  |警报名称  |
|---------|---------|
|添加证书    |  检测到将证书添加到 IoT 中心的尝试失败       |
|诊断设置的添加或编辑    | 检测到尝试添加或编辑 IoT 中心的诊断设置      |
|删除证书    |  检测到从 IoT 中心删除证书的尝试失败       |
|删除诊断设置    |  检测到尝试从 IoT 中心删除诊断设置      |
|已删除证书    | 检测到从 IoT 中心删除证书        |
|新证书     |  检测到将新证书添加到 IoT 中心       |

## <a name="security-module-for-azure-rtos-supported-customizable-alerts"></a>支持 Azure RTO 的安全模块可自定义警报

### <a name="device-related-customizable-alerts"></a>与设备相关的可自定义警报

|与设备相关的活动 |警报名称  |
|---------|---------|
|活动连接数|活动连接数不在允许的范围内|
|**MQTT**协议中的云到设备消息|**MQTT**协议中的云到设备消息数不在允许的范围内|
|出站连接| 到不允许的 IP 的出站连接|

### <a name="hub-related-customizable-alerts"></a>与集线器相关的可自定义警报 

|中心相关活动  |警报名称  |
|---------|---------|
|命令队列清除     |  超出允许范围的命令队列数       |
|**MQTT**协议中的云到设备消息    |  **MQTT**协议中允许范围之外的云到设备消息数       |
|**MQTT**协议中的设备到云消息    | **MQTT**协议中允许范围外的设备到云的消息数        |
|直接方法调用     |  允许范围外的直接方法调用数       |
|已拒绝 **MQTT** 协议中的云到设备消息     |   在 **MQTT** 协议中允许范围外拒绝云到设备消息的次数      |
|对克隆模块的更新     |  超出允许范围的非克隆模块的更新次数       |
|未经授权的操作    |  允许范围外的未授权操作的数目       |

## <a name="security-module-for-azure-rtos-supported-recommendations"></a>Azure RTO 支持的安全模块建议

### <a name="device-related-recommendations"></a>与设备相关的建议

|与设备相关的活动  |建议名称 |
|---------|---------|
|身份验证凭据    |  多设备使用相同的身份验证凭据       |

### <a name="hub-related-recommendations"></a>与中心相关的建议

|与 IoT 中心相关的活动  |建议名称 |
|---------|---------|
|IP 筛选器策略   |  默认 IP 筛选器策略应设置为 "**拒绝**"  |
|IP 筛选器规则| IP 筛选器规则包含大型 IP 范围|
|诊断日志|在 IoT 中心启用诊断日志的建议|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>所有 Defender for IoT 警报和建议

有关与 IoT 服务相关的警报和建议的完整列表，请参阅 iot 安全 [警报](concept-security-alerts.md)，iot 安全 [建议](concept-recommendations.md)。

## <a name="next-steps"></a>后续步骤

- [快速入门： Azure RTO 安全模块](quickstart-azure-rtos-security-module.md)
- [配置和自定义 Azure RTO 安全模块](how-to-azure-rtos-security-module.md)
- 请参阅 [AZURE RTO API 安全模块](azure-rtos-security-module-api.md)