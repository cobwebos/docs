---
title: 创建 Azure IoT Central 应用程序 | Microsoft Docs
description: 如何以管理员身份创建 Azure IoT Central 应用程序。
author: tbhagwat3
ms.author: tanmayb
ms.date: 03/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 39992a1cc36d00a64ee6430cad5f24af3e1e1157
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629280"
---
# <a name="create-your-azure-iot-central-application"></a>创建 Azure IoT Central 应用程序

可以在[创建应用程序](https://apps.microsoftiotcentral.com/create)页中创建 Microsoft Azure IoT Central 应用程序。 若要创建 Azure IoT Central 应用程序，必须完成此页上的所有字段，然后选择“创建”。 本文详述了每个字段。

![“创建应用程序”页](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>付款计划

可以创建试用或付费应用程序。 详细了解此页上的试用和付费应用程序。

## <a name="application-name"></a>应用程序名称

应用程序的名称显示在“应用程序管理器”页上和每个 Azure IoT Central 应用程序中。 可以为 Azure IoT Central 应用程序选择任意名称。 选择一个在自己和组织中的其他人看来合适的名称。

## <a name="application-url"></a>应用程序 URL

应用程序 URL 是应用程序的链接。 可以在浏览器中保存其书签或将其与他人共享。

输入应用程序的名称时，系统会自动创建应用程序 URL。 可以根据需要为应用程序选择另一个 URL。 每个 Azure IoT Central URL 必须唯一。 如果所选 URL 已被使用，则会看到一条错误消息。

## <a name="directory"></a>Directory

仅存在于付费应用程序中。

选择用于创建 Azure IoT Central 应用程序的 Azure Active Directory 租户。 Azure Active Directory 租户包含用户标识、凭据和其他组织信息。 可能会有多个 Azure 订阅与单个 Azure Active Directory 租户相关联。

如果没有 Azure Active Directory 租户，则在创建 Azure 订阅时，系统会为你创建一个。

有关详细信息，请参阅 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)。

## <a name="azure-subscription"></a>Azure 订阅

有了 Azure 订阅，就可以创建 Azure 服务的实例。 Azure IoT Central 自动查找你有权访问的所有 Azure 订阅，并将其显示在“创建应用程序”页的下拉列表中。 选择用于创建新 Azure IoT Central 应用程序的 Azure 订阅。

如果没有 Azure 订阅，则可在此页创建一个。 创建 Azure 订阅以后，请导航回“创建应用程序”页。 新订阅显示在“Azue 订阅”下拉列表中。

若要了解详细信息，请参阅 [Azure 订阅](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)。

## <a name="region"></a>区域

仅存在于付费应用程序中。

选择要在其中创建 Azure IoT Central 应用程序的区域。 通常应选择在地理上最靠近设备的区域，以便优化性能。

若要了解详细信息，请参阅 [Azure 区域](https://docs.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#azure-regions)。

可以在[可用产品(按区域)](https://azure.microsoft.com/regions/services/)页面上查看提供 Azure IoT Central 的区域。

> [!Note]
> 选择一个区域后，就不能在以后将应用程序移到其他区域。

## <a name="application-template"></a>应用程序模板

可以为新的 Azure IoT Central 应用程序选择一个可用的应用程序模板。 应用程序模板可能包含入门所需的预定义项目（例如设备模板和仪表板）：

| 应用程序模板 | 说明 |
| -------------------- | ----------- |
| 自定义应用程序   | 创建一个空的应用程序，以便在其中填充你自己的设备模板和设备。 |
| 示例 Contoso       | 创建一个应用程序，其中包含用于简单的已连接设备的设备模板。 通过此模板来完成 Azure IoT Central 的入门。 |
| 示例 Devkit       | 创建一个应用程序，其中的设备模板可以用来连接 MXChip 或 Raspberry Pi 设备。 如果你是在这其中的某个设备上进行代码试验的设备开发人员，请使用此模板。 |

## <a name="next-steps"></a>后续步骤

了解如何创建 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)