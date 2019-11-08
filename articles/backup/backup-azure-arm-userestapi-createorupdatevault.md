---
title: Azure 备份：使用 REST API 创建恢复服务保管库
description: 本文介绍如何使用 REST API 管理 Azure VM 备份的备份和还原操作。
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: REST API、Azure VM 备份、Azure VM 还原；
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: dacurwin
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 7c9d165f623367d1c888b90f76e96ce8fa2e8f89
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747563"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>使用 REST API 创建 Azure 恢复服务保管库

[创建保管库 REST API](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) 文档中概述了使用 REST API 创建 Azure 恢复服务保管库的步骤。 让我们使用本文档作为参考在“美国西部”区域中创建一个名为“testVault”的保管库。

若要创建或更新 Azure 恢复服务保管库，请使用以下 *PUT* 操作。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>创建请求

若要创建 *PUT* 请求，必须使用 `{subscription-id}` 参数。 如果拥有多个订阅，请参阅[使用多个订阅](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。 你将定义资源的 `{resourceGroupName}` 和 `{vaultName}` 以及 `api-version` 参数。 本文使用的是 `api-version=2016-06-01`。

以下标头是必需的：

| 请求标头   | 说明 |
|------------------|-----------------|
| Content-Type：  | 必需。 设置为 `application/json`。 |
| Authorization： | 必需。 设置为有效的 `Bearer` [访问令牌](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)。 |

有关如何创建请求的详细信息，请参阅 [REST API 请求/响应的组件](/rest/api/azure/#components-of-a-rest-api-requestresponse)。

## <a name="create-the-request-body"></a>创建请求正文

下面的通用定义用来构建请求正文：

|名称  |必选  |类型  |说明  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  可选的 eTag       |
|location     |  是       |String         |   资源位置      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  保管库的属性       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    指明每个 Azure 资源的唯一系统标识符     |
|标记     |         | 对象        |     资源标记    |

请注意，保管库名称和资源组名称是在 PUT URI 中提供的。 请求正文定义位置。

## <a name="example-request-body"></a>示例请求正文

以下示例正文用来在“美国西部”区域中创建一个保管库。 指定位置。 SKU 始终为“Standard”。

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>响应

对于要创建或更新恢复服务保管库的操作，有两个成功响应：

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |   [保管库](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | 确定        |
|201 Created     | [保管库](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   已创建      |

有关 REST API 响应的详细信息，请参阅[处理响应消息](/rest/api/azure/#process-the-response-message)。

### <a name="example-response"></a>示例响应

下面是前面的示例请求正文的精简后 *201 Created* 响应，该响应表明已分配了一个 *id* 并且 *provisioningState* 为 *Succeeded*：

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>后续步骤

[创建用于在此保管库中备份 Azure VM 的备份策略](backup-azure-arm-userestapi-createorupdatepolicy.md)。

有关 Azure REST API 的详细信息，请参阅以下文档：

- [Azure 恢复服务提供程序 REST API](/rest/api/recoveryservices/)
- [Azure REST API 入门](/rest/api/azure/)
