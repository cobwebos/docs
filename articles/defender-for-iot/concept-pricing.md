---
title: 定价和关联的成本
description: 了解与用于 IoT 的 Defender 相关的成本，以及如何对其进行控制。
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
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 24ae6c4014948639aa737a0d2d88ec15f98a7cb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934485"
---
# <a name="pricing-and-associated-costs"></a>定价和关联的成本

本文介绍 IoT 定价模型 Defender，并概述所有相关的成本，并说明如何管理它们。

## <a name="pricing"></a>定价

适用于 iot 的 Defender 定价模型由两部分组成，在 Defender 中为 IoT [启用](quickstart-onboard-iot-hub.md) iot 中心后会计费：

- 基于 IoT 中心日志分析的设备的成本-内置安全功能。

- 通过消息增强的安全功能基于 IoT Edge 或叶设备上的安全消息来收费。

有关详细信息，请参阅 [安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="associated-costs"></a>关联成本

用于 IoT 的 Defender 具有相关成本，但不属于直接定价：

- Log Analytics 存储成本

您可以通过选择退出某些解决方案功能来减少相关成本。 通过更改设置选择退出。

若要更改设置：

1. 打开 IoT 中心。

1. 在 " **安全**" 下，单击 " **设置**"。

1. 单击 " **数据收集**"。

下表提供了每个选项的相关成本和影响的摘要。

| 选项 | 使用情况 | 注释 |
| --- | --- | --- |
| **Log Analytics 存储** |  |
| 设备建议和警报| 服务生成的安全建议和警报 | 不可选 |
| 原始安全数据| 由安全代理收集的 IoT 设备的原始安全数据 | 禁用 _存储原始设备安全事件_ |
|

>[!Important]
> 选择退出对于 IoT 安全功能可用性有严重影响。

| 选择退出 | 含义 |
| --- | --- |
| _克隆的元数据集合_ | 禁用 [自定义警报](quickstart-create-custom-alerts.md) |
| | 禁用 IoT Edge 清单建议 |
| | 禁用基于设备标识的建议和警报 |
| _存储原始设备安全事件_ | 有关设备操作系统基准建议的详细信息不可用 |
| | 有关 [警报](concept-security-alerts.md) 和 [建议](concept-recommendations.md) 调查的详细信息不可用 |
|

## <a name="see-also"></a>另请参阅

- 访问 [原始安全数据](how-to-security-data-access.md)
- [调查设备](how-to-investigate-device.md)
- 了解并探索 [安全性建议](concept-recommendations.md)
- 了解并探索 [安全警报](concept-security-alerts.md)
