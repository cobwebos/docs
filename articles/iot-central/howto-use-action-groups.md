---
title: 从 Azure IoT Central 规则运行多个操作 |Microsoft Docs
description: 从一个 IoT 中心的规则，运行多个操作并创建可重复使用可以从多个规则运行的操作组。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522888"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>若要从一个或多个规则运行的多个操作进行分组

*本文适用于构建者和管理员。*

在 Azure IoT Central 创建规则，以满足某个条件时运行的操作。 规则基于设备遥测数据或事件。 例如，可以在设备温度超过阈值时通知操作员。 本文介绍如何使用[Azure Monitor](../azure-monitor/overview.md) *操作组*将附加到 IoT 中心规则的多个操作。 可以将操作组附加到多个规则。 [操作组](../azure-monitor/platform/action-groups.md)是由 Azure 订阅的所有者定义的通知首选项的集合。

## <a name="prerequisites"></a>必备组件

- 即用即付应用程序
- Azure 帐户和订阅来创建和管理 Azure Monitor 操作组

## <a name="create-action-groups"></a>创建操作组

你可以[创建和管理在 Azure 门户中的操作组](../azure-monitor/platform/action-groups.md)或使用[Azure 资源管理器模板](../azure-monitor/platform/action-groups-create-resource-manager-template.md)。

操作组可以：

- 发送通知，如电子邮件、 短信，或进行语音呼叫。
- 运行操作，例如调用 webhook。

下面的屏幕截图显示了一个操作组发送电子邮件和短信通知和调用 webhook:

![操作组](media/howto-use-action-groups/actiongroup.png)

若要在 IoT Central 规则中使用操作组，IoT Central 应用程序与同一 Azure 订阅中必须是操作组。

## <a name="use-an-action-group"></a>使用操作组

若要在 IoT Central 应用程序中使用操作组，首先创建一个遥测数据或事件规则。 在将操作添加到规则中，选择**Azure 监视器操作组**:

![选择操作](media/howto-use-action-groups/chooseaction.png)

从你的 Azure 订阅中选择一个操作组：

![选择操作组](media/howto-use-action-groups/chooseactiongroup.png)

选择“其他安全性验证” 。 操作组现在显示在运行时触发此规则的操作列表中：

![保存操作组](media/howto-use-action-groups/savedactiongroup.png)

下表总结了发送给支持的操作类型的信息：

| 操作类型 | 输出格式 |
| ----------- | -------------- |
| 电子邮件       | 标准 IoT 中心电子邮件模板 |
| 短信         | Azure IoT Central 警报: ${applicationName} 的"${ruleName}"上"${deviceName}"${triggerDate} ${triggerTime} 在触发 |
| 语音       | Azure I.O.T Central 警报： 应用程序 ${applicationName} 中的规则"${ruleName}"${triggerDate} ${triggerTime} 的设备"${deviceName}"上触发 |
| Webhook     | {"schemaId":"AzureIoTCentralRuleWebhook"，"数据": {[正则 webhook 有效负载](#payload)}} |

以下文本是示例短信操作组中：

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> 以下 JSON 显示了示例 webhook 操作有效负载：

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

现在，已了解如何使用规则操作组，建议下一步是了解如何[管理设备](howto-manage-devices.md)。
