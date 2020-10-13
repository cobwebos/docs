---
title: 如何保护资源层次结构 - Azure 治理
description: 了解如何通过层次结构设置（包括设置默认管理组）来保护资源层次结构。
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: cfaf7a732b3233536006009613cecd5aa1649dee
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951887"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>如何保护资源层次结构

资源、资源组、订阅、管理组和租户共同构成了资源层次结构。 根管理组（如 Azure 自定义角色或 Azure Policy 策略分配）的设置可能会影响资源层次结构中的每个资源。 保护资源层次结构以免发生可能会负面影响所有资源的更改，这一点很重要。

管理组现在具有可使租户管理员控制这些行为的层次结构设置。 本文介绍每个可用的层次结构设置以及如何设置它们。

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>层次结构设置的 Azure RBAC 权限

配置任何层次结构设置需要对根管理组执行以下两个资源提供程序操作：

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

这些操作只允许用户读取和更新层次结构设置。 这些操作不会提供对管理组层次结构或层次结构中的资源的任何其他访问权限。 这两个操作均可用于 Azure 内置角色“层次结构设置管理员”。

## <a name="setting---default-management-group"></a>设置 - 默认管理组

默认情况下，在租户中添加的新订阅将添加为根管理组的成员。 如果将策略分配、Azure RBAC)  (azure RBAC 的访问控制和其他调控构造分配给根管理组，则这些新订阅会立即生效。 出于此原因，许多组织不会在根管理组中应用这些构造，即使这是它们分配到的目标位置。 在其他情况下，新订阅需要一组更严格的控件，但不应将其分配给所有订阅。 此设置支持两个用例。

通过允许定义新订阅的默认管理组，可以在根管理组中应用组织范围的治理构造，并且可以定义具有更适合新订阅的策略分配或 Azure 角色分配的单独管理组。

### <a name="set-default-management-group-in-portal"></a>在门户中设置默认管理组

若要在 Azure 门户中配置此设置，请执行以下步骤：

1. 使用搜索栏搜索并选择“管理组”。

1. 在根管理组中，选择管理组名称旁边的“详细信息”。

1. 在“设置”下，选择“层次结构设置” 。

1. 选择“更改默认管理组”按钮。

   > [!NOTE]
   > 如果“更改默认管理组”按钮处于禁用状态，要么所查看的管理组不是根管理组，要么你的安全主体没有更改层次结构设置所需的权限。

1. 从层次结构中选择管理组，并使用“选择”按钮。

### <a name="set-default-management-group-with-rest-api"></a>使用 REST API 设置默认管理组

若要使用 REST API 配置此设置，请调用[层次结构设置](/rest/api/resources/hierarchysettings)终结点。 为此，请使用以下 REST API URI 和正文格式。 将 `{rootMgID}` 替换为根管理组 ID，将 `{defaultGroupID}` 替换为将成为默认管理组的管理组 ID：

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- 请求正文

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

若要将默认管理组设置回根管理组，请使用相同终结点，并将 defaultManagementGroup 设置为值 `/providers/Microsoft.Management/managementGroups/{rootMgID}`。

## <a name="setting---require-authorization"></a>设置 - 需要授权

默认情况下，任何用户都可以在租户中创建新管理组。 租户管理员可能希望仅向特定用户提供这些权限，以维护管理组层次结构中的一致性和合规性。 如果启用，则用户需要对根管理组进行 `Microsoft.Management/managementGroups/write` 操作才能创建新的子管理组。

### <a name="set-require-authorization-in-portal"></a>在门户中设置“需要授权”

若要在 Azure 门户中配置此设置，请执行以下步骤：

1. 使用搜索栏搜索并选择“管理组”。

1. 在根管理组中，选择管理组名称旁边的“详细信息”。

1. 在“设置”下，选择“层次结构设置” 。

1. 将“需要权限才能创建新管理组。” 选项切换为打开。

   > [!NOTE]
   > 如果“需要权限才能创建新管理组。” 切换功能处于禁用状态，要么所查看的管理组不是根管理组，要么你的安全主体没有更改层次结构设置所需的权限。

### <a name="set-require-authorization-with-rest-api"></a>使用 REST API 设置“需要授权”

若要使用 REST API 配置此设置，请调用[层次结构设置](/rest/api/resources/hierarchysettings)终结点。 为此，请使用以下 REST API URI 和正文格式。 此值是布尔值，因此请为该值提供 true 或 false。 值 true 允许这种保护管理组层次结构的方法：

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- 请求正文

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

若要重新打开该设置，请使用相同终结点，并将 requireAuthorizationForGroupCreation 设置为值 false。

## <a name="next-steps"></a>后续步骤

若要了解有关管理组的详细信息，请参阅：

- [创建管理组来组织 Azure 资源](../create-management-group-portal.md)
- [如何更改、删除或管理管理组](../manage.md)