---
title: 组件&先决条件
description: 使用 Azure 安全中心开始使用 IoT 服务先决条件所需的一切详细信息。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: fb5c42ad490ed04f14ff150093a44b552434ed9e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310580"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>适用于 IoT 先决条件的 Azure 安全中心

本文介绍了 Azure IoT 服务安全中心的不同组件、需要开始的内容，并解释了帮助了解服务的基本概念。

## <a name="minimum-requirements"></a>最低要求

- IoT 中心标准层
  - RBAC 角色**所有者**级别权限
- [日志分析工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure 安全中心（推荐）
  - 使用 Azure 安全中心是一项建议，而不是一项要求。 如果没有 Azure 安全中心，您将无法查看 IoT 中心中的其他 Azure 资源。

## <a name="working-with-azure-security-center-for-iot-service"></a>使用 Azure IoT 服务安全中心

Azure 安全中心可用于 IoT 见解和报告，可通过 Azure IoT 中心和 Azure 安全中心提供。 要在 Azure IoT 中心启用 IoT 的 Azure 安全中心，需要具有**所有者**级别权限的帐户。 在 IoT 中心中为 IoT 启用 ASC 后，用于 IoT 见解的 Azure 安全中心将显示为 Azure IoT 中心**中的安全**功能和 Azure 安全中心的**IoT。**

## <a name="supported-service-regions"></a>支持的服务区域

IoT 的 Azure 安全中心当前支持以下 Azure 区域中的 IoT 中心：

- 美国中部
- 美国东部
- 美国东部 2
- 美国中西部
- 美国西部
- 美国西部 2
- 美国中南部
- 美国中北部
- 加拿大中部
- 加拿大东部
- 北欧
- 巴西南部
- 法国中部
- 英国西部
- 英国南部
- 西欧
- 北欧
- 日本西部
- 日本东部
- 澳大利亚东南部
- 澳大利亚东部
- 东亚
- 东南亚
- 韩国中部
- 韩国南部
- 印度中部
- 印度南部

IoT Azure 安全中心将所有流量从所有欧洲区域路由到西欧区域数据中心和所有剩余区域到美国中部区域数据中心。

## <a name="wheres-my-iot-hub"></a>我的 IoT 中心在哪里？

在开始之前，请检查 IoT 中心位置以验证服务可用性。

1. 打开 IoT 中心。
1. 单击“概述”****。
1. 验证列出的位置与[受支持的服务区域](#supported-service-regions)之一匹配。

## <a name="supported-platforms-for-agents"></a>支持的代理平台

IoT 代理的 Azure 安全中心支持越来越多的设备和平台列表。 请参阅[支持的平台列表](how-to-deploy-agent.md)以检查现有或计划的设备库。

## <a name="next-steps"></a>后续步骤

- 阅读 Azure IoT 安全[概述](overview.md)
- 了解如何[启用服务](quickstart-onboard-iot-hub.md)
- 阅读[Azure 安全中心了解 IoT 常见问题解答](resources-frequently-asked-questions.md)
- 了解如何了解[IoT 警报的 Azure 安全中心](concept-security-alerts.md)
