---
title: 了解 Azure 安全中心的 IoT 成本 |Microsoft Docs
description: 了解与 Azure 安全中心 (IoT) 关联的成本, 以及如何对其进行控制。
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 603df1def011232ad2120c37ad1ba256f2a30526
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596518"
---
# <a name="pricing-and-associated-costs"></a>定价和关联的成本

本文介绍了 IoT 定价模型的 Azure 安全中心, 并汇总了所有相关的成本, 并说明了如何对其进行管理。

## <a name="pricing"></a>定价

用于 IoT 定价模型的 Azure 安全中心由两部分组成, 一旦在 Azure 安全中心为 IoT[启用](quickstart-onboard-iot-hub.md)了 iot 中心, 就会按计费:

- 基于 IoT 中心日志分析的设备的成本-内置安全功能。

- 通过消息增强的安全功能基于 IoT Edge 或叶设备上的安全消息来收费。

  >[!Note]
  > 安全消息还会在 IoT 中心产生配额消耗。

有关详细信息, 请参阅[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="associated-costs"></a>关联成本

用于 IoT 的 Azure 安全中心具有两种类型的关联成本, 这些成本不是直接定价的一部分:

- IoT 中心配额消耗

- Log Analytics 存储成本

您可以通过更改设置来减少某些功能, 从而减少相关的成本。

若要更改设置:

1. 打开 IoT 中心。

2. 在 "**安全**" 下, 单击 "**概述**"。

3. 单击“设置”。

下表提供了每个选项的相关成本和影响的摘要。

|     | 用法 | 注释 |
| --- | --- | --- |
| **IoT 中心配额消耗** |  |
| [导出设备](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices)作业 (克隆导出) | 每天 1 次 | 禁用克隆的_元数据集合_ |
| **Log Analytics 存储** |  |
| 设备建议和警报| 服务生成的安全建议和警报 | 不可选 |
| 原始安全数据| 由安全代理收集的 IoT 设备的原始安全数据 | 禁用_存储原始设备安全事件_ |

>[!Important]
> 选择退出会对可用的安全功能产生严重影响。
  
| 选择禁用 | 深远 |
| --- | --- |
| _克隆的元数据集合_ | 禁用[自定义警报](quickstart-create-custom-alerts.md) |
| | 禁用 IoT Edge 清单建议 |
| | 禁用基于设备标识的建议和警报 |
| _存储原始设备安全事件_ | 有关设备操作系统基准建议的详细信息不可用 |
| | 有关[警报](concept-security-alerts.md)和[建议](concept-recommendations.md)调查的详细信息不可用 |
|


## <a name="see-also"></a>请参阅

- 访问[原始安全数据](how-to-security-data-access.md)
- [调查设备](how-to-investigate-device.md)
- 了解并探索[安全性建议](concept-recommendations.md)
- 了解并探索[安全警报](concept-security-alerts.md)
