---
title: Azure RTO 安全模块概述
description: 若要详细了解 Azure RTO 支持和实施的安全模块，请参阅用于 IoT 的 Azure Defender。
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
ms.openlocfilehash: ec314a729914fee26d30165c9df1644bde3845aa
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995501"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>概述： Azure RTO (预览版的 IoT 安全模块 Defender) 

Azure Defender for IoT 安全模块为使用 Azure RTO 的设备提供了一个全面的安全解决方案。 它提供常见威胁的覆盖范围，并在实时操作系统 (RTO) 设备上提供潜在的恶意活动。 Azure RTO 现在随附了 Azure IoT 安全模块。

![用于 IoT Azure RTO 的 Defender 的可视化。](./media/architecture/azure-rtos-security-monitoring.png)


Azure RTO 的安全模块提供了以下功能：

- 恶意网络活动检测
- 自定义基于警报的设备行为基线
- 改善了设备安全的安全

## <a name="detect-malicious-network-activities"></a>检测恶意网络活动

监视每个设备的入站和出站网络活动。 支持的协议是 IPv4 和 IPv6 上的 TCP、UDP 和 ICMP。 Defender for IoT 会对照 Microsoft 威胁情报源检查每个网络活动。 该源会实时更新，并在世界各地收集数百万个独特的威胁指标。

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>基于自定义警报的设备行为基线

基线允许将设备群集到安全组，并定义每个组的预期行为。 由于 IoT 设备通常设计为在定义完善的方案中运行，因此，可以通过使用一组参数轻松创建定义其预期行为的基线。 基线之间的任何偏差都会触发警报。

## <a name="improve-your-device-security-hygiene"></a>提高设备安全的安全

通过使用适用于 IoT 的建议基础结构，你可以获取有关环境中的问题的知识和见解，这些问题会影响和损坏设备的安全状况。 如果不保持原样，则不安全的 IoT 设备安全状态会允许潜在攻击成功。 安全始终由任何组织内的最薄弱环节来度量。

## <a name="get-started-protecting-azure-rtos-devices"></a>开始保护 Azure RTO 设备

可免费下载适用于你的设备的 Azure RTO 安全模块。 每个 Azure 订阅30天试用版提供 IoT 云服务的 Defender。 若要开始，请下载 [AZURE rto 安全模块](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md)。 

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure RTO 的安全模块。 若要详细了解安全模块并开始操作，请参阅以下文章：

- [Azure RTO IoT 安全模块概念](concept-rtos-security-module.md)
- [快速入门： Azure RTO IoT 安全模块](quickstart-azure-rtos-security-module.md)
