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
ms.date: 07/17/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 4f52c30614ad39da8a34ff6ecfb707b75400517f
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---
# <a name="use-tags-to-organize-your-azure-resources"></a>使用标记整理 Azure 资源
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

> [!NOTE]
> 只能将标记应用到支持 Azure 资源管理器操作的资源。 如果已通过经典部署模型（例如通过 Azure 经典门户）创建虚拟机、虚拟网络或存储帐户，则无法向该资源应用标记。 若要支持标记，需通过资源管理器重新部署这些资源。 所有其他资源均支持标记。
> 
> 

## <a name="policies-for-tag-consistency"></a>标记一致性策略

可使用资源策略为组织创建标准规则。 可创建相应策略，确保使用适当的值标记资源。 有关详细信息，请参阅[应用针对标记的资源策略](resource-manager-policy-tags.md)。

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Azure CLI

若要查看*资源组*的现有标记，请使用：

```azurecli
az group show -n examplegroup --query tags
```

该脚本返回以下格式：

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

若要查看具有指定资源 ID 的资源的现有标记，请使用：

```azurecli
az resource show --id {resource-id} --query tags
```

若要查看具有指定名称、类型和资源组的资源的现有标记，请使用：

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

若要获取具有特定标记的资源组，请使用 `az group list`：

```azurecli
az group list --tag Dept=IT
```

若要获取具有特定标记和值的所有资源，请使用 `az resource list`：

```azurecli
az resource list --tag Dept=Finance
```

每次将标记应用到某个资源或资源组时，都会覆盖该资源或资源组中的现有标记。 因此，必须根据该资源或资源组是否包含现有标记来使用不同的方法。 

若要将标记添加到*不包含现有标记的资源组*，请使用：

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

若要将标记添加到*不包含现有标记的资源*，请使用：

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
``` 

若要将标记添加到已带标记的资源，请检索现有标记，重新格式化该值，然后重新应用现有标记和新标记： 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若要将资源组中的所有标记应用于其资源，并且*不保留资源上的现有标记*，请使用以下脚本：

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups 
do 
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv) 
  for resid in $r 
  do 
    az resource tag --tags $t --id $resid
  done 
done
```

若要将资源组中的所有标记应用于其资源，并且*保留资源上的现有标记*，请使用以下脚本：

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups 
do 
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv) 
  for resid in $r 
  do 
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done 
done
```


## <a name="templates"></a>模板

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>门户
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]


## <a name="rest-api"></a>REST API
Azure 门户和 PowerShell 均在后台使用[资源管理器 REST API](https://docs.microsoft.com/rest/api/resources/)。 如果需要将标记集成到其他环境中，可对资源 ID 使用 GET 以获取标记，并使用 PATCH 调用更新标记集。

## <a name="tags-and-billing"></a>标记和计费
可使用标记对计费数据进行分组。 例如，如果针对不同组织运行多个虚拟机，可以使用标记根据成本中心对使用情况进行分组。 还可使用标记根据运行时环境对成本进行分类；例如，在生产环境中运行的虚拟机的计费使用情况。


可以通过 [Azure 资源使用情况与费率卡 API](../billing/billing-usage-rate-card-overview.md) 或者使用情况逗号分隔值 (CSV) 文件检索有关标记的信息。 可从 [Azure 帐户门户](https://account.windowsazure.com/)或 [EA 门户](https://ea.azure.com)下载使用情况文件。 有关以编程方式访问计费信息的详细信息，请参阅[深入了解 Microsoft Azure 资源消耗](../billing/billing-usage-rate-card-overview.md)。 有关 REST API 操作，请参阅 [Azure 计费 REST API 参考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。


在为支持标记和计费的服务下载使用情况 CSV 时，标记将显示在“标记”列中。 有关详细信息，请参阅[了解你的 Microsoft Azure 帐单](../billing/billing-understand-your-bill.md)。

![在计费中查看标记](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>后续步骤
* 可使用自定义策略对订阅应用限制和约定。 定义的策略可能要求所有资源具有特定标记的值。 有关详细信息，请参阅[使用策略来管理资源和控制访问](resource-manager-policy.md)。
* 有关部署资源时使用 Azure PowerShell 的说明，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](powershell-azure-resource-manager.md)。
* 有关部署资源时使用 Azure CLI 的说明，请参阅[将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理器配合使用](xplat-cli-azure-resource-manager.md)。
* 有关使用门户的说明，请参阅[使用 Azure 门户管理 Azure 资源](resource-group-portal.md)。  
* 有关企业可如何使用资源管理器有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。


