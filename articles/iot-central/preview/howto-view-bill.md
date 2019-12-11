---
title: 管理帐单并将试用版转换为 Azure IoT Central 应用程序中的即用即付 |Microsoft Docs
description: 以管理员身份了解如何管理你的帐单，并从试用版中转换为你在 Azure IoT Central 应用程序中的使用
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1c1643fb8c8394ce3ce50e5926f05fd4d366f6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976411"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>在 IoT Central 应用程序中管理你的帐单

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文介绍如何以管理员身份在 "管理" 部分中的 "Azure IoT Central 应用程序" 中管理帐单，以及如何将试用版转换为即用即付。

只有 Azure IoT Central 应用程序的“管理员”角色才能访问和使用“管理”部分。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”角色。

## <a name="view-your-bill"></a>查看帐单

若要查看帐单，请在“管理”部分转到“计费”页。 Azure 计费页将在新选项卡中打开，可以在此处看到每个 Azure IoT Central 应用程序的帐单。

## <a name="convert-your-trial-to-pay-as-you-go"></a>将试用版转换为即用即付

- **试用版**应用程序免费 7 天，然后过期。 它们可以在到期之前随时转换为即用即付。
- 即用**即付**应用程序按设备收费，每个订阅的前五个设备可用。

可在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上了解定价详细信息。

在 "计费" 部分，可以将试用应用程序转换为即用即付。

若要完成此自助服务过程，请遵循以下步骤：

1. 在“管理”部分转到“计费”页。

    ![试用状态](media/howto-view-bill/freetrialbilling.png)

1. 选择 "**转换为即用即付**"。

    ![转换试用版](media/howto-view-bill/convert.png)

1. 选择相应的 Azure Active Directory，然后选择要对即用即付应用程序使用的 Azure 订阅。

1. 选择 "**转换**" 后，你的应用程序现在是即用即付应用程序，你将开始计费。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中管理帐单，下一步是了解如何在 Azure IoT Central 中[自定义应用程序 UI](howto-customize-ui.md) 。