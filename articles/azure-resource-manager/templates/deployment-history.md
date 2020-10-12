---
title: 部署历史记录
description: 介绍如何通过门户、PowerShell、Azure CLI 和 REST API 查看 Azure Resource Manager 部署操作。
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 00e0c51244d5c191d4c9f05f689b90ece81ec5a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91284612"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>使用 Azure 资源管理器查看部署历史记录

通过 Azure 资源管理器，你能够查看部署历史记录。 可以检查过去的部署中的特定操作和部署的资源。 此历史记录包含有关任何错误的信息。

一个资源组的部署历史记录限含 800 个部署。 接近限制时，将自动从历史记录中删除部署。 有关详细信息，请参阅[从部署历史记录中自动删除](deployment-history-deletions.md)。

如需帮助解决特定部署错误，请参阅[解决使用 Azure Resource Manager 将资源部署到 Azure 时的常见错误](common-deployment-errors.md)。

## <a name="get-deployments-and-correlation-id"></a>获取部署和相关 ID

可通过 Azure 门户、PowerShell、Azure CLI 或 REST API 查看部署详细信息。 每个部署都有一个相关 ID，用于跟踪相关的事件。 如果 [创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)，则支持人员可能会要求你提供相关 ID。 支持人员使用相关 ID 识别失败部署的操作。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 选择要检查的资源组。

1. 选择“部署”**** 下的链接。

   ![选择部署历史记录](./media/deployment-history/select-deployment-history.png)

1. 从部署历史记录中选择一个部署。

   ![选择部署](./media/deployment-history/select-details.png)

1. 会显示部署摘要，包括相关 ID。

    ![部署摘要](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要列出一个资源组的所有部署，请使用 [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) 命令。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

若要获取资源组的特定部署，请添加 **DeploymentName** 参数。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

若要获取相关 ID，请使用：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要列出资源组部署，请使用 [az 部署组列表](/cli/azure/group/deployment#az-deployment-group-list)。

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

若要获取特定部署，请使用 [az 部署组显示](/cli/azure/group/deployment#az-deployment-group-show)。

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

若要获取相关 ID，请使用：

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

若要列出某个资源组的部署，请使用以下操作。 如需在请求中使用的最新 API 版本号，请参阅[部署 - 按资源组列出](/rest/api/resources/deployments/listbyresourcegroup)。

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

若要获取特定部署， 请使用以下操作。 如需在请求中使用的最新 API 版本号，请参阅[部署 - 获取](/rest/api/resources/deployments/get)。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

响应包含相关 ID。

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>获取部署操作和错误消息

每个部署可能包括多个操作。 若要查看某个部署的更多详细信息，请参阅部署操作。 当部署失败时，部署操作会包含一条错误消息。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在部署的摘要上，选择“操作详细信息”。****

    ![选择操作详细信息](./media/deployment-history/get-operation-details.png)

1. 会看到部署的该步骤的详细信息。 发生错误时，详细信息会包含错误消息。

    ![显示操作详细信息](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要查看部署到某个资源组时的部署操作，请使用 [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) 命令。

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

若要查看失败的操作，请使用“失败”状态筛选操作。****

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

若要获取失败操作的状态消息，请使用以下命令：

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要查看部署到资源组的部署操作，请使用 [az deployment operation group list](/cli/azure/deployment/operation/group#az-deployment-operation-group-list) 命令。 必须具有 Azure CLI 2.6.0 或更高版本。

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

若要查看失败操作，请筛选具有“失败”**** 状态的操作。

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

若要获取失败操作的状态消息，请使用以下命令：

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

若要获取部署操作，请使用以下操作。 如需在请求中使用的最新 API 版本号，请参阅[部署操作 - 列出](/rest/api/resources/deploymentoperations/list)。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

响应包含错误消息。

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>后续步骤

* 如需帮助解决特定部署错误，请参阅[解决使用 Azure Resource Manager 将资源部署到 Azure 时的常见错误](common-deployment-errors.md)。
* 要了解历史记录中的部署是如何进行管理的，请参阅[从部署历史记录中自动删除](deployment-history-deletions.md)。
* 若要在执行部署之前验证部署，请参阅[使用 Azure Resource Manager 模板部署资源组](deploy-powershell.md)。

