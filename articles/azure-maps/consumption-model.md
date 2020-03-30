---
title: 用于布线的车辆消耗模型 |微软 Azure 地图
description: 在本文中，您将了解用于在 Microsoft Azure 地图中路由的车辆消耗模型。
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190250"
---
# <a name="consumption-model"></a>消耗模型

路由服务提供一组参数，用于详细描述车辆特定的消耗模型。
根据 vehicleEngineType 的值，支持以下两种主要消耗模型：燃烧和电气****____。 在同一请求中指定属于不同模型的参数是不对的。 此外，消费模型参数不能与以下**行驶模式**值一起使用：_自行车_和_行人_。

## <a name="parameter-constraints-for-consumption-model"></a>消耗模型的参数约束

在这两个消耗模型中，指定参数时都有一些依赖关系。 这意味着，显式指定某些参数可能需要指定一些其他参数。 以下是需要注意的这些依赖项：

* 所有参数均要求用户指定 constantSpeedConsumption****。 如果未指定**常量速度消耗**，则指定任何其他消耗模型参数是错误的。 **车辆重量**参数是此要求的例外。
* **加速度效率和****减速效率**必须始终指定为对（即两者或无）。
* 如果指定了 accelerationEfficiency 和 decelerationEfficiency，两者值的乘积不得大于 1（防止出现永恒运动）********。
* **上坡效率和****下坡效率**必须始终指定为对（即两者或无）。
* 如果指定了 uphillEfficiency 和 downhillEfficiency，两者值的乘积不得大于 1（防止出现永恒运动）********。
* 如果用户指定了 \*Efficiency 参数，还必须指定“vehicleWeight”参数____****。 如果 vehicleEngineType 为燃料，则还必须指定 fuelEnergyDensityInMJoulesPerLiter****__****。
* **maxChargeInkWh**和**电流 ChargeInkWh**必须始终指定为对（即两者或无）。

> [!NOTE]
> 如果仅指定了 constantSpeedConsumption，消耗计算不会考虑其他任何消耗（如爬坡和车辆加速）****。

## <a name="combustion-consumption-model"></a>燃料消耗模型

如果将 vehicleEngineType 设置为“燃烧”，则使用燃烧消耗模型****__。
下面是属于此模型的参数列表。 参阅“参数”部分，获取详细说明。

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>电力消耗模型

如果将 vehicleEngineType 设置为“电气”，则使用电气消耗模型****__。
下面是属于此模型的参数列表。 参阅“参数”部分，获取详细说明。

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>合理的消耗参数值

可以拒绝一组特定的消耗参数，即使该参数集可能满足所有显式要求。 当特定参数的值或多个参数的值的组合被视为导致不合理的消耗值量级时，就会发生这种情况。 如果出现此情况，则很可能是存在输入错误，应当注意调整所有合理的消耗参数值。 如果一组特定消耗参数遭到拒绝，随之而来的错误消息会解释其原因。
参数的详细说明中包含两种模型的合理值的示例。
