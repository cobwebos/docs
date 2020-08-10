---
title: 从 IoT Central 导出数据 |Microsoft Docs
description: 如何使用新的数据导出将 IoT 数据导出到 Azure 和自定义云目标。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: f51630154b77233aeb2587ac3a2d603c1da6fa4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036549"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>使用数据导出 (预览将 IoT 数据导出到云目标) 

> [!Note]
> 正在查找旧的数据导出？ 可在[此处](./howto-export-data.md)找到数据导出文档。 若要了解新的数据导出和旧数据导出之间的差异，请参阅[比较表](#comparison-of-legacy-data-export-and-new-data-export)。

本文介绍如何使用 Azure 中的新数据导出预览功能 IoT Central。 你可以使用此功能将筛选和扩充的 IoT 数据持续导出到你的云服务。 你可以使用数据导出将所做的更改及时推送到云解决方案的其他部分，以获取热路径见解、分析和存储。 

 例如，可以：
-   以近乎实时的顺序连续导出 JSON 格式的遥测数据和属性更改
-   筛选这些数据流以导出与自定义条件匹配的特定功能
-   利用来自设备的自定义值和属性值丰富数据流
-   将此数据发送到 Azure 事件中心、Azure 服务总线、Azure Blob 存储和 webhook 终结点等目标

> [!Note]
> 当你打开数据导出时，你只会获得那一刻的数据。 当前，数据导出关闭时无法检索数据。 若要保留更多的历史数据，请及早打开数据导出。

## <a name="prerequisites"></a>先决条件

若要使用数据导出 (预览) ，必须具有 V3 应用程序，并且必须具有数据导出权限。

## <a name="set-up-export-destination"></a>设置导出目标

导出目标必须存在，然后才能配置数据导出。 以下是可用的目标类型：
  - Azure 事件中心
  - Azure 服务总线队列
  - Azure 服务总线主题
  - Azure Blob 存储
  - Webhook

### <a name="create-an-event-hubs-destination"></a>创建事件中心目标

如果当前没有可导出到的事件中心命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的事件中心命名空间](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 可以在 [Azure 事件中心文档](../../event-hubs/event-hubs-create.md)中进行详细的了解。

2. 在事件中心命名空间中创建事件中心。 转到命名空间，选择顶部的“+ 事件中心”，以便创建事件中心实例。****

3. 生成一个密钥，用于在 IoT Central 中设置数据导出。 
    - 单击你创建的事件中心实例。 
    - 单击 "**设置"/"共享访问策略**"。 
    - 创建新的密钥或选择具有 "**发送**" 权限的现有密钥。 
    - 复制主连接字符串或辅助连接字符串。 将使用此设置在 IoT Central 中设置新的目标。

### <a name="create-a-service-bus-queue-or-topic-destination"></a>创建服务总线队列或主题目标

如果当前没有可导出到的服务总线命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的服务总线命名空间](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 可以在 [Azure 服务总线文档](../../service-bus-messaging/service-bus-create-namespace-portal.md)中进行详细的了解。

2. 若要创建要导出到的队列或主题，请使用服务总线命名空间，然后选择 " **+ 队列**" 或 " **+ 主题**"。

3. 生成一个密钥，用于在 IoT Central 中设置数据导出。 
    - 单击你创建的队列或主题。 
    - 单击 "**设置"/"共享访问策略**"。 
    - 创建新的密钥或选择具有 "**发送**" 权限的现有密钥。 
    - 复制主连接字符串或辅助连接字符串。 将使用此设置在 IoT Central 中设置新的目标。

### <a name="create-an-azure-blob-storage-destination"></a>创建 Azure Blob 存储目标

如果没有要导出到的现有 Azure 存储帐户，请执行以下步骤：

1. [在 Azure 门户中创建新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 可以详细了解如何创建新的[Azure Blob 存储帐户](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 存储帐户](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。 数据导出只能将数据写入支持块 blob 的存储帐户。 以下列表显示了已知的兼容存储帐户类型：

    |性能层|帐户类型|
    |-|-|
    |Standard|常规用途 V2|
    |Standard|常规用途 V1|
    |Standard|Blob 存储|
    |高级|块 Blob 存储|

2. 在存储帐户中创建容器。 转到存储帐户。 在“Blob 服务”下选择“浏览 Blob”********。 选择顶部的“+ 容器”以创建新容器。****

3. 转到 "**设置/访问密钥**"，为你的存储帐户生成一个连接字符串。 复制两个连接字符串中的一个。

### <a name="create-a-webhook-endpoint"></a>创建 webhook 终结点
您可以为要导出到的数据提供公共可用的 HTTP 终结点。 可以使用 RequestBin 创建测试 webhook 终结点。 请注意，在请求受到限制之前，RequestBin 具有设置请求限制。

1. 打开 [RequestBin](https://requestbin.net/)。
2. 创建一个新的 RequestBin 并复制 Bin URL。

## <a name="set-up-data-export"></a>设置数据导出

现在，你已有将数据导出到的目标，请按照以下步骤设置数据导出。

1. 登录到 IoT Central 应用程序。

2. 在左窗格中，选择 "**数据导出**"。

    > [!Tip]
    > 如果左窗格中未显示 "**数据导出**"，则没有权限在应用中配置数据导出。 请与管理员联系以设置数据导出。

3. 选择 " **+ 新建导出**" 按钮。 

4. 为新的导出输入显示名称，并确保已**启用 "已启用**" 切换功能，以使数据流动。

## <a name="1-choose-the-type-of-data-to-export"></a>1. 选择要导出的数据类型
您可以选择连续导出不同类型的数据。 下面是受支持的数据类型：

| 数据类型 | 说明 | 数据格式 |
| :------------- | :---------- | :----------- |
|  遥测 | 以近乎实时的速度从设备导出遥测消息。 每个导出的消息将包含原始设备消息的已规范化内容。   |  [遥测消息格式](#telemetry-format)   |
| 属性更改 | 以近乎实时的速度将更改导出到设备和云属性。 对于只读设备属性，将导出对报告值所做的更改。 对于读写属性，将导出报告的值和所需的值。 | [属性更改消息格式](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (可选) 添加筛选器
添加筛选器以减少根据筛选条件导出的数据量。 有不同类型的筛选器可用于导出的每种类型的数据。

### <a name="telemetry-filters"></a>遥测筛选器
  - **功能筛选器**：如果选择下拉列表中的遥测项，则导出的流将仅包含符合筛选条件的遥测。 如果在下拉列表中选择 "设备" 或 "云" 属性项，则导出的流将只包含其属性与筛选条件相匹配的设备的遥测数据。
  - **消息属性筛选器**：允许使用设备 sdk 的设备在每个遥测消息上发送*消息属性*或*应用程序属性*，这是一袋用于使用自定义标识符标记消息的键值对。 您可以通过键入要查找的消息属性键并指定条件来创建消息属性筛选器。 将仅导出其消息属性与指定筛选条件匹配的遥测消息。 只有 string 比较运算符受支持 (等于、不等于、不包含、不包含、不存在) 。 [详细了解 IoT 中心文档中的应用程序属性](../../iot-hub/iot-hub-devguide-messages-construct.md)。

### <a name="property-changes-filters"></a>属性更改筛选器
"**属性筛选器**"：在下拉列表中选择一个属性项，导出的流将只包含所选属性的更改，这些更改符合筛选条件。

## <a name="3-optional-add-enrichments"></a>3. (可选) 添加根据
添加根据，以便在键/值对中包含其他元数据来丰富导出的消息。 以下是遥测数据类型和属性更改数据类型的可用根据：
  - **自定义字符串**：向每个消息添加一个自定义静态字符串。 输入任意密钥，并输入任意字符串值。
  - **属性**：将当前设备报告的属性或云属性值添加到每条消息。 输入任意密钥，并选择 "设备" 或 "云" 属性。 如果导出的消息来自没有指定的设备或云属性的设备，则导出的消息将不具有该扩充。

## <a name="4-add-destinations"></a>4. 添加目标
创建新的目标或添加已创建的目标。 
  
1. 单击 "**创建新目标**" 链接。 填充以下信息：
    - **目标名称**：目标的显示名称 IoT Central
    - **目标类型**：选择目标的类型。 如果尚未安装，请[设置导出目标](#set-up-export-destination)
    - 对于 Azure 事件中心、Azure 服务总线队列或主题，请粘贴资源的连接字符串。 
    - 对于 Azure Blob 存储，请粘贴资源的连接字符串，并输入 (区分大小写) 的容器名称。
    - 对于 Webhook，请粘贴 webhook 终结点的回调 URL。 
    - 单击“创建”

2. 单击 " **+ 目标**"，然后从下拉列表中选择一个目标。 向单个导出添加最多5个目标。

3. 完成导出设置后，单击 "**保存**"。 几分钟后，你的数据将显示在目标中。

## <a name="export-contents-and-format"></a>导出内容和格式

### <a name="azure-blob-storage-destination"></a>Azure Blob 存储目标

每分钟导出一次数据，每个文件包含自上次导出的文件以来发生的一批更改。 导出的数据以 JSON 格式放置在三个文件夹中。 存储帐户中的默认路径是：

- 遥测： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 属性更改： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

若要浏览 Azure 门户中的已导出文件，请导航到该文件，然后选择 "**编辑 blob** " 选项卡。

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure 事件中心和 Azure 服务总线目标

数据会以近乎实时的速度导出。 数据在消息正文中，采用 JSON 格式编码为 UTF-8。 

在消息的批注或系统属性包中，可以找到、、 `iotcentral-device-id` `iotcentral-application-id` 和， `iotcentral-message-source` `iotcentral-message-type` 它们具有与消息正文中的相应字段相同的值。

### <a name="webhook-destination"></a>Webhook 目标
对于 webhook 目标，还会以近乎实时的时间导出数据。 数据在消息正文中的格式与事件中心和服务总线的格式相同。


## <a name="telemetry-format"></a>遥测格式
每个导出的消息都包含一种标准化的消息，该消息是以 utf-8 格式编码为 utf-8 格式的消息正文中发送的完整消息。 每条消息中包含的其他信息包括：

- `applicationId`IoT Central 应用的
- `messageSource`用于导出遥测数据的*遥测*数据
- `deviceId`发送遥测消息的设备的
- `schema`有效负载架构的名称和版本
- `templateId`与设备关联的设备模板 ID
- `enrichments`是对导出设置的任何根据
- `messageProperties`设备与消息一起发送的其他数据片段。 这也称为*应用程序属性*，[详细了解 IoT 中心文档](../../iot-hub/iot-hub-devguide-messages-construct.md)。

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

每条消息或记录表示对设备或云属性进行的一项更改。 对于设备属性，只会以单独的消息形式导出所报告值中的更改。 导出的消息中包含的其他信息包括：

- `applicationId`IoT Central 应用的
- `messageSource`用于导出属性更改数据的*属性*
- `messageType`这是*cloudPropertyChange*或*devicePropertyDesiredChange*， *devicePropertyReportedChange*
- `deviceId`其属性已更改的设备
- `schema`有效负载架构的名称和版本
- `templateId`与设备关联的设备模板 ID
- `enrichments`是对导出设置的任何根据

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

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>旧数据导出和新数据导出的比较
这是一个表，其中突出显示了旧的数据导出与新的数据导出之间的差异。 可在[此处](howto-export-data.md)了解有关旧数据导出的信息。

| 功能  | 旧数据导出 | 新数据导出 |
| :------------- | :---------- | :----------- |
| 可用数据类型 | 遥测、设备、设备模板 | 遥测，属性更改 |
| 筛选 | 无 | 取决于导出的数据类型。 对于遥测，按遥测、消息属性和属性值进行筛选 |
| 根据 | 无 | 使用自定义字符串或设备上的属性值丰富 |
| Destinations | Azure 事件中心、Azure 服务总线队列和主题、Azure Blob 存储 | 与旧数据导出和 webhook 相同| 
| 支持的应用 | V2、V3 | 仅 V3 |
| 明显限制 | 每个应用导出5个，每个导出1个目标 | 10个导出-每个应用的目标连接 | 

## <a name="next-steps"></a>后续步骤

了解如何使用新的数据导出后，请继续执行下一步：

> [!div class="nextstepaction"]
> [如何在 IoT Central 中使用分析](./howto-create-analytics.md)
