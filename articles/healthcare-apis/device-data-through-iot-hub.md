---
title: 教程：通过 Azure IoT 中心接收设备数据
description: 在本教程中，你将了解如何通过适用于 FHIR 的 Azure IoT 连接器启用从 IoT 中心到 Azure API for FHIR 的设备数据路由。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 04c732b857c06246bdc636f01afd2689c98c2b0d
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831611"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>教程：通过 Azure IoT 中心接收设备数据

通过适用于 FHIR 的 Azure IoT 连接器*，你能够将来自医疗物联网 (IoMT) 设备的数据引入 Azure API for FHIR。 [使用 Azure 门户部署适用于 FHIR 的 Azure IoT 连接器（预览版）](iot-fhir-portal-quickstart.md)快速入门展示了一个示例设备，该设备由[将遥测发送到适用于 FHIR 的 Azure IoT 连接器](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot)的 Azure IoT Central 管理。 适用于 FHIR 的 Azure IoT 连接器还可用于通过 Azure IoT 中心预配和管理的设备。 本教程提供连接 Azure IoT 中心的设备数据并将其路由到适用于 FHIR 的 Azure IoT 连接器的过程。

## <a name="prerequisites"></a>先决条件

- 有效的 Azure 订阅 - [免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 包含至少一个适用于 FHIR 的 Azure IoT 连接器的 Azure API for FHIR 资源 - [使用 Azure 门户部署适用于 FHIR 的 Azure IoT 连接器（预览版）](iot-fhir-portal-quickstart.md)
- 与真实或模拟设备连接的 Azure IoT 中心资源 - [使用 Azure 门户创建 IoT 中心](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> 如果你使用的是 Azure IoT 中心模拟设备应用程序，则可以从采用各种不同支持语言和系统的应用程序中进行选择。

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>获取适用于 FHIR 的 Azure IoT 连接器（预览版）的连接字符串

Azure IoT 中心需要一个连接字符串，以便与适用于 FHIR 的 Azure IoT 连接器安全连接。 按照[生成连接字符串](iot-fhir-portal-quickstart.md#generate-a-connection-string)中所述，为适用于 FHIR 的 Azure IoT 连接器创建新的连接字符串。 保留此连接字符串，下一步会使用它。

适用于 FHIR 的 Azure IoT 连接器在幕后使用 Azure 事件中心实例来接收设备消息。 上面创建的连接字符串基本上就是此基础事件中心的连接字符串。

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>使用适用于 FHIR 的 Azure IoT 连接器（预览版）连接 Azure IoT 中心

Azure IoT 中心支持[消息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)功能，该功能可将设备数据发送到各种 Azure 服务（例如事件中心、存储帐户和服务总线）。 适用于 FHIR 的 Azure IoT 连接器利用此功能连接到 Azure IoT 中心中的设备数据并将其发送到事件中心终结点。

> [!NOTE] 
> 目前只能使用 PowerShell 或 CLI 命令[创建消息路由](https://docs.microsoft.com/azure/iot-hub/tutorial-routing)，因为适用于 FHIR 的 Azure IoT 连接器的事件中心不在客户订阅上托管，因此不会通过 Azure 门户显示。 但是，在使用 PowerShell 或 CLI 添加消息路由对象之后，这些对象将在 Azure 门户上可见，并可以从其中进行管理。

消息路由的设置包括两个步骤。

### <a name="add-an-endpoint"></a>添加终结点
此步骤定义一个终结点，IoT 中心会将数据路由到该终结点。 可根据偏好使用 [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell 命令或 [az iot hub routing-endpoint create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) CLI 命令创建此终结点。

下面是用于创建终结点的命令的参数列表：

|PowerShell 参数|CLI 参数|说明|
|---|---|---|
|ResourceGroupName|resource-group|IoT 中心资源的资源组名称。|
|名称|hub-name|IoT 中心资源的名称。|
|EndpointName|endpoint-name|使用想要分配给创建的终结点的名称。|
|EndpointType|endpoint-type|IoT 中心需要连接的终结点的类型。 对于 PowerShell 和 CLI ，请分别使用文本值“EventHub”和“eventhub”。|
|EndpointResourceGroup|endpoint-resource-group|适用于 FHIR 的 Azure IoT 连接器的 Azure API for FHIR 资源的资源组名称。 可以从 Azure API for FHIR 的概述页获取此值。|
|EndpointSubscriptionId|endpoint-subscription-id|适用于 FHIR 的 Azure IoT 连接器的 Azure API for FHIR 资源的订阅 ID。 可以从 Azure API for FHIR 的概述页获取此值。|
|ConnectionString|connection-string|适用于 FHIR 的 Azure IoT 连接器的连接字符串。 使用在上一步中获得的值。|

### <a name="add-a-message-route"></a>添加消息路由
此步骤使用上面创建的终结点定义消息路由。 可根据偏好使用 [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell 命令或 [az iot hub route create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) CLI 命令创建路由。

下面是用于创建终结点的命令的参数列表：

|PowerShell 参数|CLI 参数|说明|
|---|---|---|
|ResourceGroupName|g|IoT 中心资源的资源组名称。|
|名称|hub-name|IoT 中心资源的名称。|
|EndpointName|endpoint-name|上面创建的终结点的名称。|
|RouteName|route-name|要分配给正在创建的消息路由的名称。|
|源|source-type|要发送给终结点的数据的类型。 对于 PowerShell 和 CLI，分别使用文本值“Devicemessages”和“devicemessages”。|

## <a name="send-device-message-to-iot-hub"></a>向 IoT 中心发送设备消息

使用设备（真实或模拟）将下面显示的示例心率消息发送到 Azure IoT 中心。 此消息将路由到适用于 FHIR 的 Azure IoT 连接器，在连接器中，消息将转换为 FHIR 观测资源并存储到 Azure API for FHIR。

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> 请确保发送符合[映射模板](iot-mapping-templates.md)（通过适用于 FHIR 的 Azure IoT 连接器配置的）标准的设备消息。

## <a name="view-device-data-in-azure-api-for-fhir"></a>查看 Azure API for FHIR 中的设备数据

可以使用 Postman 在 Azure API for FHIR 上查看由适用于 FHIR 的 Azure IoT 连接器创建的 FHIR 的观察资源。 设置 [Postman 以访问 Azure API for FHIR](access-fhir-postman-tutorial.md)，并向 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 发出 `GET` 请求，以查看在上述示例消息中提交的具有心率值的观察 FHIR 资源。

> [!TIP]
> 确保用户可以访问 Azure API for FHIR 数据平面。 使用 [Azure 基于角色的访问控制 (Azure RBAC)](configure-azure-rbac.md) 分配所需的数据平面角色。


## <a name="next-steps"></a>后续步骤

在本快速入门指南中，你将设置 Azure IoT 中心，以便将设备数据路由到适用于 FHIR 的 Azure IoT 连接器。 选择下面的后续步骤以了解有关适用于 FHIR 的 Azure IoT 连接器的详细信息：

了解适用于 FHIR 的 Azure IoT 连接器中数据流的不同阶段。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器数据流](iot-data-flow.md)

了解如何使用设备和 FHIR 映射模板配置 IoT 连接器。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器映射模板](iot-mapping-templates.md)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。

FHIR 是 HL7 的注册商标，经 HL7 许可使用。