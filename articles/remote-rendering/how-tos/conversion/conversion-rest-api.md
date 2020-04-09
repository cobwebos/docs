---
title: 资产转换 REST API
description: 描述如何通过 REST API 转换资产
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 38116efc9e87eca8e2514a0a84045a69b8d42326
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887038"
---
# <a name="use-the-model-conversion-rest-api"></a>使用模型转换 REST API

[模型转换](model-conversion.md)服务通过[REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)进行控制。 本文介绍了转换服务 API 详细信息。

## <a name="regions"></a>区域

请参阅要将请求发送到的基本 URL[的可用区域列表](../../reference/regions.md)。

## <a name="common-headers"></a>常见标头

### <a name="common-request-headers"></a>常见请求标头

必须为所有请求指定这些标头：

- **授权**标头的值必须为"承载器 =*TOKEN*}"，其中 [*TOKEN*] 是[服务访问令牌](../tokens.md)。

### <a name="common-response-headers"></a>常见响应标头

所有响应都包含以下标头：

- **MS-CV**标头包含一个唯一字符串，可用于跟踪服务中的调用。

## <a name="endpoints"></a>终结点

转换服务提供三个 REST API 终结点，以：

- 使用与 Azure 远程呈现帐户关联的存储帐户启动模型转换。 
- 使用提供的*共享访问签名 （SAS）* 启动模型转换。
- 查询转换状态

### <a name="start-conversion-using-a-linked-storage-account"></a>使用链接的存储帐户开始转换
Azure 远程呈现帐户需要按照有关如何[链接存储帐户](../create-an-account.md#link-storage-accounts)的步骤访问提供的存储帐户。

| 终结点 | 方法 |
|-----------|:-----------|
| /v1/帐户/**帐户 ID**/转换/创建 | POST |

返回在 JSON 文档中包装的持续转换的 ID。 字段名称为"转换 ID"。

#### <a name="request-body"></a>请求正文


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>使用提供的共享访问签名开始转换
如果您的 ARR 帐户未链接到存储帐户，则此 REST 接口允许您使用*共享访问签名 （SAS）* 提供访问权限。

| 终结点 | 方法 |
|-----------|:-----------|
| /v1/帐户/**帐户 ID**/转化/创建共享访问签名 | POST |

返回在 JSON 文档中包装的持续转换的 ID。 字段名称为"转换 ID"。

#### <a name="request-body"></a>请求正文

请求正文与上面创建 REST 调用相同，但输入和输出包含*共享访问签名 （SAS） 令牌*。 这些令牌提供对存储帐户的访问，用于读取输入和写入转换结果。

> [!NOTE]
> 这些 SAS URI 令牌是查询字符串，而不是完整的 URI。 


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>轮询转换状态
可以使用以下接口查询从上述 REST 调用之一开始的正在进行的转换的状态：


| 终结点 | 方法 |
|-----------|:-----------|
| /v1/帐户/**帐户 ID**/转换/**转换 Id** | GET |

返回具有"状态"字段的 JSON 文档，该字段可以具有以下值：

- "正在运行"
- “Success”
- "失败"

如果状态为"失败"，则将存在一个附加的"错误"字段，其中将显示一个包含错误信息的"消息"子字段。 其他日志将上载到输出容器。

## <a name="next-steps"></a>后续步骤

- [将 Azure Blob 存储用于模型转换](blob-storage.md)
- [模型转换](model-conversion.md)
