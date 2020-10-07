---
title: 快速入门：调查安全警报
description: 了解、深化和调查 IoT 设备上的 Defender for IoT 安全警报。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: 172ae82288c2cb948839b69955b9491715eb4690
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943346"
---
# <a name="quickstart-investigate-security-alerts"></a>快速入门：调查安全警报

对 Defender for IoT 发出的警报进行计划调查和修正是确保 IoT 解决方案中的合规性和防护性的最佳方法。

本快速入门将介绍每个 IoT 安全警报中可用的信息，并说明如何深化和使用每个警报和相关设备的详细信息来响应和修正。 

让我们开始吧。 


## <a name="investigate-new-security-alerts"></a>调查新的安全警报

IoT 中心安全警报列表显示 IoT 中心的所有聚合安全警报。 

1. 在 Azure 门户中，打开要调查是否存在新警报的“IoT 中心”。
1. 从“安全”菜单中，选择“警报” 。 IoT 中心的所有安全警报随即显示，带有“新”标志的警报标记过去 24 小时内的警报。
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="使用新的警报标志调查新的 IoT 安全警报":::
1. 从列表中选择并打开任何警报，可打开警报详细信息并深化了解警报细节。 

## <a name="security-alert-details"></a>安全警报详细信息

打开每个聚合警报将显示详细的警报描述、修正步骤、触发警报的每个设备的设备 ID，以及警报严重性和使用 Log Analytics 的直接调查访问。 

1. 在“IoT 中心” > “安全” > “警报”列中，选择并打开任何安全警报  。 
1. 查看警报“说明”、“严重性”、“检测源”，和在聚合期间发出此警报的所有设备的“设备详细信息”   。
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="使用新的警报标志调查新的 IoT 安全警报"::: 
1. 查看警报细节后，使用“手动修正步骤”说明可帮助修正和/或解决导致警报的问题。 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="使用新的警报标志调查新的 IoT 安全警报":::

1. 如果需要进一步调查，请使用此链接调查 Log Analytics 中的警报。 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="使用新的警报标志调查新的 IoT 安全警报":::

## <a name="next-steps"></a>后续步骤

转到下一篇文章，详细了解 Defender 警报类型和可能的自定义项…

> [!div class="nextstepaction"]
> [了解 IoT 安全警报](concept-security-alerts.md)
