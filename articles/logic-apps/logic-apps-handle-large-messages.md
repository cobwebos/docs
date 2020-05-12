---
title: 使用分块处理大消息
description: 了解如何在使用 Azure 逻辑应用创建的自动任务和工作流中使用分块来处理大消息大小
services: logic-apps
ms.suite: integration
author: DavidCBerry13
ms.author: daberry
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 54828dded5196c86946d99a9cd8cec7a42533661
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117557"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用分块处理大型消息

处理消息时，逻辑应用通过最大大小来限制消息内容。 此限制可以减少大型消息的存储和处理带来的开销。 逻辑应用可以通过分块将大消息拆分成小消息，以便处理其大小超出此限制的消息。  有了这种方式，就可以使用逻辑应用在特定条件下传输大文件。 通过连接器或 HTTP 与其他服务通信时，逻辑应用可以使用大型消息，但只能采用区块的方式。  这种情况意味着，连接器也必须支持分块，或者逻辑应用和这些服务之间的基础 HTTP 消息交换必须使用分块。

本文展示了如何为处理超过限制的消息的操作设置分块。 逻辑应用触发器不支持分块，因为交换多个消息的开销会增加。 

## <a name="what-makes-messages-large"></a>哪些因素导致消息成为“大型”消息？

消息是否为“大型”消息取决于处理这些消息的服务。 不同的逻辑应用和连接器对大型消息的具体大小限制并不相同。 逻辑应用和连接器都不能直接使用大型消息，必须将这些消息分块。 有关逻辑应用消息的大小限制，请参阅[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md)。
有关每个连接器的消息大小限制，请参阅[连接器的特定技术详细信息](../connectors/apis-list.md)。

### <a name="chunked-message-handling-for-logic-apps"></a>逻辑应用的分块消息处理

逻辑应用不能直接使用大于消息大小限制的分块消息输出。 只有支持分块的操作才能访问这些输出中的消息内容。 因此，负责处理大型消息的操作必须满足以下条件之一： 

* 在该操作属于某个连接器时能够以本机方式支持分块。 
* 在该操作的运行时配置中启用分块支持。 

否则，在尝试访问大型内容输出时，会出现运行时错误。 若要启用分块，请参阅[设置分块支持](#set-up-chunking)。

### <a name="chunked-message-handling-for-connectors"></a>连接器的分块消息处理

与逻辑应用通信的服务可以有其自己的消息大小限制。 这些限制通常小于逻辑应用限制。 例如，假定某个连接器支持分块，该连接器可能会将 30 MB 的消息视为大型消息，而逻辑应用则不会。 为了与此连接器的限制保持一致，逻辑应用会将任何大于 30 MB 的消息拆分成更小型的区块。

支持分块的连接器的基础分块协议对最终用户不可见。 但是，并非所有连接器都支持分块，因此当传入消息超出连接器的大小限制时，这些不支持分块的连接器就会生成运行时错误。

> [!NOTE]
> 对于使用分块的操作，不能传递触发器主体，也不能使用 `@triggerBody()?['Content']` 这些操作中的表达式。 相反，对于文本或 JSON 文件内容，可以尝试使用[**撰写**操作](../logic-apps/logic-apps-perform-data-operations.md#compose-action)或[创建变量](../logic-apps/logic-apps-create-variables-store-values.md)来处理该内容。 如果触发器正文包含其他内容类型，如媒体文件，则需要执行其他步骤来处理该内容。

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>设置基于 HTTP 的分块

在泛型 HTTP 方案中，可以拆分通过 HTTP 进行的大型下载和上传的内容，因此逻辑应用和终结点可以交换大型消息。 但是，必须按逻辑应用预期的方式进行消息分块。 

如果某个终结点为下载内容或上传内容启用了分块功能，则逻辑应用中的 HTTP 操作会自动将大型消息分块。 否则，必须在终结点上设置分块支持。 如果没有终结点或连接器的所有权或控制权，则可能无法设置分块。

另外，如果某个 HTTP 操作尚未启用分块功能，则还必须在操作的 `runTimeConfiguration` 属性中设置分块。 可以在操作中设置此属性，可以按照后面的说明在代码视图编辑器中直接进行，也可以按下述说明在逻辑应用设计器中进行：

1. 在 HTTP 操作的右上角选择省略号按钮 (**...**)，然后选择“设置”。****

   ![在操作中打开设置菜单](./media/logic-apps-handle-large-messages/http-settings.png)

2. 在“内容传输”下将“允许分块”设置为“启用”。************

   ![启用分块](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. 若要继续为下载或上传设置分块，请继续完成以下部分。

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>以区块形式下载内容

在通过 HTTP GET 请求下载大型消息时，许多终结点会自动将这些消息以区块的形式发送。 通过 HTTP 从终结点下载分块的消息时，终结点必须支持部分内容请求，也称“分块下载”。** 当逻辑应用向某个用于下载内容的终结点发送 HTTP GET 请求时，如果该终结点以“206”状态代码进行响应，则说明响应包含分块内容。 某个终结点是否支持部分请求不受逻辑应用的控制。 但是，逻辑应用在获得第一个“206”响应时，会自动发送多个要求下载所有内容的请求。

若要检查终结点是否能够支持部分内容，请发送 HEAD 请求。 此请求用于确定响应是否包含 `Accept-Ranges` 标头。 这样一来，如果终结点支持分块下载但没有发送分块内容，则可在 HTTP GET 请求中设置 `Range` 标头，以便建议此选项。** 

以下步骤说明了逻辑应用用来将分块内容从终结点下载到你的逻辑应用的详细过程：

1. 你的逻辑应用向终结点发送 HTTP GET 请求。

   请求标头可以选择性地包括一个 `Range` 字段，用于描述请求内容区块的字节范围。

2. 终结点使用“206”状态代码和 HTTP 消息正文进行响应。

    有关此区块中内容的详细信息显示在响应的 `Content-Range` 标头中，其中包括有助于逻辑应用确定区块的开头和结尾的信息，以及整个内容在分块前的总大小。

3. 逻辑应用自动发送后续 HTTP GET 请求。

    逻辑应用会一直发送后续 GET 请求，直至检索完整个内容。

例如，此操作定义显示一个设置 `Range` 标头的 HTTP GET 请求。 此标头建议终结点使用分块内容进行响应：**

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

GET 请求将表示字节范围的 "Range" 标头设置为 "bytes=0-1023"。 如果终结点支持部分内容请求，则终结点会使用所请求范围中的内容区块进行响应。 "Range" 标头字段的确切格式因终结点而异。

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>以区块形式上传内容

若要从 HTTP 操作上传分块内容，该操作必须已经通过操作的 `runtimeConfiguration` 属性启用了分块支持。 此设置允许操作启动分块协议。 然后，逻辑应用即可向目标终结点发送初始的 POST 或 PUT 消息。 在终结点以建议的区块大小进行响应以后，逻辑应用就会跟进，发送包含内容区块的 HTTP PATCH 请求。

以下步骤说明了逻辑应用用来将分块内容从你的逻辑应用上传到终结点的详细过程：

1. 你的逻辑应用使用空的消息正文发送初始的 HTTP POST 或 PUT 请求。 请求标头包括与逻辑应用需要以区块形式上传的内容的以下信息：

   | 逻辑应用请求标头字段 | 值 | 类型 | 描述 |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | 分块 | String | 指示内容以区块形式上传 |
   | **x-ms-content-length** | <*content-length*> | Integer | 整个内容在分块之前的大小（以字节为单位） |
   ||||

2. 终结点以“200”成功状态代码和以下可选信息进行响应：

   | 终结点响应标头字段 | 类型 | 必须 | 说明 |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | 否 | 建议的区块大小（以字节为单位） |
   | **位置** | String | 是 | 要向其发送 HTTP PATCH 消息的 URL 位置 |
   ||||

3. 逻辑应用创建并发送后续 HTTP PATCH 消息 - 每条消息包含以下信息：

   * 基于 **x-ms-chunk-size** 或某个内部计算大小的内容区块，直至顺序上传总计 **x-ms-content-length** 的所有内容

   * 这些标头详述了在每个 PATCH 消息中发送的内容区块：

     | 逻辑应用请求标头字段 | 值 | 类型 | 描述 |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*内*> | String | 当前内容区块的字节范围，包括起始值、结束值、内容总大小，例如："bytes=0-1023/10100" |
     | **Content-type** | <*content-type*> | String | 分块内容的类型 |
     | **Content-length** | <*content-length*> | String | 当前区块的大小长度（以字节为单位） |
     |||||

4. 每次进行 PATCH 请求之后，终结点会以“200”状态代码和以下响应标头进行响应，以此确认每个区块的接收情况：

   | 终结点响应标头字段 | 类型 | 必须 | 说明 |
   |--------------------------------|------|----------|-------------|
   | **内** | String | 是 | 终结点收到的内容的字节范围，例如：“bytes=0-1023” |   
   | **x-ms-chunk-size** | Integer | 否 | 建议的区块大小（以字节为单位） |
   ||||

例如，此操作定义显示一个要求将分块内容上传到终结点的 HTTP POST 请求。 在操作的 `runTimeConfiguration` 属性中，`contentTransfer` 属性将 `transferMode` 设置为 `chunked`：

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
