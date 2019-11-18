---
title: 部署历史记录
description: 介绍如何通过门户、PowerShell、Azure CLI 和 REST API 查看 Azure 资源管理器部署操作。
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: d8daf7191bb22f7c7057f6ef6b220a18868872cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149571"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>使用 Azure 资源管理器查看部署历史记录

使用 Azure 资源管理器可以查看部署历史记录并检查过去部署中的特定操作。 你可以查看已部署的资源，并获取有关任何错误的信息。

有关解决特定部署错误的帮助，请参阅[解决使用 Azure 资源管理器将资源部署到 Azure 时的常见错误](resource-manager-common-deployment-errors.md)。

## <a name="portal"></a>门户

从部署历史记录中获取有关部署的详细信息。

1. 选择要检查的资源组。

1. 选择“部署”下面的链接。

   ![选择部署历史记录](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. 从部署历史记录中选择一个部署。

   ![选择部署](./media/resource-manager-deployment-operations/select-details.png)

1. 将显示部署的摘要，包括已部署资源的列表。

    ![部署摘要](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. 若要查看用于部署的模板，请选择 "**模板**"。 您可以下载模板以重复使用它。

    ![显示模板](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. 如果部署失败，会显示一条错误消息。 选择错误消息以了解更多详细信息。

    ![查看失败的部署](./media/resource-manager-deployment-operations/show-error.png)

1. 将显示详细的错误消息。

    ![查看错误详细信息](./media/resource-manager-deployment-operations/show-details.png)

1. 相关 ID 用于跟踪相关事件，在与技术支持部门合作排查部署问题时非常有用。

    ![获取相关 ID](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. 若要了解有关失败步骤的详细信息，请选择 "**操作详细信息**"。

    ![选择部署操作](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. 你将看到该部署步骤的详细信息。

    ![显示操作详细信息](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要获取部署的总体状态，请使用 **Get-AzResourceGroupDeployment** 命令。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

也可以筛选结果，以便只获取失败的部署。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

相关 ID 用于跟踪相关事件，在与技术支持部门合作排查部署问题时非常有用。 若要获取相关 ID，请使用：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

每个部署包括多个操作。 每个操作代表部署过程中的一个步骤。 为了查明部署何处出现问题，通常需要查看部署操作的详细信息。 可以使用 **Get-AzResourceGroupDeploymentOperation** 查看操作状态。

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

它将返回多个操作，其中每个操作采用以下格式：

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

若要获取有关失败操作的更多详细信息，请检索状态为“失败”的操作的属性。

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

它将返回所有失败的操作，其中每个操作采用以下格式：

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

注意操作的 serviceRequestId 和 trackingId。 与技术支持人员合作排查部署问题时，serviceRequestId 非常有用。 你将在下一步中使用 trackingId 来重点关注特定操作。

若要获取特定失败操作的状态消息，请使用以下命令：

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

将返回：

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Azure 中的每个部署操作均包括请求和响应内容。 在部署期间，可以使用**DeploymentDebugLogLevel**参数来指定记录请求和/或响应。

使用以下 PowerShell 命令从日志中获取该信息，并将其保存在本地：

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

若要获取部署的总体状态，请使用**azure group deployment show**命令。

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
相关 ID 用于跟踪相关事件，在与技术支持部门合作排查部署问题时非常有用。

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

若要查看部署操作，请使用：

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

若要获取有关部署的信息，请使用[获取有关模板部署](https://docs.microsoft.com/rest/api/resources/deployments)操作的信息。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

在响应中，请特别注意 **provisioningState**、**correlationId** 和 **error** 元素。 **correlationId** 可用于跟踪相关事件，在与技术支持人员合作排查部署问题时非常有用。

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

若要获取有关部署的信息，请使用[列出所有模板部署操作](https://docs.microsoft.com/rest/api/resources/deployments)。 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
响应包含请求和/或响应信息，具体取决于部署期间在 **debugSetting** 属性中指定的内容。

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>后续步骤
* 有关解决特定部署错误的帮助，请参阅[解决使用 Azure 资源管理器将资源部署到 Azure 时的常见错误](resource-manager-common-deployment-errors.md)。
* 若要了解如何使用活动日志监视其他类型的操作，请参阅[通过查看活动日志管理 Azure 资源](resource-group-audit.md)。
* 若要在执行部署之前验证部署，请参阅[使用 Azure 资源管理器模板部署资源组](resource-group-template-deploy.md)。

