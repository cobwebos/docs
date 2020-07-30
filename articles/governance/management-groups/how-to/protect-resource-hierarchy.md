---
title: 如何保护资源层次结构 - Azure 治理
description: 了解如何通过层次结构设置（包括设置默认管理组）来保护资源层次结构。
ms.date: 05/21/2020
ms.topic: conceptual
ms.openlocfilehash: cdaad59d136e89c595a6a42a9760c73523a977c0
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422821"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>如何保护资源层次结构

资源、资源组、订阅、管理组和租户共同构成了资源层次结构。 根管理组中的设置（如 Azure 自定义角色或 Azure 策略策略分配）可能会影响资源层次结构中的每个资源。 保护资源层次结构以免发生可能会负面影响所有资源的更改，这一点很重要。

管理组现在具有可使租户管理员控制这些行为的层次结构设置。 本文介绍每个可用的层次结构设置以及如何设置它们。

## <a name="rbac-permissions-for-hierarchy-settings"></a>层次结构设置的 RBAC 权限

配置任何层次结构设置需要对根管理组执行以下两个 RBAC 操作：

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

这些操作只允许用户读取和更新层次结构设置。 这些操作不会提供对管理组层次结构或层次结构中的资源的任何其他访问权限。 Azure 内置角色**层次结构设置管理员**中提供了这两项操作。

## <a name="setting---default-management-group"></a>设置 - 默认管理组

默认情况下，在租户中添加的新订阅将添加为根管理组的成员。 如果将策略分配、基于角色的访问控制 (RBAC) 和其他治理构造分配给根管理组，则它们会立即影响这些新订阅。 出于此原因，许多组织不会在根管理组中应用这些构造，即使这是它们分配到的目标位置。 在其他情况下，新订阅需要一组更严格的控件，但不应将其分配给所有订阅。 此设置支持两个用例。

通过允许定义新订阅的默认管理组，可在根管理组中应用组织范围内的调控构造，并且可以定义更适合于新订阅的单独管理组（具有策略分配或 Azure 角色分配）。

若要配置此设置，请调用[层次结构设置](/rest/api/resources/hierarchysettings) REST API 终结点。 为此，请使用以下 REST API URI 和正文格式。 将 `{rootMgID}` 替换为根管理组 ID，将 `{defaultGroupID}` 替换为将成为默认管理组的管理组 ID：

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

若要配置此设置，请调用[层次结构设置](/rest/api/resources/hierarchysettings) REST API 终结点。 为此，请使用以下 REST API URI 和正文格式。 此值是布尔值，因此请为该值提供 true 或 false。 值 true 允许这种保护管理组层次结构的方法：

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

- [创建管理组来组织 Azure 资源](../create.md)
- [如何更改、删除或管理管理组](../manage.md)
