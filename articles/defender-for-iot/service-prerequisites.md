---
title: 组件 & 必备组件
description: Azure Defender for IoT 服务先决条件入门所需的所有内容的详细信息。
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
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933476"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Azure Defender for IoT 必备组件

本文介绍 IoT 服务的 Defender 的不同组件、需要开始的内容，并说明有助于了解服务的基本概念。

## <a name="minimum-requirements"></a>最低要求

- IoT 中心标准层
  - Azure 角色 **所有者** 级别权限
- [Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure 安全中心 (推荐) 
  - 使用 Azure 安全中心是一种建议，不是必需的。 如果没有 Azure 安全中心，你将无法在 IoT 中心内查看其他 Azure 资源。

## <a name="working-with-defender-for-iot-service"></a>使用用于 IoT 的 Defender 服务

使用 Azure IoT 中心和 Azure 安全中心可获取 IoT insights 和报告的 Defender。 若要在 Azure IoT 中心启用 Defender for IoT，需要具有 **所有者** 级别权限的帐户。 在 IoT 中心为 IoT 启用 ASC 后，将在 Azure IoT 中心 **中将 Defender** 用于 iot insights，并在 Azure 安全中心中显示为  **iot** 。

## <a name="supported-service-regions"></a>支持的服务区域

以下 Azure 区域中的 IoT 中心目前支持用于 IoT 的 Defender：

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
- Japan East
- Australia Southeast
- 澳大利亚东部
- 东亚
- 东南亚
- 韩国中部
- 韩国南部
- 印度中部
- 印度南部

Defender for IoT 将所有欧洲地区的所有流量路由到西欧的区域数据中心，并将所有剩余区域路由到美国中部区域数据中心。

## <a name="wheres-my-iot-hub"></a>我的 IoT 中心在哪里？

在开始之前，请查看 IoT 中心位置以验证服务可用性。

1. 打开 IoT 中心。
1. 单击“概览”。 
1. 验证列出的位置是否与其中一个 [受支持的服务区域](#supported-service-regions)匹配。

## <a name="supported-platforms-for-agents"></a>支持的代理平台

用于 IoT 代理的 Defender 支持不断增长的设备和平台列表。 请参阅 [支持的平台列表](how-to-deploy-agent.md) ，查看现有或计划的设备库。

## <a name="next-steps"></a>后续步骤

- 阅读 Azure IoT 安全 [概述](overview.md)
- 了解如何 [启用该服务](quickstart-onboard-iot-hub.md)
- 阅读 [用于 IoT 的 DEFENDER 常见问题解答](resources-frequently-asked-questions.md)
- 了解如何 [了解用于 IoT 警报的 Defender](concept-security-alerts.md)
