---
title: "标记 Azure 资源实现逻辑布局 | Microsoft Docs"
description: "演示如何应用标记来整理 Azure 资源以便进行计费和管理。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: 2f56314769d90a1f0f9ebb5ece9c8e54b23b8936
ms.lasthandoff: 02/21/2017


---
# <a name="use-tags-to-organize-your-azure-resources"></a>使用标记整理 Azure 资源
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

> [!NOTE]
> 您只能将标记应用到支持资源管理器操作的资源。 如果通过经典部署模型（如通过经典门户）创建虚拟机、虚拟网络或存储，则无法向该资源应用标记。 若要支持标记，需通过 Resource Manager 重新部署这些资源。 所有其他资源均支持标记。
> 
> 

## <a name="ensure-tag-consistency-with-policies"></a>使用策略确保标记一致性

通过资源策略，可为组织创建标准规则。 可创建相应策略，确保使用适当的值标记资源。 有关详细信息，请参阅[应用针对标记的资源策略](resource-manager-policy-tags.md)。

## <a name="templates"></a>模板

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>门户
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli-20"></a>Azure CLI 2.0

使用 Azure CLI 2.0 可将标记添加到资源和资源组，以及按标记值查询资源。

每次将标记应用到某个资源或资源组时，都会覆盖该资源或资源组中的现有标记。 因此，必须根据该资源或资源组是否包含想要保留的现有标记来使用不同的方法。 若将标记添加到：

* 不包含现有标记的资源组。

  ```azurecli
  az group update -n TagTestGroup --set tags.Environment=Test tags.Dept=IT
  ```

* 不包含现有标记的资源。

  ```azurecli
  az resource tag --tags Dept=IT Environment=Test -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
  ``` 

若要向已包含标记的资源添加标记，请首先检索现有标记： 

```azurecli
az resource show --query tags --output list -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
```

将返回以下格式：

```
Dept        : Finance
Environment : Test
```

将现有标记重新应用到资源，并添加新标记。

```azurecli
az resource tag --tags Dept=Finance Environment=Test CostCenter=IT -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
``` 

若要获取具有特定标记的资源组，请使用 `az group list`。

```azurecli
az group list --tag Dept=IT
```

若要获取具有特定标记和值的所有资源，请使用 `az resource list`。

```azurecli
az resource list --tag Dept=Finance
```

## <a name="azure-cli-10"></a>Azure CLI 1.0
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>REST API
门户和 PowerShell 在幕后都使用 [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/)。 如果需要将标记集成到另一个环境中，可以对资源 ID 使用 GET 以获取标记，并使用 PATCH 调用更新标记集。

## <a name="tags-and-billing"></a>标记和计费
使用标记可对计费数据进行分组。 例如，如果针对不同组织运行多个虚拟机，可以使用标记根据成本中心对使用情况进行分组。 您还可以使用标记根据运行时环境对成本进行分类；例如，在生产环境中运行的虚拟机的计费使用情况。


可以通过 [Azure 资源使用情况与费率卡 API](../billing/billing-usage-rate-card-overview.md) 或者使用情况逗号分隔值 (CSV) 文件检索有关标记的信息。 可以从 [Azure 帐户门户](https://account.windowsazure.com/)或 [EA 门户](https://ea.azure.com)下载使用情况文件。 有关以编程方式访问计费信息的详细信息，请参阅[深入了解 Microsoft Azure 资源消耗](../billing/billing-usage-rate-card-overview.md)。 有关 REST API 操作，请参阅 [Azure 计费 REST API 参考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。


在为支持标记和计费的服务下载使用情况 CSV 时，标记将显示在“标记”列中。 有关详细信息，请参阅[了解你的 Microsoft Azure 帐单](../billing/billing-understand-your-bill.md)。

![在计费中查看标记](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>后续步骤
* 可以使用自定义策略对订阅应用限制和约定。 定义的策略可能要求为所有资源设置特定的标记值。 有关详细信息，请参阅 [使用策略来管理资源和控制访问](resource-manager-policy.md)。
* 有关部署资源时使用 Azure PowerShell 的说明，请参阅[将 Azure PowerShell 与 Azure Resource Manager 配合使用](powershell-azure-resource-manager.md)。
* 有关部署资源时使用 Azure CLI 的说明，请参阅[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理配合使用](xplat-cli-azure-resource-manager.md)。
* 有关使用门户的说明，请参阅[使用 Azure 门户管理 Azure 资源](resource-group-portal.md)  
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。


