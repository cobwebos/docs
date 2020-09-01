---
title: 使用 Azure 资源管理器模板部署用于 Redis 的 Azure 缓存
description: 了解如何使用 Azure 资源管理器模板部署 Azure Cache for Redis 资源。 为常见方案提供模板。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 3c2d13794f2fc3af4541032d1f94967681c0deee
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078412"
---
# <a name="create-an-azure-cache-for-redis-using-a-resource-manager-template"></a>使用资源管理器模板创建用于 Redis 的 Azure 缓存

了解如何创建用于 Redis 部署 Azure 缓存的 Azure 资源管理器模板。 该缓存可以用于现有存储帐户以保存诊断数据。 还将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。 目前，对订阅的同一区域中的所有缓存共享诊断设置。 更新区域中的一个缓存会影响该区域中的所有其他缓存。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* **一个存储帐户**：若要创建一个，请参阅 [创建 Azure 存储帐户](/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=template)。 存储帐户用于诊断数据。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-redis-cache/)。

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

该模板中定义了以下资源：

* [Redis/缓存](/azure/templates/microsoft.cache/redis)
* [Microsoft Insights/diagnosticsettings](/azure/templates/diagnosticsettings)

新 [高级层](cache-overview.md#service-tiers) 的资源管理器模板也可用。

* [通过群集创建高级 Azure Redis 缓存](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [通过数据暂留创建高级 Azure Redis 缓存](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Create Premium Redis Cache deployed into a Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)（创建部署到虚拟网络中的高级 Redis 缓存）

若要检查最新模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/)并搜索 `Azure Cache for Redis`。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开模板。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. 选择或输入以下值：

    * 订阅：选择用于创建数据共享和其他资源的 Azure 订阅。
    * 资源组：选择“新建”以创建新的资源组，或选择现有资源组。
    * **位置**：选择资源组的位置。 存储帐户和 Redis 缓存必须位于同一区域。 默认情况下，Redis 缓存使用与资源组相同的位置。 因此，指定与存储帐户相同的位置。
    * **Redis 缓存名称**：输入 Redis 缓存的名称。
    * **现有的诊断存储帐户**：输入存储帐户的资源 ID。 语法为 **/Subscriptions/ &lt; 订阅 ID>/RESOURCEGROUPS/ &lt; 资源组名称>/PROVIDERS/MICROSOFT.STORAGE/STORAGEACCOUNTS/ &lt; 存储帐户名称>**。

    其余设置均采用默认值。
1. 选择“我同意上述条款和条件”，然后选择“购买” 。

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 打开创建的 Redis 缓存。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组，可以将其删除，这将删除资源组中的资源。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>后续步骤

本教程介绍如何创建 Azure 资源管理器模板，用于部署用于 Redis 的 Azure 缓存。 若要了解如何使用 Azure Cache for Redis 创建用于部署 Azure Web 应用的 azure 资源管理器模板，请参阅 [使用模板创建 Web 应用和用于 Redis 的 Azure 缓存](./cache-web-app-arm-with-redis-cache-provision.md)。