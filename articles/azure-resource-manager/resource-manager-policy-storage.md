---
title: "用于存储的 Azure Resource Manager 策略 | Microsoft Docs"
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
ms.date: 01/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ce31bbcb96a6afe19cf6c25dd9a68d6c2d4d080c
ms.openlocfilehash: f2141304f6db6d137065e06558e10c721b11f892


---
# <a name="apply-azure-resource-policies-to-storage-accounts"></a>将 Azure 资源策略应用于存储帐户
通过 Azure Resource Manager 策略，可以针对资源在组织中的部署方式定义统一的规则。 创建自定义策略确保组织中的用户不会违反管理组织资源所需的惯例。 本主题演示定义 Azure 存储帐户规则的多个策略。 有关策略的详细信息，请参阅[使用资源策略管理资源](resource-manager-policy.md)。

本主题中的示例演示策略规则中出现的硬编码值。 但是，可以使用参数来传入分配策略时使用的值。 有关详细信息，请参阅[策略参数](resource-manager-policy.md#parameters)。

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

## <a name="create-and-assign-policies"></a>创建和分配策略

定义策略规则后（如前面的示例所示），需要创建策略并将其分配给作用域。 作用域可以是订阅、资源组或资源。 有关创建和分配策略的示例，请参阅[创建和分配策略](resource-manager-policy.md#create-and-assign-a-policy)。 

## <a name="next-steps"></a>后续步骤
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。




<!--HONumber=Jan17_HO3-->


