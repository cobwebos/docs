---
title: 从 Azure IoT Central 规则运行多个操作 | Microsoft Docs
description: 从单个 IoT Central 规则运行多个操作，并创建可从多个规则运行的可重用操作组。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80157681"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>将多个操作分组以从一个或多个规则运行

*本文面向构建人员和管理员。*

在 Azure IoT Central 中可以创建规则，以便在满足条件时运行操作。 规则基于设备遥测数据或事件。 例如，当设备温度超过阈值时，可以通知操作员。 本文介绍如何使用 [Azure Monitor](../../azure-monitor/overview.md) 操作组将多个操作附加到 IoT Central 规则。  可将一个操作组附加到多个规则。 [操作组](../../azure-monitor/platform/action-groups.md)是由 Azure 订阅的所有者定义的通知首选项的集合。

## <a name="prerequisites"></a>先决条件

- 使用标准定价计划创建的应用程序
- 用于创建和管理 Azure Monitor 操作组的 Azure 帐户与订阅

## <a name="create-action-groups"></a>创建操作组

可以 [在 Azure 门户中创建和管理操作组](../../azure-monitor/platform/action-groups.md)，也可以使用 [Azure 资源管理器模板](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)执行这些操作。

操作组可以：

- 发送电子邮件、短信等通知，或拨打语音电话。
- 运行某个操作，例如调用 Webhook。

以下屏幕截图显示了一个可以发送电子邮件和短信通知以及调用 Webhook 的操作组：

![操作组](media/howto-use-action-groups/actiongroup.png)

若要在 IoT Central 规则中使用某个操作组，该操作组必须与 IoT Central 应用程序位于同一 Azure 订阅中。

## <a name="use-an-action-group"></a>使用操作组

若要在 IoT Central 应用程序中使用操作组，请先创建一个规则。 将操作添加到规则时，请选择“Azure Monitor 操作组”： 

![选择操作](media/howto-use-action-groups/chooseaction.png)

从 Azure 订阅中选择操作组：

![选择操作组](media/howto-use-action-groups/chooseactiongroup.png)

选择“保存”  。 该操作组随即显示在触发规则时要运行的操作列表中：

![保存的操作组](media/howto-use-action-groups/savedactiongroup.png)

下表汇总了发送到受支持操作类型的信息：

| 操作类型 | 输出格式 |
| ----------- | -------------- |
| Email       | 标准 IoT Central 电子邮件模板 |
| SMS         | Azure IoT Central alert: ${applicationName} - "${ruleName}" triggered on "${deviceName}" at ${triggerDate} ${triggerTime} |
| 语音       | Azure I.O.T Central alert: rule "${ruleName}" triggered on device "${deviceName}" at ${triggerDate} ${triggerTime}, in application ${applicationName} |
| Webhook     | { "schemaId" :"AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](howto-create-webhooks.md#payload)}} |

以下文本是来自操作组的示例短信：

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>后续步骤

了解如何在规则中使用操作组后，建议接下来了解如何[管理设备](howto-manage-devices.md)。
