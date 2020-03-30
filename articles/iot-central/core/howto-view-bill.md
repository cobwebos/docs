---
title: 在 Azure IoT 集中应用程序中管理账单并从免费定价计划进行转换 |微软文档
description: 作为管理员，了解如何管理账单，并在 Azure IoT 中央应用程序中从免费定价计划迁移到标准定价计划
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157483"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>在 IoT Central 应用程序中管理帐单

本文介绍作为管理员，如何在管理部分的 Azure IoT 集中应用程序中管理帐单。 您将了解如何将应用程序从免费定价计划移动到标准定价计划，以及如何升级或降级定价计划。

要访问和使用**管理**部分，您必须处于*管理员*角色中，或者具有允许您查看 Azure IoT 中央应用程序的计费的*自定义用户角色*。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”**** 角色。

## <a name="move-from-free-to-standard-pricing-plan"></a>从免费定价计划转向标准定价计划

- 使用免费定价计划的应用程序在过期前七天免费。 为了避免丢失数据，您可以在数据过期前随时将其移动到标准定价计划。
- 使用标准定价计划的应用程序按设备收费，每个应用程序前两台设备免费。

可在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上了解定价详细信息。

在定价部分中，您可以将应用程序从免费版移动到标准定价计划。

若要完成此自助服务过程，请遵循以下步骤：

1. 转到 **"管理**"部分中的 **"定价**"页面。

    ![试用状态](media/howto-view-bill/freetrialbilling.png)

1. 选择 **"转换为付费计划**"。

    ![转换试用版](media/howto-view-bill/convert.png)

1. 选择相应的 Azure 活动目录，然后选择 Azure 订阅，用于使用付费计划的应用程序。

1. 选择 **"转换"** 后，应用程序现在使用付费计划，然后开始计费。

> [!Note]
> 默认情况下，您将转换为*标准 2*定价计划。

## <a name="how-to-change-your-application-pricing-plan"></a>如何更改应用程序定价计划

使用标准定价计划的应用程序按设备收费，每个应用程序前两台设备免费。

在定价部分中，您可以随时升级或降级 Azure IoT 定价计划。

1. 转到 **"管理**"部分中的 **"定价**"页面。

    ![试用状态](media/howto-view-bill/pricing.png)

1. 选择 **"计划****"，然后单击"保存"** 以升级或降级。

## <a name="view-your-bill"></a>查看帐单

1. 选择相应的 Azure 活动目录，然后选择 Azure 订阅，用于使用付费计划的应用程序。

1. 选择 **"转换"** 后，应用程序现在使用付费计划，然后开始计费。

> [!Note]
> 默认情况下，您将转换为*标准 2*定价计划。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中管理帐单，建议下一步是了解如何在 Azure IoT Central 中[自定义应用程序 UI](howto-customize-ui.md)。