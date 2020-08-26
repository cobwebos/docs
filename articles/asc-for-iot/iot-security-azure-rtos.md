---
title: Azure RTO 支持
description: 了解 Azure 安全中心在 IoT 服务中的支持。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095893"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>适用于 Azure RTO 的 IoT 安全解决方案的 azure 安全中心 

用于 IoT 安全模块的 Azure 安全中心为 Azure RTO 设备提供了一个全面的安全解决方案。 Azure RTO 附带内置的安全模块，涵盖实时操作系统设备上的常见威胁。 

![用于 IoT 的 azure 安全中心 Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


Azure 安全中心 with Azure RTO 支持的 IoT 安全模块提供以下功能： 
- 恶意网络活动检测
- 基于自定义警报，设备行为基线
- 改善设备安全卫生

### <a name="detection-of-malicious-network-activities"></a>检测恶意网络活动

监视每个设备的入站和出站网络活动（支持的协议： TCP、UDP、IPv4 和 IPv6 上的 ICMP）。 适用于 IoT 的 Azure 安全中心会检查每个网络活动的 Microsoft 威胁智能源。 该源会实时更新，并在世界各地收集数百万个独特的威胁指标。 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>基于自定义警报的设备行为基线

基线允许将设备群集到安全组，并定义每个组的预期行为。 由于 IoT 设备通常设计为在定义完善的方案中运行，因此可以使用一组参数轻松创建一个定义其预期行为的基线。 基线之间的任何偏差都会触发警报。 

### <a name="improve-your-device-security-hygiene"></a>提高设备安全的安全

利用 Azure 安全中心建议的 Azure 安全中心提供的信息，获取有关环境中的问题的知识和见解，这些问题会影响和损坏设备的安全状况。 如果不保持原样，IoT 设备安全状况可能会导致潜在攻击成功，因为安全始终由任何组织内的最薄弱环节来度量。 

## <a name="get-started-protecting-azure-rtos-devices"></a>开始保护 Azure RTO 设备

- 适用于 Azure RTO 的 IoT 安全模块的 azure 安全中心作为设备免费下载提供。 适用于 IoT 云服务的 Azure 安全中心提供，每个 Azure 订阅30天试用版。 下载 azure[安全中心的 IoT 安全模块，以便开始执行 AZURE rto](https://github.com/azure-rtos/iot-security-module-preview) 。 


## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 安全中心，了解 IoT Azure RTO 支持。 若要了解如何开始使用并在 IoT 中心启用安全解决方案，请参阅以下文章：

- [服务先决条件](service-prerequisites.md)
- [入门](getting-started.md)
- [配置解决方案](quickstart-configure-your-solution.md)
- [启用 IoT 中心中的安全性](quickstart-onboard-iot-hub.md)
- [Azure 安全中心的 IoT 常见问题](resources-frequently-asked-questions.md)
- [用于 IoT 安全警报的 Azure 安全中心](concept-security-alerts.md)
