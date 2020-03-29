---
title: 了解资源锁定
description: 了解 Azure 蓝图中的锁定选项，以在分配蓝图时保护资源。
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: b810e8d4ddd263f9e651704d1bf9b785ce0202db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199693"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 蓝图中的资源锁定

仅当存在一个可以维护该一致性的机制时，大规模创建一致的环境才会真正有价值。 本文介绍 Azure 蓝图中的资源锁定的工作原理。 要查看资源锁定和应用_拒绝分配_的示例，请参阅[保护新资源](../tutorials/protect-new-resources.md)教程。

## <a name="locking-modes-and-states"></a>锁定模式和状态

锁定模式适用于蓝图分配，它有三个选项：**不锁定**、**只读**或**不删除**。 在蓝图分配过程中的项目部署过程中配置锁定模式。 可以通过更新蓝图分配来设置不同的锁定模式。
但是，不能在蓝图外部更改锁定模式。

蓝图分配中工件创建的资源有四种状态：**未锁定**、**只读**、**无法编辑/删除**或**无法删除**。 每种项目类型都可以处于“未锁定”**** 状态。 下表可以用于确定资源的状态：

|“模式”|项目资源类型|状态|描述|
|-|-|-|-|
|不锁定|*|未锁定|资源不受蓝图保护。 此状态也用于从蓝图分配外部添加到“只读”**** 或“不要删除”**** 资源组项目的资源。|
|只读|资源组|无法编辑/删除|资源组是只读的，资源组上的标记无法修改。 可以从此资源组添加、移动、更改或删除“未锁定”**** 资源。|
|只读|非资源组|只读|以任何方式都无法更改资源 -- 无更改且无法将其删除。|
|不要删除|*|无法删除|资源可以更改，但无法删除。 可以从此资源组添加、移动、更改或删除“未锁定”**** 资源。|

## <a name="overriding-locking-states"></a>重写锁定状态

通常可以允许在订阅上具有合适的[基于角色的访问控制](../../../role-based-access-control/overview.md) (RBAC) 的某人（例如“所有者”角色）更改或删除任何资源。 当蓝图在已部署的分配中应用了锁定时，无法进行此访问。 如果使用“只读”**** 或“不要删除”**** 选项设置了分配，则即使订阅所有者也无法对受保护资源执行阻止的操作。

此安全措施可以保护已定义的蓝图与设计用于通过意外或以编程方式删除或更改创建的环境之间的一致性。

### <a name="assign-at-management-group"></a>在管理组分配

防止订阅所有者删除蓝图分配的另一个选项是将蓝图分配给管理组。 在这种情况下，只有管理组**的所有者**具有删除蓝图分配所需的权限。

要将蓝图分配给管理组而不是订阅，REST API 调用将更改为如下所示：

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

定义的`{assignmentMG}`管理组必须位于管理组层次结构中，或者与保存蓝图定义的管理组相同。

蓝图分配的请求正文如下所示：

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
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

此请求正文中的关键区别和分配给订阅的一个属性是属性`properties.scope`。 必须将所需的属性设置为蓝图分配应用于的订阅。 订阅必须是存储蓝图分配的管理组层次结构的直接子级。

> [!NOTE]
> 分配给管理组作用域的蓝图仍作为订阅级别的蓝图分配运行。 唯一的区别是，蓝图分配的存储位置是为了防止订阅所有者删除分配和相关锁。

## <a name="removing-locking-states"></a>删除锁定状态

如果需要修改或删除受分配保护的资源，则可通过两种方法来实现。

- 将蓝图分配更新为“不锁定”**** 锁定模式
- 删除蓝图分配

删除分配后，将删除由蓝图创建的锁定。 但是，资源会留在原地，需要通过正常方式删除。

## <a name="how-blueprint-locks-work"></a>蓝图锁定的工作原理

如果蓝图分配选择了“只读”**** 或“不要删除”**** 选项，则会在分配期间将 RBAC [拒绝分配](../../../role-based-access-control/deny-assignments.md)拒绝操作应用于项目资源。 该拒绝操作由蓝图分配的托管标识添加，并且只能通过同一托管标识从项目资源中删除。 此安全措施将强制实施锁定机制，并防止在蓝图外部删除蓝图锁定。

![蓝图拒绝资源组分配](../media/resource-locking/blueprint-deny-assignment.png)

每个模式的[拒绝分配属性](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties)如下所示：

|“模式” |权限.操作 |权限.不操作 |校长[i]。类型 |排除主体[i]。Id | 不应用于儿童范围 |
|-|-|-|-|-|-|
|只读 |**\*** |**\*/读取** |系统定义（所有人） |在**排除主体**中，蓝图分配和用户定义 |资源组 - _true_;资源 -_错误_ |
|不要删除 |**\*/删除** | |系统定义（所有人） |在**排除主体**中，蓝图分配和用户定义 |资源组 - _true_;资源 -_错误_ |

> [!IMPORTANT]
> Azure 资源管理器可以将角色分配详细信息缓存最多 30 分钟。 因此，蓝图资源上的拒绝分配拒绝操作可能不会立即完全生效。 在此时间段内，可能无法删除将由蓝图锁保护的资源。

## <a name="exclude-a-principal-from-a-deny-assignment"></a>从拒绝分配中排除主体

在某些设计或安全方案中，可能需要从蓝图分配创建的[拒绝分配](../../../role-based-access-control/deny-assignments.md)中排除主体。 此步骤在 REST API 中完成，在[创建赋值](/rest/api/blueprints/assignments/createorupdate)时，将最多五个值添加到**锁**属性中的**排除主体**数组中。 以下分配定义是包含**排除主体**的请求正文的示例：

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

## <a name="exclude-an-action-from-a-deny-assignment"></a>从拒绝分配中排除操作

与在蓝图分配中[排除](#exclude-a-principal-from-a-deny-assignment)[拒绝分配](../../../role-based-access-control/deny-assignments.md)上的主体类似，可以排除特定的[RBAC 操作](../../../role-based-access-control/resource-provider-operations.md)。 在**属性.locks**块中，在**排除主体**的同一位置，可以添加**排除的操作**：

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

虽然**排除主体**必须是显式的，**但排除的操作**条目可用于`*`RBAC 操作的通配符匹配。

## <a name="next-steps"></a>后续步骤

- 请按照[保护新资源](../tutorials/protect-new-resources.md)教程进行操作。
- 了解[蓝图生命周期](lifecycle.md)。
- 了解如何使用[静态和动态参数](parameters.md)。
- 学习自定义[蓝图排序顺序](sequencing-order.md)。
- 了解如何[更新现有作业](../how-to/update-existing-assignments.md)。
- 在分配蓝图期间使用[常规故障排除时](../troubleshoot/general.md)解决问题。