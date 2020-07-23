---
title: 部署历史记录删除
description: 介绍 Azure 资源管理器如何从部署历史记录中自动删除部署。 当历史记录即将超过限制（800 条）时，将删除部署。
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 8ec3291dc5e35689d4e2c614949e0328057fbfd3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248969"
---
# <a name="automatic-deletions-from-deployment-history"></a>从部署历史记录自动删除

每次部署模板时，有关部署的信息都会写入到部署历史记录中。 每个资源组在其部署历史记录中最多只能有 800 个部署。

Azure 资源管理器很快就会开始从历史记录中自动删除部署，就像你这样的限制。 自动删除是对过去的行为的更改。 以前，必须从部署历史记录中手动删除部署，以避免出现错误。 **尚未将此功能添加到 Azure。如果你想要退出，我们会通知你即将发生的更改。**

> [!NOTE]
> 从历史记录中删除部署不会影响已部署的任何资源。
>
> 如果对资源组使用 [CanNotDelete 锁](../management/lock-resources.md)，则无法删除该资源组的部署。 必须删除锁才能利用部署历史记录中的自动删除功能。

## <a name="when-deployments-are-deleted"></a>删除部署时

当你达到775或更多部署时，将从历史记录中删除部署。 在历史记录下降到750之前，Azure 资源管理器会删除部署。 始终最先删除最早的部署。

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="从部署历史记录中删除内容":::

> [!NOTE]
> 起始编号 (775) ， (750) 的结束数字可能会更改。
>
> 如果资源组已经达到 800 条记录的限制，则下一个部署将失败并出现错误。 会立即启动自动删除过程。 等待片刻后，可以再次尝试进行部署。

除了部署之外，还可以在运行 [what-if 操作](template-deploy-what-if.md)或验证部署时触发删除操作。

为部署指定与历史记录中的部署相同的名称时，将重置其在历史记录中的位置。 部署会移至历史记录中的最新位置。 在发生错误后[回退到该部署](rollback-on-error.md)时，也会重置部署的位置。

## <a name="opt-out-of-automatic-deletions"></a>选择退出自动删除

可以选择退出从历史记录中自动删除条目的功能。 仅当要自行管理部署历史记录时才使用此选项。**** 仍强制实施在历史记录中保留 800 个部署的限制。 如果超过 800 个部署，你将收到错误，并且部署将失败。

要禁用自动删除，请注册 `Microsoft.Resources/DisableDeploymentGrooming` 功能标志。 注册功能标志时，即为整个 Azure 订阅选择退出自动删除功能。 不能仅为特定资源组选择退出。 若要重新启用自动删除，请注销功能标志。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 PowerShell，请使用 [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature)。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

要查看订阅的当前状态，请使用：

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

若要重新启用自动删除，请使用 Azure REST API 或 Azure CLI。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az feature register](/cli/azure/feature#az-feature-register)。

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

要查看订阅的当前状态，请使用：

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

若要重新启用自动删除，请使用[az feature 取消注册](/cli/azure/feature#az-feature-unregister)。

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

对于 REST API，请使用[功能 - 注册](/rest/api/resources/features/register)。

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

要查看订阅的当前状态，请使用：

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

若要重新启用自动删除，请使用[功能-取消注册](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>后续步骤

* 要了解如何查看部署历史记录，请参阅[使用 Azure 资源管理器查看部署历史记录](deployment-history.md)。
