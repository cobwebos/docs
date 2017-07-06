---
title: "适用于存储帐户的 Azure 资源策略 | Microsoft Docs"
description: "介绍用于管理存储帐户的部署的 Azure Resource Manager 策略。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/05/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 75685a21ce4a212638016be62640badd4870454a
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017


---
# <a name="apply-resource-policies-to-storage-accounts"></a>将资源策略应用于存储帐户
本主题介绍多个可以应用于 Azure 存储帐户的[资源策略](resource-manager-policy.md)。 这些策略可确保组织中部署的存储帐户的一致性。 

## <a name="define-permitted-storage-account-types"></a>定义允许的存储帐户类型

以下策略限制可以部署[存储帐户类型](../storage/storage-redundancy.md)：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

提供了一个类似的策略规则，作为内置策略定义，其中具有用于接受允许的 SKU 的参数。 该内置策略具有 `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1` 的资源 ID。 

## <a name="define-permitted-access-tier"></a>定义允许的访问层

以下策略指定可为存储帐户指定的[访问层](../storage/storage-blob-storage-tiers.md)类型：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>确保已启用加密

以下策略要求所有存储帐户启用[存储服务加密](../storage/storage-service-encryption.md)：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

此策略规则也可用作具有 `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f` 资源 ID 的内置策略定义。

## <a name="next-steps"></a>后续步骤
* 定义策略规则后（如上述示例所示），需要创建策略定义并将其分配给作用域。 作用域可以是订阅、资源组或资源。 若要通过门户分配策略，请参阅[使用 Azure 门户分配和管理资源策略](resource-manager-policy-portal.md)。 若要通过 REST API、PowerShell 或 Azure CLI 分配策略，请参阅[通过脚本分配和管理策略](resource-manager-policy-create-assign.md)。 
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。


