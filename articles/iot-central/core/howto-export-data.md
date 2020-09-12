---
title: 从 Azure IoT Central (preview) 导出数据 |Microsoft Docs
description: 如何使用新的数据导出将 IoT 数据导出到 Azure 和自定义云目标。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/02/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: contperfq1
ms.openlocfilehash: 0a07d7e57ced5e2cd9457dc51ebcd355306fc48e
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461929"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>使用数据导出 (预览将 IoT 数据导出到云目标) 

> [!Note]
> 本文介绍 IoT Central 中的预览数据导出功能。
>
> - 有关旧数据导出功能的信息，请参阅 [使用数据导出将 IoT 数据导出到云目标 (旧) ](./howto-export-data-legacy.md)。
> - 若要了解预览版数据导出功能和旧数据导出功能之间的差异，请参阅下表中的 [比较表](#comparison-of-legacy-data-export-and-preview-data-export) 。

本文介绍如何使用 Azure 中的新数据导出预览功能 IoT Central。 使用此功能可以从您的 IoT Central 应用程序持续导出已筛选和增加的 IoT 数据。 数据导出会将接近实时的更改推送到云解决方案的其他部分，以获取热路径见解、分析和存储。

例如，你能够：

- 以近乎实时的顺序连续导出 JSON 格式的遥测数据和属性更改。
- 筛选数据流以导出与自定义条件匹配的数据。
- 利用来自设备的自定义值和属性值丰富数据流。
- 将数据发送到 Azure 事件中心、Azure 服务总线、Azure Blob 存储和 webhook 终结点等目标。

> [!Tip]
> 当你打开数据导出时，你只会获得那一刻的数据。 当前，数据导出关闭时无法检索数据。 若要保留更多的历史数据，请及早打开数据导出。

## <a name="prerequisites"></a>先决条件

若要使用预览数据导出功能，你必须具有 [V3 应用程序](howto-get-app-info.md)，并且必须具有 [数据导出](howto-manage-users-roles.md) 权限。

## <a name="set-up-export-destination"></a>设置导出目标

导出目标必须存在，然后才能配置数据导出。 当前提供以下目标类型：

- Azure 事件中心
- Azure 服务总线队列
- Azure 服务总线主题
- Azure Blob 存储
- Webhook

### <a name="create-an-event-hubs-destination"></a>创建事件中心目标

如果当前没有可导出到的事件中心命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的事件中心命名空间](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 可以在 [Azure 事件中心文档](../../event-hubs/event-hubs-create.md)中进行详细的了解。

1. 在事件中心命名空间中创建事件中心。 转到命名空间，选择顶部的“+ 事件中心”，以便创建事件中心实例。****

1. 生成要在 IoT Central 中设置数据导出时使用的密钥：

    - 选择创建的事件中心实例。
    - 选择 " **设置" > 共享访问策略**"。
    - 创建新的密钥或选择具有 " **发送** " 权限的现有密钥。
    - 复制主连接字符串或辅助连接字符串。 使用此连接字符串在 IoT Central 中设置新的目标。

### <a name="create-a-service-bus-queue-or-topic-destination"></a>创建服务总线队列或主题目标

如果当前没有可导出到的服务总线命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的服务总线命名空间](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 可以在 [Azure 服务总线文档](../../service-bus-messaging/service-bus-create-namespace-portal.md)中进行详细的了解。

1. 若要创建要导出到的队列或主题，请使用服务总线命名空间，然后选择 " **+ 队列** " 或 " **+ 主题**"。

1. 生成要在 IoT Central 中设置数据导出时使用的密钥：

    - 选择创建的队列或主题。
    - 选择 " **设置"/"共享访问策略**"。
    - 创建新的密钥或选择具有 " **发送** " 权限的现有密钥。
    - 复制主连接字符串或辅助连接字符串。 使用此连接字符串在 IoT Central 中设置新的目标。

### <a name="create-an-azure-blob-storage-destination"></a>创建 Azure Blob 存储目标

如果没有要导出到的现有 Azure 存储帐户，请执行以下步骤：

1. [在 Azure 门户中创建新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 可以详细了解如何创建新的 [Azure Blob 存储帐户](https://aka.ms/blobdocscreatestorageaccount) 或 [Azure Data Lake Storage v2 存储帐户](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。 数据导出只能将数据写入支持块 blob 的存储帐户。 以下列表显示了已知的兼容存储帐户类型：

    |性能层|帐户类型|
    |-|-|
    |标准|常规用途 V2|
    |标准|常规用途 V1|
    |标准|Blob 存储|
    |高级|块 Blob 存储|

1. 若要在存储帐户中创建容器，请使用存储帐户。 在“Blob 服务”下选择“浏览 Blob”********。 选择顶部的“+ 容器”以创建新容器。****

1. 转到 " **设置" > "访问密钥**"，为你的存储帐户生成一个连接字符串。 复制两个连接字符串中的一个。

### <a name="create-a-webhook-endpoint"></a>创建 webhook 终结点

可以将数据导出到公开可用的 HTTP webhook 终结点。 可以使用 [RequestBin](https://requestbin.net/)创建测试 webhook 终结点。 达到请求限制时，RequestBin 限制请求：

1. 打开 [RequestBin](https://requestbin.net/)。
2. 创建一个新的 RequestBin 并复制 **Bin URL**。 在测试数据导出时使用此 URL。

## <a name="set-up-data-export"></a>设置数据导出

现在，你已有将数据导出到的目标，请在 IoT Central 应用程序中设置数据导出：

1. 登录到 IoT Central 应用程序。

1. 在左窗格中，选择 " **数据导出 (预览") **。

    > [!Tip]
    > 如果在左窗格中看不到 " **数据导出 (预览") ** ，则无权在应用中配置数据导出。 请与管理员联系以设置数据导出。

1. 选择 " **+ 新建导出**"。

1. 为新的导出输入显示名称，并确保 **已启用**数据导出。

1. 选择要导出的数据类型。 下表列出了支持的数据导出类型：

    | 数据类型 | 说明 | 数据格式 |
    | :------------- | :---------- | :----------- |
    |  遥测 | 以近乎实时的速度从设备导出遥测消息。 每个导出的消息都包含原始设备消息的已规范化内容。   |  [遥测消息格式](#telemetry-format)   |
    | 属性更改 | 以近乎实时的速度将更改导出到设备和云属性。 对于只读设备属性，将导出对报告值所做的更改。 对于读写属性，将导出报告的值和所需的值。 | [属性更改消息格式](#property-changes-format) |

1. （可选）添加筛选器以减少导出的数据量。 每种数据导出类型都有不同类型的筛选器：

    若要筛选遥测数据，请使用：

    - **功能筛选器**：如果在 " **名称** " 下拉列表中选择遥测项，则导出的流只包含符合筛选条件的遥测。 如果在 " **名称** " 下拉列表中选择 "设备" 或 "云" 属性项，则导出的流只包含其属性与筛选条件相匹配的设备的遥测数据。
    - **消息属性筛选器**：使用设备 sdk 的设备可以在每个遥测消息上发送 *消息属性* 或 *应用程序属性* 。 属性是使用自定义标识符对消息进行标记的键值对包。 若要创建消息属性筛选器，请输入所需的消息属性键，并指定条件。 仅导出属性与指定筛选条件匹配的遥测消息。 支持以下字符串比较运算符：等于、不等于、包含、不包含、存在、不存在。 [详细了解 IoT 中心文档中的应用程序属性](../../iot-hub/iot-hub-devguide-messages-construct.md)。

    若要筛选属性更改，请使用 **功能筛选器**。 选择下拉列表中的属性项。 导出的流只包含所选属性的更改，这些更改符合筛选条件。

1. （可选）利用附加的键值对元数据丰富导出的消息。 以下根据适用于遥测和属性更改数据导出类型：

    - **自定义字符串**：向每个消息添加一个自定义静态字符串。 输入任意密钥，并输入任意字符串值。
    - **属性**：将当前设备报告的属性或云属性值添加到每条消息。 输入任意密钥，并选择 "设备" 或 "云" 属性。 如果导出的消息来自没有指定属性的设备，则导出的消息不会获取扩充。

1. 添加新的目标或添加已创建的目标。 选择 " **新建一个** " 链接，并添加以下信息：

    - **目标名称**： IoT Central 中目标的显示名称。
    - **目标类型**：选择目标的类型。 如果尚未设置目标，请参阅 [设置导出目标](#set-up-export-destination)。
    - 对于 Azure 事件中心、Azure 服务总线队列或主题，请粘贴资源的连接字符串。
    - 对于 Azure Blob 存储，请粘贴资源的连接字符串，并输入区分大小写的容器名称。
    - 对于 Webhook，请粘贴 webhook 终结点的回调 URL。
    - 选择“创建” 。

1. 选择 " **+ 目标** "，然后从下拉列表中选择一个目标。 最多可以向单个导出添加5个目标。

1. 完成导出设置后，请选择 " **保存**"。 几分钟后，你的数据将显示在目标中。

## <a name="export-contents-and-format"></a>导出内容和格式

### <a name="azure-blob-storage-destination"></a>Azure Blob 存储目标

每分钟导出一次数据，每个文件包含自上一次导出以来发生的一批更改。 导出的数据以 JSON 格式保存。 存储帐户中导出数据的默认路径为：

- 遥测： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 属性更改： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

若要浏览 Azure 门户中的已导出文件，请导航到该文件，然后选择 " **编辑 blob**"。

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure 事件中心和 Azure 服务总线目标

数据将以近乎实时的速度导出。 数据在消息正文中，采用 JSON 格式编码为 UTF-8。

消息的批注或系统属性包包含 `iotcentral-device-id` 、 `iotcentral-application-id` 、 `iotcentral-message-source` 和字段，这些字段的 `iotcentral-message-type` 值与消息正文中相应的字段的值相同。

### <a name="webhook-destination"></a>Webhook 目标

对于 webhook 目标，数据也会以近乎实时的速度导出。 消息正文中的数据的格式与事件中心和服务总线的格式相同。

### <a name="telemetry-format"></a>遥测格式

每个导出的消息都包含在消息正文中发送的完整消息的规范化形式。 消息采用 JSON 格式，并编码为 UTF-8。 每条消息中的信息包括：

- `applicationId`： IoT Central 应用程序的 ID。
- `messageSource`：消息的源 `telemetry` 。
- `deviceId`：发送遥测消息的设备的 ID。
- `schema`：负载架构的名称和版本。
- `templateId`：与设备关联的设备模板的 ID。
- `enrichments`：导出时设置的任何根据。
- `messageProperties`：设备随消息一起发送的附加属性。 这些属性有时称为 *应用程序属性*。 [了解 IoT 中心文档中的详细信息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

对于事件中心和服务总线，IoT Central 在接收到来自设备的消息之后迅速导出一条新消息。

对于 Blob 存储，将每分钟分批和导出一次消息。

下面的示例显示导出的遥测消息：

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>属性更改格式

每条消息或记录表示对设备或云属性进行的一项更改。 对于设备属性，只会将报告的值中的更改导出为单独的消息。 导出的消息中的信息包括：

- `applicationId`： IoT Central 应用程序的 ID。
- `messageSource`：消息的源 `properties` 。
- `messageType`： `cloudPropertyChange` 、 `devicePropertyDesiredChange` 或 `devicePropertyReportedChange` 。
- `deviceId`：发送遥测消息的设备的 ID。
- `schema`：负载架构的名称和版本。
- `templateId`：与设备关联的设备模板的 ID。
- `enrichments`：导出时设置的任何根据。

对于事件中心和服务总线，IoT Central 会以近乎实时的时间将新的消息数据导出到事件中心、服务总线队列或主题。

对于 Blob 存储，将每分钟分批和导出一次消息。

下面的示例演示了 Azure Blob 存储中接收到的已导出属性更改消息。

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-preview-data-export"></a>旧数据导出和预览数据导出的比较

下表显示了 [旧的数据导出](howto-export-data-legacy.md) 和预览数据导出功能之间的差异：

| 功能  | 旧数据导出 | 新数据导出 |
| :------------- | :---------- | :----------- |
| 可用数据类型 | 遥测、设备、设备模板 | 遥测，属性更改 |
| Filtering | 无 | 取决于导出的数据类型。 对于遥测，按遥测、消息属性和属性值进行筛选 |
| 根据 | 无 | 使用自定义字符串或设备上的属性值丰富 |
| Destinations | Azure 事件中心、Azure 服务总线队列和主题、Azure Blob 存储 | 与旧数据导出和 webhook 相同|
| 支持的应用程序版本 | V2、V3 | 仅 V3 |
| 明显限制 | 每个应用导出5个，每个导出1个目标 | 10个导出-每个应用的目标连接 |

## <a name="next-steps"></a>后续步骤

了解如何使用新的数据导出后，建议下一步是了解 [如何在 IoT Central 中使用分析](./howto-create-analytics.md)
