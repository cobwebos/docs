---
title: 路由的车辆消耗模型 |Microsoft Azure 映射
description: 在本文中，你将了解 Microsoft Azure 地图中路由的车辆消耗模型。
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190250"
---
# <a name="consumption-model"></a>消耗模型

路由服务提供了一组用于特定于车辆的消耗模型详细说明的参数。
根据 vehicleEngineType 的值，支持以下两种主要消耗模型：燃烧和电气。 指定属于同一请求中不同模型的参数是不正确的。 此外，消耗模型参数不能与以下**travelMode**值一起使用：_自行车_和_行人_。

## <a name="parameter-constraints-for-consumption-model"></a>消耗模型的参数约束

在这两种消耗模型中，指定参数时有一些依赖关系。 这意味着，显式指定某些参数可能需要指定其他一些参数。 下面是要注意的这些依赖项：

* 所有参数均要求用户指定 constantSpeedConsumption。 如果未指定**constantSpeedConsumption** ，则指定任何其他消耗模型参数是错误的。 **VehicleWeight**参数是此要求的例外情况。
* **accelerationEfficiency**和**decelerationEfficiency**必须始终指定为成对（即，或 none）。
* 如果指定了 accelerationEfficiency 和 decelerationEfficiency，两者值的乘积不得大于 1（防止出现永恒运动）。
* **uphillEfficiency**和**downhillEfficiency**必须始终指定为成对（即，或 none）。
* 如果指定了 uphillEfficiency 和 downhillEfficiency，两者值的乘积不得大于 1（防止出现永恒运动）。
* 如果用户指定了 \*Efficiency 参数，还必须指定“vehicleWeight”参数。 如果 vehicleEngineType 为燃料，则还必须指定 fuelEnergyDensityInMJoulesPerLiter。
* **maxChargeInkWh**和**currentChargeInkWh**必须始终指定为成对（即，或 none）。

> [!NOTE]
> 如果仅指定了 constantSpeedConsumption，消耗计算不会考虑其他任何消耗（如爬坡和车辆加速）。

## <a name="combustion-consumption-model"></a>燃料消耗模型

如果将 vehicleEngineType 设置为“燃烧”，则使用燃烧消耗模型。
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

如果将 vehicleEngineType 设置为“电气”，则使用电气消耗模型。
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

可以拒绝一组特定的消耗参数，即使该集可能满足所有显式要求。 如果特定参数的值或多个参数的值的组合导致使用值的度不合理，则会发生这种情况。 如果出现此情况，则很可能是存在输入错误，应当注意调整所有合理的消耗参数值。 如果一组特定消耗参数遭到拒绝，随之而来的错误消息会解释其原因。
参数的详细说明中包含两种模型的合理值的示例。
