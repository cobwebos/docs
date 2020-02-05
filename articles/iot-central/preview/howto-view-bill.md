---
title: 在 Azure IoT Central 应用程序中管理帐单并从免费定价计划转换 |Microsoft Docs
description: 以管理员身份了解如何管理帐单并从免费定价计划移到 Azure IoT Central 应用程序中的标准定价计划
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: adc7b59eebf513042667c0c585c05307aeab07b2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990841"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>在 IoT Central 应用程序中管理你的帐单

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文介绍如何以管理员身份在 "管理" 部分中管理 Azure IoT Central 应用程序中的帐单。 你将了解如何将应用程序从免费定价计划转移到标准定价计划，以及如何升级或降级定价计划。

若要访问和使用 "**管理**" 部分，你必须是 "*管理员*" 角色或拥有允许查看 Azure IoT Central 应用程序计费的*自定义用户角色*。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”角色。

## <a name="move-from-free-to-standard-pricing-plan"></a>从免费到标准定价计划移动

- 使用免费定价计划的应用程序在过期前7天免费。 为了避免丢失数据，您可以在任何时间在过期之前将它们移到标准定价计划。

在 "定价" 部分中，你可以将应用程序从免费层移到标准定价计划。

若要完成此自助服务过程，请遵循以下步骤：

1. 请在 "**管理**" 部分中转到 "**定价**" 页。

    ![试用状态](media/howto-view-bill/freetrialbilling.png)

1. 选择 "**转换为付费计划**"。

    ![转换试用版](media/howto-view-bill/convert.png)

1. 选择相应的 Azure Active Directory，然后选择要用于应用程序的 Azure 订阅，该应用程序使用付费计划。

1. 选择 "**转换**" 后，你的应用程序现在使用付费计划，并且你开始付费。

> [!Note]
> 默认情况下，将转换为*标准 2*定价计划。

## <a name="how-to-change-your-application-pricing-plan"></a>如何更改应用程序定价计划

- 使用标准定价计划的应用程序按设备收费，每个应用程序的前两个设备是免费的。

在 "定价" 部分中，可以随时升级或降级 Azure IoT 定价计划。

1. 请在 "**管理**" 部分中转到 "**定价**" 页。

    ![试用状态](media/howto-view-bill/pricing.png)

1. 选择**计划**，然后单击 "**保存**" 进行升级或降级。

可在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上了解定价详细信息。

## <a name="view-your-bill"></a>查看帐单

若要查看帐单，请在 "**管理**" 部分中，请参阅**定价**页。 此时将在新选项卡中打开 "Azure 定价" 页，可在其中查看每个 Azure IoT Central 应用程序的帐单。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中管理帐单，下一步是了解如何在 Azure IoT Central 中[自定义应用程序 UI](howto-customize-ui.md) 。