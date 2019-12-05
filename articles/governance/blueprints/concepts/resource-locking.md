---
title: 了解资源锁定
description: 了解 Azure 蓝图中的锁定选项，以在分配蓝图时保护资源。
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: 50f506cc57f67ca2ae2b07e342750d6c5099e739
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406407"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 蓝图中的资源锁定

仅当存在一个可以维护该一致性的机制时，大规模创建一致的环境才会真正有价值。 本文介绍 Azure 蓝图中的资源锁定的工作原理。 若要查看资源锁定的示例以及_拒绝分配_的应用，请参阅[保护新资源](../tutorials/protect-new-resources.md)教程。

## <a name="locking-modes-and-states"></a>锁定模式和状态

锁定模式适用于蓝图分配，它有三个选项： "**不锁定**"、"**只读**" 或 "不**删除**"。 在蓝图分配过程中的项目部署过程中配置锁定模式。 可以通过更新蓝图分配来设置不同的锁定模式。
但是，不能在蓝图外部更改锁定模式。

蓝图分配中的项目创建的资源具有四种状态：**未锁定**、**只读**、**无法编辑/删除**或**无法删除**。 每种项目类型都可以处于“未锁定”状态。 下表可以用于确定资源的状态：

|Mode|项目资源类型|状态|说明|
|-|-|-|-|
|不锁定|*|未锁定|资源不受蓝图保护。 此状态也用于从蓝图分配外部添加到“只读”或“不要删除”资源组项目的资源。|
|只读|资源组|无法编辑/删除|资源组是只读的，资源组上的标记无法修改。 可以从此资源组添加、移动、更改或删除“未锁定”资源。|
|只读|非资源组|只读|以任何方式都无法更改资源 -- 无更改且无法将其删除。|
|不要删除|*|无法删除|资源可以更改，但无法删除。 可以从此资源组添加、移动、更改或删除“未锁定”资源。|

## <a name="overriding-locking-states"></a>重写锁定状态

通常可以允许在订阅上具有合适的[基于角色的访问控制](../../../role-based-access-control/overview.md) (RBAC) 的某人（例如“所有者”角色）更改或删除任何资源。 当蓝图在已部署的分配中应用了锁定时，无法进行此访问。 如果使用“只读”或“不要删除”选项设置了分配，则即使订阅所有者也无法对受保护资源执行阻止的操作。

此安全措施可以保护已定义的蓝图与设计用于通过意外或以编程方式删除或更改创建的环境之间的一致性。

## <a name="removing-locking-states"></a>删除锁定状态

如果需要修改或删除受分配保护的资源，则可通过两种方法来实现。

- 将蓝图分配更新为“不锁定”锁定模式
- 删除蓝图分配

删除分配后，将删除由蓝图创建的锁定。 但是，资源会留在原地，需要通过正常方式删除。

## <a name="how-blueprint-locks-work"></a>蓝图锁定的工作原理

如果蓝图分配选择了“只读”[](../../../role-based-access-control/deny-assignments.md)或“不要删除”选项，则会在分配期间将 RBAC **拒绝分配**拒绝操作应用于项目资源。 该拒绝操作由蓝图分配的托管标识添加，并且只能通过同一托管标识从项目资源中删除。 此安全措施将强制实施锁定机制，并防止在蓝图外部删除蓝图锁定。

![蓝图拒绝对资源组的分配](../media/resource-locking/blueprint-deny-assignment.png)

每个模式的[拒绝分配属性](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties)如下所示：

|Mode |Permissions.Actions |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|只读 |**\*** |**\*/read** |SystemDefined （Everyone） |**excludedPrincipals**中的蓝图分配和用户定义 |资源组- _true_;资源- _false_ |
|不要删除 |**\*/delete** | |SystemDefined （Everyone） |**excludedPrincipals**中的蓝图分配和用户定义 |资源组- _true_;资源- _false_ |

> [!IMPORTANT]
> Azure 资源管理器可以将角色分配详细信息缓存最多 30 分钟。 因此，蓝图资源上的拒绝分配拒绝操作可能不会立即完全生效。 在此时间段内，可能无法删除将由蓝图锁保护的资源。

## <a name="exclude-a-principal-from-a-deny-assignment"></a>从拒绝分配中排除主体

在某些设计或安全方案中，可能需要将主体从蓝图分配创建的[拒绝分配](../../../role-based-access-control/deny-assignments.md)中排除。 这是在 REST API 中完成的，方法是在[创建分配](/rest/api/blueprints/assignments/createorupdate)时，将最多五个值添加到 "**锁定**" 属性中的**excludedPrincipals**数组。 下面是包含**excludedPrincipals**的请求正文示例：

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

- 按照[保护新资源](../tutorials/protect-new-resources.md)教程操作。
- 了解[蓝图生命周期](lifecycle.md)。
- 了解如何使用[静态和动态参数](parameters.md)。
- 了解如何自定义[蓝图排序顺序](sequencing-order.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。