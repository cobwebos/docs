---
title: 快速入门：使用 REST API 进行新策略分配
description: 本快速入门介绍如何使用 REST API 创建 Azure Policy 分配以识别不合规资源。
ms.date: 08/10/2020
ms.topic: quickstart
ms.openlocfilehash: 04880ef013060bc5ff12618af6a9156295a26a88
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88137081"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>快速入门：使用 REST API 创建策略分配以识别不合规资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。
本快速入门逐步讲解如何创建策略分配，以识别未使用托管磁盘的虚拟机。

此过程结束时，你可以成功识别哪些虚拟机未使用托管磁盘。 这些虚拟机不符合策略分配要求。 

REST API 用于创建和管理 Azure 资源。 本指南使用 REST API 创建策略分配，并识别 Azure 环境中的不合规资源。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- 安装 [ARMClient](https://github.com/projectkudu/ARMClient)（如果尚未安装）。 该工具可将 HTTP 请求发送到基于 Azure 资源管理器的 REST API。 你也可以使用 REST 文档中的“试用”功能，或者使用 PowerShell 的 [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) 或 [Postman](https://www.postman.com) 等工具。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>创建策略分配

本快速入门将创建一个策略分配，并分配“审核未使用托管磁盘的 VM”(`06a78e20-9358-41c9-923c-fb736d382a4d`) 定义。 此策略定义可识别不符合策略定义中设置的条件的资源。

运行以下命令创建策略分配：

   - REST API URI

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - 请求正文

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
       }
     }
     ```

前面的终结点和请求正文使用以下信息：

REST API URI：
- **范围** - 范围确定在其中实施策略分配的资源或资源组。 它的范围可以从管理组到单个资源。 请确保将 `{scope}` 替换为以下某个模式：
  - 管理组：`/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 订阅：`/subscriptions/{subscriptionId}`
  - 资源组：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 资源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **名称** - 分配的实际名称。 对于此示例，使用 _audit-vm-manageddisks_。

请求正文：
- **显示名称** - 策略分配的显示名称。 本例使用了“审核未使用托管磁盘分配的虚拟机”  。
- **说明** - 有关策略用途或将其分配到此范围的原因的更深入说明。
- **policyDefinitionId** - 策略定义 ID，用作创建分配的依据。 在本例中，它为策略定义“审核未使用托管磁盘的 VM”的 ID  。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

若要查看不符合此新分配的资源，请运行以下命令，以获取输出到 JSON 文件的不合规资源的资源 ID：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
```

结果应如以下示例所示：

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

这些结果与 Azure 门户视图中“不合规资源”下通常所列的结果类似。 

## <a name="clean-up-resources"></a>清理资源

要删除创建的分配，请使用以下命令：

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

将 `{scope}` 替换为最初创建策略分配时使用的范围。

## <a name="next-steps"></a>后续步骤

本快速入门已分配一个策略定义用于识别 Azure 环境中的不合规资源。

要了解有关分配策略以验证新资源是否符合要求的详细信息，请继续以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./tutorials/create-and-manage.md)
