---
title: 在 Azure IoT Central 应用程序中管理帐单以及从免费定价计划进行转换 | Microsoft Docs
description: 作为管理员，学习如何在 Azure IoT Central 应用程序中管理帐单，以及从免费定价计划转移到标准定价计划
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80157483"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>在 IoT Central 应用程序中管理帐单

本文介绍了如何以管理员身份在 Azure IoT Central 应用程序中管理帐单。 你将了解如何将应用程序从免费定价计划转移到标准定价计划，以及如何升级或降级定价计划。

若要访问和使用“管理”  部分，必须具有“管理员”  角色，或者具有允许查看 Azure IoT Central 应用程序的帐单的*自定义用户角色*。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”  角色。

## <a name="move-from-free-to-standard-pricing-plan"></a>从免费定价计划转移到标准定价计划

- 使用免费定价计划的应用程序在七天内免费，然后将过期。 为了避免丢失数据，你可以在它们过期之前的任何时间将它们转移到标准定价计划。
- 使用标准定价计划的应用程序按设备收费，每个应用程序的前两个设备是免费的。

可在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上了解定价详细信息。

在定价部分中，你可以将应用程序从免费定价计划转移到标准定价计划。

若要完成此自助服务过程，请遵循以下步骤：

1. 在“管理”部分中转到“定价”页面。  

    ![试用状态](media/howto-view-bill/freetrialbilling.png)

1. 选择“转换为付费计划”  。

    ![转换试用版](media/howto-view-bill/convert.png)

1. 选择相应的 Azure Active Directory，然后选择要对使用付费计划的应用程序使用的 Azure 订阅。

1. 选择“转换”  后，你的应用程序现在将使用某个付费计划，并开始计费。

> [!Note]
> 默认情况下，将转换为“标准 2”  定价计划。

## <a name="how-to-change-your-application-pricing-plan"></a>如何更改应用程序定价计划

使用标准定价计划的应用程序按设备收费，每个应用程序的前两个设备是免费的。

在定价部分中，可以随时升级或降级 Azure IoT 定价计划。

1. 在“管理”部分中转到“定价”页面。  

    ![试用状态](media/howto-view-bill/pricing.png)

1. 选择“计划”  并单击“保存”  以进行升级或降级。

## <a name="view-your-bill"></a>查看帐单

1. 选择相应的 Azure Active Directory，然后选择要对使用付费计划的应用程序使用的 Azure 订阅。

1. 选择“转换”  后，你的应用程序现在将使用某个付费计划，并开始计费。

> [!Note]
> 默认情况下，将转换为“标准 2”  定价计划。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中管理帐单，建议下一步是了解如何在 Azure IoT Central 中[自定义应用程序 UI](howto-customize-ui.md)。