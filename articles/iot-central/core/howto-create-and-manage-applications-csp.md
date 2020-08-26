---
title: 从 CSP 门户创建和管理 Azure IoT Central 应用程序 | Microsoft Docs
description: 作为 CSP，如何代表客户创建 Azure IoT Central 应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 02481d5dcbaba15c9b17a27348207d9af64f3355
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982032"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>从 CSP 门户创建和管理 Azure IoT Central 应用程序

Microsoft 云解决方案提供商 (CSP) 计划是 Microsoft 经销商计划。 其目的是为我们的渠道合作伙伴提供一站式计划，以转售所有 Microsoft Commercial Online Services。 详细了解[云解决方案提供商计划](https://partner.microsoft.com/cloud-solution-provider)。

作为 CSP，可以通过 [Microsoft 合作伙伴中心](https://partnercenter.microsoft.com/partner/home)代表客户创建和管理 Microsoft Azure IoT Central 应用程序。 当 CSP 代表客户创建 Azure IoT Central 应用程序时，就像其他 CSP 托管的 Azure 服务一样，CSP 管理客户的计费。 Azure IoT Central 的费用将显示在 Microsoft 合作伙伴中心的总账单中。

若要开始，请在 Microsof 合作伙伴门户上登录你的帐户，然后选择要为其创建 Azure IoT Central 应用程序的客户。 从左侧导航栏导航到客户的服务管理。

![Microsoft 合作伙伴中心, 客户视图](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central 将列为可用于管理的服务。 选择页面上的 Azure IoT Central 链接以为该客户创建新应用程序或管理现有应用程序。

![可用于管理的 Azure IoT Central](media/howto-create-and-manage-applications-csp/image2.png)

登陆 Azure IoT Central 的“应用程序管理器”页。 Azure IoT Central 保留你来自 Microsoft 合作伙伴中心的上下文，并且你来管理该特定客户。 你会在“应用程序管理器”页的标题中看到此确认。 从这里，你可以导航到之前为该客户创建的现有应用程序来管理它或为客户创建新的应用程序。

![创建适用于 CSP 的管理器](media/howto-create-and-manage-applications-csp/image3.png)

若要创建 Azure IoT Central 应用程序，请在左侧菜单中选择“生成”  。 选择一个行业模板，或选择“自定义应用”**** 从头开始创建应用程序。 这将加载“应用程序创建”页。 必须完成此页上的所有字段，然后选择“创建”。  你将找到有关以下每个字段的详细信息。

![适用于 CSP 的“创建应用程序”页](media/howto-create-and-manage-applications-csp/image4.png)

![适用于 CSP 的“创建应用程序”页](media/howto-create-and-manage-applications-csp/image4-1.png)

![适用于云解决方案提供商的“创建应用程序”页 - 计费信息](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>定价计划

你作为云解决方案提供商只能创建使用标准定价计划的应用程序。 若要向客户展示 Azure IoT Central，可以单独创建一个使用免费定价计划的应用程序。 在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上详细了解免费和标准定价计划。

你作为云解决方案提供商只能创建使用标准定价计划的应用程序。 若要向客户展示 Azure IoT Central，可以单独创建一个使用免费定价计划的应用程序。 在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上详细了解免费和标准定价计划。

## <a name="application-name"></a>应用程序名称

应用程序的名称显示在“应用程序管理器”页上和每个 Azure IoT Central 应用程序中。  可以为 Azure IoT Central 应用程序选择任意名称。 选择一个在自己和组织中的其他人看来合适的名称。

## <a name="application-url"></a>应用程序 URL

应用程序 URL 是应用程序的链接。 可以在浏览器中保存其书签或将其与他人共享。

输入应用程序的名称时，系统会自动创建应用程序 URL。 可以根据需要为应用程序选择另一个 URL。 每个 Azure IoT Central URL 在 Azure IoT Central 中必须唯一。 如果所选 URL 已被使用，则会看到一条错误消息。

## <a name="directory"></a>目录

由于 Azure IoT Central 有你用于管理已在 Microsoft 合作伙伴门户中选择的客户的上下文，因此在“目录”字段中只显示该客户的 Azure Active Directory 租户。 

Azure Active Directory 租户包含用户标识、凭据和其他组织信息。 可能会有多个 Azure 订阅与单个 Azure Active Directory 租户相关联。

有关详细信息，请参阅 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)。

## <a name="azure-subscription"></a>Azure 订阅

有了 Azure 订阅，就可以创建 Azure 服务的实例。 Azure IoT Central 会自动查找你有权访问的客户的所有 Azure 订阅，并将其显示在“创建应用程序”页的下拉列表中。  选择用于创建新 Azure IoT Central 应用程序的 Azure 订阅。

如果没有 Azure 订阅，则可在 Microsoft 合作伙伴中心创建一个。 创建 Azure 订阅以后，请导航回“创建应用程序”页。  新订阅显示在“Azue 订阅”  下拉列表中。

若要了解详细信息，请参阅 [Azure 订阅](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)。

## <a name="location"></a>位置

**Location**是要在其中创建应用程序的[地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置。 通常，应选择物理上离设备最近的位置以获得最佳性能。 目前，可以在**澳大利亚**、**亚太**、**欧洲**、**美国**、**英国**和**日本**地区创建 IoT Central 应用程序。 选择一个位置后，之后便不能将应用程序移到其他位置。

## <a name="application-template"></a>应用程序模板

选择要用于应用程序的应用程序模板。

## <a name="next-steps"></a>后续步骤

了解如何以 CSP 身份创建 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)
