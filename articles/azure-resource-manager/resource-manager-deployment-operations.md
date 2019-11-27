---
title: 部署历史记录
description: 介绍如何通过门户、PowerShell、Azure CLI 和 REST API 查看 Azure 资源管理器部署操作。
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 895704e5c4cb8acc60067809bdd7e7baa6f05142
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538898"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>使用 Azure 资源管理器查看部署历史记录

使用 Azure 资源管理器可以查看部署历史记录并检查过去部署中的特定操作。 你可以查看已部署的资源，并获取有关任何错误的信息。

有关解决特定部署错误的帮助，请参阅[解决使用 Azure 资源管理器将资源部署到 Azure 时的常见错误](resource-manager-common-deployment-errors.md)。

## <a name="get-deployments-and-correlation-id"></a>获取部署和相关性 ID

可以通过 Azure 门户、PowerShell、Azure CLI 或 REST API 查看有关部署的详细信息。 每个部署都有一个相关 ID，用于跟踪相关事件。 与技术支持人员合作排查部署问题时，此方法非常有用。

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. 选择要检查的资源组。

1. 选择“部署”下面的链接。

   ![选择部署历史记录](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. 从部署历史记录中选择一个部署。

   ![选择部署](./media/resource-manager-deployment-operations/select-details.png)

1. 将显示部署的摘要，包括相关 ID。 

    ![部署摘要](./media/resource-manager-deployment-operations/show-correlation-id.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

若要列出资源组的所有部署，请使用[AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)命令。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

若要从资源组中获取特定部署，请添加**DeploymentName**参数。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

若要获取相关 ID，请使用：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要列出资源组的部署，请使用[az group deployment list](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-list)。

```azurecli-interactive
az group deployment list --resource-group ExampleGroup
```

若要获取特定部署，请使用[az group deployment show](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-show)。

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment
```
  
若要获取相关 ID，请使用：

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

若要列出资源组的部署，请使用以下操作。 若要在请求中使用最新的 API 版本号，请参阅[按资源组列出的部署](/rest/api/resources/deployments/listbyresourcegroup)。 

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

获取特定的部署。 使用以下操作。 若要在请求中使用最新的 API 版本号，请参阅[部署-获取](/rest/api/resources/deployments/get)。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

响应包括相关 ID。

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

每个部署可包含多个操作。 若要查看有关部署的更多详细信息，请查看部署操作。 部署失败时，部署操作将包含一条错误消息。

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. 在 "部署摘要" 中，选择 "**操作详细信息**"。

    ![选择部署操作](./media/resource-manager-deployment-operations/get-operation-details.png)

1. 你将看到该部署步骤的详细信息。 出现错误时，详细信息包含错误消息。

    ![显示操作详细信息](./media/resource-manager-deployment-operations/see-operation-details.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

若要查看部署到资源组的部署操作，请使用[AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation)命令。

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

若要查看失败的操作，请查看状态为 "**失败**" 的筛选器操作。

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

若要获取失败操作的状态消息，请使用以下命令：

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要查看部署对资源组的部署操作，请使用[az group deployment operation list](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-group-deployment-operation-list)命令。

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeployment
```

若要查看失败的操作，请查看状态为 "**失败**" 的筛选器操作。

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

若要获取失败操作的状态消息，请使用以下命令：

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

若要获取部署操作，请使用以下操作。 若要在请求中使用最新的 API 版本号，请参阅[部署操作-列表](/rest/api/resources/deploymentoperations/list)。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

响应包括一条错误消息。

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

* 有关解决特定部署错误的帮助，请参阅[解决使用 Azure 资源管理器将资源部署到 Azure 时的常见错误](resource-manager-common-deployment-errors.md)。
* 若要了解如何使用活动日志监视其他类型的操作，请参阅[通过查看活动日志管理 Azure 资源](resource-group-audit.md)。
* 若要在执行部署之前验证部署，请参阅[使用 Azure 资源管理器模板部署资源组](resource-group-template-deploy.md)。

