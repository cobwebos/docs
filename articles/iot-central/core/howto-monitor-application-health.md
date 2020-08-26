---
title: 监视 Azure IoT Central 应用程序的运行状况 |Microsoft Docs
description: 作为操作员或管理员，监视连接到 IoT Central 应用程序的设备的总体运行状况。
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d246adbf8e87d5503bc19bcd497dd00370c0a867
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001894"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>监视连接到 IoT Central 应用程序的设备的总体运行状况

> [!NOTE]
> 指标仅适用于版本 3 IoT Central 应用程序。 若要了解如何检查您的应用程序版本，请参阅[关于您的应用程序](./howto-get-app-info.md)。

*本文适用于操作员和管理员。*

本文介绍如何使用 IoT Central 提供的指标集来评估连接到 IoT Central 应用程序的设备的总体运行状况。

默认情况下，对 IoT Central 应用程序启用指标，并从[Azure 门户](https://portal.azure.com/)访问它们。 [Azure Monitor 的数据平台公开了这些指标](../../azure-monitor/platform/data-platform-metrics.md)，并提供了几种方式来与它们进行交互。 例如，你可以在 Azure 门户中使用图表，或在 PowerShell 中使用 REST API 或查询，或者 Azure CLI。

### <a name="trial-applications"></a>试用应用程序

使用免费试用计划的应用程序没有关联的 Azure 订阅，因此不支持 Azure Monitor 度量值。 你可以[将应用程序转换为标准定价计划](./howto-view-bill.md#move-from-free-to-standard-pricing-plan)并访问这些指标。

## <a name="view-metrics-in-the-azure-portal"></a>查看 Azure 门户中的度量值

以下步骤假设你有一个[IoT Central 应用程序](./quick-deploy-iot-central.md)与一些[连接的设备](./tutorial-connect-device-nodejs.md)。

若要在门户中查看 IoT Central 度量值：

1. 在门户中导航到 IoT Central 应用程序资源。 默认情况下，IoT Central 资源位于名为**IOTC**的资源组中。
1. 若要从应用程序的指标创建图表，请在 "**监视**" 部分中选择 "**指标**"。

![Azure 指标](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure 门户权限

对 Azure 门户中的度量值的访问由[Azure 基于角色的访问控制](../../role-based-access-control/overview.md)进行管理。 使用 Azure 门户将用户添加到 IoT Central 应用程序/资源组/订阅中，以向他们授予访问权限。 即使已将用户添加到 IoT Central 应用程序，也必须在门户中添加该用户。 使用[Azure 内置角色](../../role-based-access-control/built-in-roles.md)以获得更精细的访问控制。

## <a name="iot-central-metrics"></a>IoT Central 度量值

有关 IoT Central 当前可用指标的列表，请参阅[支持的指标和 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftiotcentraliotapps)。

### <a name="metrics-and-invoices"></a>指标和发票

指标可能不同于 Azure IoT Central 发票上显示的数字。 出现这种情况的原因有很多，例如：

- IoT Central[标准定价计划](https://azure.microsoft.com/pricing/details/iot-central/)包括两个设备，并为免费提供不同的消息配额。 虽然免费项目不会从帐单中排除，但仍会将其计入指标中。

- IoT Central 自动生成应用程序中每个设备模板的一个测试设备 ID。 此设备 ID 显示在设备模板的 "**管理测试设备**" 页上。 解决方案构建者可以通过生成使用这些测试设备 Id 的代码，选择[验证其设备模板](./overview-iot-central.md#create-device-templates)，然后再进行发布。 尽管从帐单中排除了这些设备，但这些设备仍会计入指标。

- 虽然度量值可能会显示从设备到云的通信子集，但设备和云之间的所有通信均[计为计费消息](https://azure.microsoft.com/pricing/details/iot-central/)。

## <a name="next-steps"></a>后续步骤

了解如何使用应用程序模板后，建议接下来了解如何[在 Azure 门户中管理 IoT Central](howto-manage-iot-central-from-portal.md)
