---
title: 使用 Azure 资源管理器模板在 Azure 数据资源管理器中配置客户管理的密钥
description: 本文介绍如何使用 Azure 资源管理器模板在 Azure 数据资源管理器中配置客户管理的密钥加密。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d0f7085342f972f227fc549c423672296697d7de
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281251"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>使用 Azure 资源管理器模板配置客户托管密钥

> [!div class="op_single_selector"]
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 模板](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>通过客户托管的密钥配置加密

在本部分中，将使用 Azure 资源管理器模板配置客户管理的密钥。 默认情况下，Azure 数据资源管理器加密使用 Microsoft 托管密钥。 在此步骤中，将 Azure 数据资源管理器群集配置为使用客户管理的密钥，并指定要与群集关联的密钥。

可以通过使用 Azure 门户或使用 PowerShell 来部署 Azure 资源管理器模板。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>更新密钥版本

创建新的密钥版本时，需要更新群集以使用新版本。 首先，调用 `Get-AzKeyVaultKey` 获取密钥的最新版本。 然后，将群集的密钥保管库属性更新为使用新的密钥版本，如使用[客户管理的密钥配置加密](#configure-encryption-with-customer-managed-keys)中所示。

## <a name="next-steps"></a>后续步骤

* [在 Azure 中保护 Azure 数据资源管理器群集](security.md)
* [配置 Azure 数据资源管理器群集的托管标识](managed-identities.md)
* 通过启用静态加密来[保护 Azure 中的群集数据资源管理器 Azure 门户](manage-cluster-security.md)。
* [使用配置客户管理的密钥C#](customer-managed-keys-csharp.md)

