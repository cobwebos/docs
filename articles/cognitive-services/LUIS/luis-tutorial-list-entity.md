---
title: 在 Nodejs 中使用列表实体自动标记实体 | Microsoft Docs
description: 了解如何添加有助于 LUIS 标记字词或短语变体的列表实体。
services: cognitive-services
author: v-geberr
titleSuffix: Azure
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: e8558ecf4a64dbccef6e6367c1447bdcdb005126
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "35365526"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>使用列表实体提升实体检测 
本教程展示了如何使用[列表实体](luis-concept-entity-types.md)提升实体检测。 无需标记列表实体，因为它们与术语完全匹配。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
* 创建列表实体 
* 添加规范化值和同义词
* 验证改进后的实体标识

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * 最新版 [Node.js](https://nodejs.org)
> * [HomeAutomation LUIS 应用](luis-get-started-create-app.md)。 如果未创建主自动化应用，请新建一个，并添加预生成的域 HomeAutomation。 定型并发布应用。 
> * LUIS 应用的 [AuthoringKey](luis-concept-keys.md#authoring-key)、[EndpointKey](luis-concept-keys.md#endpoint-key)（若要多次查询的话）、应用 ID、版本 ID 和[区域](luis-reference-regions.md)。

> [!Tip]
> 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/free/)。

本教程中的所有代码都存储在 [LUIS 示例 Github 存储库](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-list-entity)中。 

## <a name="use-homeautomation-app"></a>使用 HomeAutomation 应用
使用 HomeAutomation 应用，可以控制灯等设备、娱乐系统和供热制冷等环境控制系统。 这些系统有多个不同名称，包括制造商名称、别名、首字母缩略词和行话。 

一种跨不同文化和受众有多个名称的系统就是恒温调节器。 恒温调节器可以控制房屋或建筑物的供热制冷系统。

理想情况下，以下陈述应当会解析为预生成实体 HomeAutomation.Device：

|#|陈述|标识的实体|score|
|--|--|--|--|
|1|turn on the ac（打开空调）|HomeAutomation.Device -“ac（空调）”|0.8748562|
|2|turn up the heat（打开供热）|HomeAutomation.Device -“heat（供热）”|0.784990132|
|3|make it colder（降温）|||

前两个陈述映射到不同的设备。 第三个陈述“make it colder（降温）”不映射到设备，而是请求获取结果。 LUIS 并不知道，术语“colder（降温）”意味着恒温调节器就是请求获取的设备。 理想情况下，LUIS 应将所有这些陈述都解析为同一设备。 

## <a name="use-a-list-entity"></a>使用列表实体
HomeAutomation.Device 实体非常适用于数量较少的设备或几乎没有名称变体的设备。 对于办公楼或校园，设备名称有很多，HomeAutomation.Device 实体就不适用了。 

在这种情况下，列表实体很适用，因为办公楼或校园中设备的术语集是已知的，即使这个集合很大，也不例外。 使用列表实体，LUIS 可以接收恒温调节器术语集中的任何可取值，并将它解析为同一个设备“恒温调节器”。 

本教程将创建包含恒温调节器的实体列表。 在本教程中，恒温调节器的可选名称包括： 

|恒温调节器的可选名称|
|--|
| ac（空调） |
| a/c（空调）|
| a-c（空调）|
|heater（加热器）|
|hot（热）|
|hotter（升温）|
|cold（冷）|
|colder（降温）|

如果 LUIS 需要经常确定新可选名称，最好使用[短语列表](luis-concept-feature.md#how-to-use-phrase-lists)。

## <a name="create-a-list-entity"></a>创建列表实体
创建 Node.js 文件，并将下面的代码复制到其中。 更改 authoringKey、appId、versionId 和 region 值。

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

运行下面的命令，安装 NPM 依赖项，并通过运行代码来创建列表实体：

```Javascript
npm install && node add-entity-list.js
```

代码运行输出的是列表实体 ID：

```Javascript
026e92b3-4834-484f-8608-6114a83b03a6
```
## <a name="train-the-model"></a>训练模型
定型 LUIS，让新列表能够影响查询结果。 定型过程分为两部分，然后在定型完成后检查状态。 有多个模型的应用可能需要一段时间才能完成定型。 下面的代码先定型应用，然后等到定型成功完成。 此代码使用等待并重试策略，以免发生 429“请求次数过多”错误。 

创建 Node.js 文件，并将下面的代码复制到其中。 更改 authoringKey、appId、versionId 和 region 值。

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

运行下面的命令，通过运行代码来定型应用：

```Javascript
node train.js
```

代码运行输出的是，LUIS 模型的每次定型迭代状态。 执行下面的代码只需要检查一次定型：

```Javascript
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>发布模型
发布模型，让用户可通过终结点使用列表实体。

创建 Node.js 文件，并将下面的代码复制到其中。 更改 endpointKey、appId 和 region 值。 如果没有计划在配额限制外调用此文件，可以使用 authoringKey。

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

运行下面的命令，通过运行代码来查询应用：

```Javascript
node publish.js
```

下面的输出包括任何查询的终结点 URL。 实际 JSON 结果包括实际 appID。 

```JSON
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>查询应用 
通过终结点查询应用，以证明列表实体有助于 LUIS 确定设备类型。

创建 Node.js 文件，并将下面的代码复制到其中。 更改 endpointKey、appId 和 region 值。 如果没有计划在配额限制外调用此文件，可以使用 authoringKey。

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

运行下面的命令，通过运行代码来查询应用：

```Javascript
node train.js
```

输出的是查询结果。 因为此代码向查询字符串添加详细名称/值对，所以输出包括所有意向及其分数：

```JSON
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

特定设备“恒温调节器”是通过面向结果的“turn up the heat（打开供热）”查询进行标识。 由于应用中仍有原始 HomeAutomation.Device 实体，因此还可以看到它的结果。 

尝试其他两个陈述，看看它们是否也作为“恒温调节器”返回。 

|#|陈述|实体|type|值|
|--|--|--|--|--|
|1|turn on the ac（打开空调）| ac（空调） | DevicesList | 恒温调节器|
|2|turn up the heat（打开供热）|heat（供热）| DevicesList |恒温调节器|
|3|make it colder（降温）|colder（降温）|DevicesList|恒温调节器|

## <a name="next-steps"></a>后续步骤

可以创建其他列表实体，将设备位置扩展到房间、楼层或建筑物。 
