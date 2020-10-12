---
title: 将 CloudEvents v1.0 架构与 Azure 事件网格配合使用
description: 介绍如何将 CloudEvents v1.0 架构用于 Azure 事件网格中的事件。 该服务支持 Cloud Events 的 JSON 实现中的事件。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324172"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>将 CloudEvents v1.0 架构与 Azure 事件网格配合使用

除了采用[默认事件架构](event-schema.md)的事件，Azure 事件网格本身还支持采用 [CloudEvents v1.0 的 JSON 实现](https://github.com/cloudevents/spec/blob/v1.0/json-format.md)和 [HTTP 协议绑定](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)的事件。 [CloudEvents](https://cloudevents.io/) 是一种用于描述事件数据的[开放规范](https://github.com/cloudevents/spec/blob/v1.0/spec.md)。

CloudEvents 提供的常用事件架构适合发布和使用基于云的事件，因此可简化互操作性。 可以通过此架构使用统一的工具、以标准方式路由和处理事件，以及以通用方式反序列化外部事件架构。 使用通用架构可以更轻松地跨平台集成工作。

CloudEvents 是由包括 Microsoft 在内的多个[协作者](https://github.com/cloudevents/spec/blob/master/community/contributors.md)通过 [Cloud Native Computing Foundation](https://www.cncf.io/) 构建的。 它目前的发布版本为 1.0。

本文介绍 CloudEvents 架构和事件网格。

## <a name="sample-event-using-cloudevents-schema"></a>使用 CloudEvents 架构的示例事件

下面以示例方式说明了 CloudEvents 格式的 Azure Blob 存储事件：

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

[此处提供](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes) CloudEvents v1.0 中的可用字段、类型和定义的详细说明。

在 CloudEvents 架构和事件网格架构中传递的事件的标头值是相同的，但 `content-type` 除外。 对于 CloudEvents 架构，该标头值为 `"content-type":"application/cloudevents+json; charset=utf-8"`。 对于事件网格架构，该标头值为 `"content-type":"application/json; charset=utf-8"`。

## <a name="event-grid-for-cloudevents"></a>CloudEvents 的事件网格

可以将事件网格用于 CloudEvents 架构的事件的输入和输出。 可以将 CloudEvents 用于系统事件（例如 Blob 存储事件和 IoT 中心事件）和自定义事件。 它还可以将网络上的这些事件来回转换。


| 输入架构       | 输出架构
|--------------------|---------------------
| CloudEvents 格式 | CloudEvents 格式
| 事件网格格式  | CloudEvents 格式
| 事件网格格式  | 事件网格格式

对于所有事件架构，事件网格都要求在发布到事件网格主题时以及在创建事件订阅时进行验证。 有关详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。

## <a name="next-steps"></a>后续步骤
请参阅[如何将 CloudEvents v1.0 架构与事件网格配合使用](cloudevents-schema.md)。  
