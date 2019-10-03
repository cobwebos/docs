---
title: 删除资源组和资源 - Azure 资源管理器
description: 介绍如何删除资源组和资源。 本文介绍 Azure 资源管理器如何在删除资源组时对资源的删除进行排序。 它描述响应代码以及资源管理器如何处理它们以确定是否成功删除。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 30a394fd33ed5d928175fc27e003661c2b53de9a
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275086"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure 资源管理器资源组和资源删除

本文介绍如何删除资源组和资源。 本文介绍 Azure 资源管理器如何在删除资源组时对资源的删除进行排序。

## <a name="how-order-of-deletion-is-determined"></a>如何确定删除顺序

删除资源组时，资源管理器会确定删除资源的顺序。 采用以下顺序：

1. 删除所有子（嵌套）资源。

2. 接下来，删除管理其他资源的资源。 可以为资源设置 `managedBy` 属性，用于指示管理它的其他资源。 设置此属性后，先删除管理其他资源的资源，再删除受管理的这些资源。

3. 删除前两种类别后，便会删除剩余资源。

确定顺序后，资源管理器为每个资源发出 DELETE 操作。 它先等待所有依赖项均完成操作，再继续。

对于同步操作，预期的成功响应代码为：

* 200
* 204
* 404

对于异步操作，预期的成功响应代码为 202。 资源管理器会跟踪 location 标头或 azure-async operation 标头，以确定异步删除操作的状态。
  
### <a name="deletion-errors"></a>删除错误

如果删除操作返回错误，资源管理器会重试 DELETE 调用。 若状态代码为 5xx、429 和 408，便会重试。 默认情况下，重试的时间段为 15 分钟。

## <a name="after-deletion"></a>删除之后

资源管理器对其尝试删除的每个资源发出 GET 调用。 此 GET 调用的响应应为 404。 当资源管理器获得 404 时，便将删除操作视为成功完成。 资源管理器会从其缓存中删除资源。

但是，如果对资源的 GET 调用返回 200 或 201，资源管理器会重新创建资源。

如果 GET 操作返回错误，资源管理器会为以下错误代码重试 GET 调用：

* 小于 100
* 408
* 429
* 大于 500

对于其他错误代码，它会将删除操作视为失败。

## <a name="delete-resource-group"></a>删除资源组

使用以下方法之一来删除资源组。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

1. 在[门户](https://portal.azure.com)中，选择要删除的资源组。

1. 选择“删除资源组”。

   ![删除资源组](./media/resource-group-delete/delete-group.png)

1. 若要确认删除，请键入资源组的名称

---

## <a name="delete-resource"></a>删除资源

使用以下方法之一删除资源。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

1. 在[门户](https://portal.azure.com)中，选择要删除的资源。

1. 选择“删除”。 以下屏幕截图显示了虚拟机的管理选项。

   ![删除资源](./media/resource-group-delete/delete-resource.png)

1. 出现提示时，确认删除。

---


## <a name="next-steps"></a>后续步骤

* 若要了解资源管理器概念，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。
* 有关删除命令，请参阅 [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup)、[Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) 和 [REST API](/rest/api/resources/resourcegroups/delete)。
