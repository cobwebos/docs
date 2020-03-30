---
title: 了解 Azure 安全中心，了解 IoT 成本*微软文档
description: 了解与 IoT Azure 安全中心关联的成本，以及如何控制这些成本。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "71348518"
---
# <a name="pricing-and-associated-costs"></a>定价和关联的成本

本文介绍了 IoT 定价模型的 Azure 安全中心，总结了所有相关成本，并解释了如何管理这些成本。

## <a name="pricing"></a>定价

IoT 定价的 Azure 安全中心由两部分组成，一旦在 Azure IoT 安全中心[中启用](quickstart-onboard-iot-hub.md)IoT 中心，将计费：

- 按设备计算的成本 - 基于 IoT 中心日志分析的内置安全功能。

- 按消息计算的成本 - 基于来自 IoT Edge 或叶设备的安全消息增强的安全功能。


有关详细信息，请参阅[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="associated-costs"></a>相关成本

IoT 的 Azure 安全中心具有相关成本，这些成本不是直接定价的一部分：


- 日志分析存储成本

您可以通过选择退出某些解决方案功能来降低相关成本。 通过更改设置选择退出。

要更改设置，

1. 打开 IoT 中心。

2. 在 **"安全"** 下，单击 **"概述**"。

3. 单击“设置”****。

下表概述了每个备选方案的相关费用和影响。

|     | 使用情况 | 注释 |
| --- | --- | --- |
| **日志分析存储** |  |
| 设备建议和警报| 服务生成的安全建议和警报 | 不是可选的 |
| 原始安全数据| 由安全代理收集的 IoT 设备的原始安全数据 | 禁用_存储原始设备安全事件_ |
|

>[!Important]
> 选择退出对 Azure 安全中心具有严重影响，这些安全中心具有 IoT 安全功能可用性。 
  
| 退出 | 含义 |
| --- | --- |
| _双元元集_ | 禁用[自定义警报](quickstart-create-custom-alerts.md) |
| | 禁用 IoT 边缘清单建议 |
| | 禁用基于设备标识的建议和警报 |
| _存储原始设备安全事件_ | 有关设备操作系统基线建议的详细信息不可用 |
| | 有关[警报](concept-security-alerts.md)[和建议调查](concept-recommendations.md)的详细信息，请提供 |
|


## <a name="see-also"></a>请参阅

- 访问[原始安全数据](how-to-security-data-access.md)
- [调查设备](how-to-investigate-device.md)
- 了解并探讨[安全建议](concept-recommendations.md)
- 了解和探索[安全警报](concept-security-alerts.md)
