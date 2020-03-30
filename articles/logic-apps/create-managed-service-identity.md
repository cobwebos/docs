---
title: 使用托管标识进行身份验证
description: 使用托管标识访问其他 Azure 活动目录租户中的资源，而无需使用凭据或机密登录
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117357"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>使用 Azure 逻辑应用中的托管标识对 Azure 资源的访问进行身份验证

要访问其他 Azure 活动目录 （Azure AD） 租户中的资源并在未登录的情况下对标识进行身份验证，逻辑应用可以使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)（以前托管服务标识或 MSI），而不是凭据或机密。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。

Azure 逻辑应用同时支持[*系统分配*](../active-directory/managed-identities-azure-resources/overview.md)和[*用户分配的*](../active-directory/managed-identities-azure-resources/overview.md)托管标识。 逻辑应用可以使用系统分配的标识或*单个*用户分配的标识，您可以在一组逻辑应用之间共享，但不能同时共享这两个标识。 目前，只有[特定的内置触发器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)支持托管标识，不支持托管连接器或连接，例如：

* HTTP
* Azure Functions
* Azure API 管理
* Azure 应用服务

本文演示如何为逻辑应用设置这两种类型的托管标识。 有关详细信息，请参阅以下主题：

* [支持托管标识的触发器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [出站呼叫上受支持的身份验证类型](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [逻辑应用的托管标识限制](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [支持具有托管标识的 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。 托管标识和目标 Azure 资源（需要访问）都必须使用相同的 Azure 订阅。

* 要授予对 Azure 资源的托管标识访问权限，需要向该标识的目标资源添加角色。 要添加角色，需要[Azure AD 管理员权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)，这些权限可以将角色分配给相应 Azure AD 租户中的标识。

* 要访问的目标 Azure 资源。 在此资源上，您将为托管标识添加一个角色，这有助于逻辑应用对目标资源的访问权限进行身份验证。

* 要使用[支持托管标识的触发器或操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)的逻辑应用

## <a name="enable-managed-identity"></a>启用托管标识

要设置要使用的托管标识，请按照该标识的链接操作：

* [系统分配的标识](#system-assigned)
* [用户分配的标识](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>启用系统分配的标识

与用户分配的身份不同，您不必手动创建系统分配的标识。 要为逻辑应用设置系统分配的标识，可以使用以下选项：

* [Azure 门户](#azure-portal-system-logic-app)
* [Azure 资源管理器模板](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>在 Azure 门户中启用系统分配的标识

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“标识”********。 选择 > **在** > **保存**时**分配的系统**。 当 Azure 提示您确认时，请选择"**是**"。

   ![启用系统分配的标识](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > 如果收到一个错误，即只能具有单个托管标识，则逻辑应用已与用户分配的标识关联。 在添加系统分配的标识之前，必须首先从逻辑应用*中删除*用户分配的标识。

   逻辑应用现在可以使用系统分配的标识，该标识在 Azure 活动目录中注册，并由对象 ID 表示。

   ![系统分配标识的对象 ID](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | properties | “值” | 描述 |
   |----------|-------|-------------|
   | **对象 ID** | <*标识资源 ID*> | 表示 Azure AD 租户中逻辑应用的系统分配标识的全局唯一标识符 （GUID） |
   ||||

1. 现在，请按照本主题稍后将[授予该标识对资源访问权限的步骤](#access-other-resources)进行操作。

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中启用系统分配的标识

要自动创建和部署 Azure 资源（如逻辑应用），可以使用[Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。 要在模板中为逻辑应用启用系统分配的托管标识，请`identity`将对象和`type`子属性添加到模板中的逻辑应用的资源定义中，例如：

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

当 Azure 创建逻辑应用资源定义时`identity`，对象将获取以下其他属性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 属性 (JSON) | “值” | 描述 |
|-----------------|-------|-------------|
| `principalId` | <*本金 ID*> | 表示 Azure AD 租户中逻辑应用的托管标识的服务主体对象的全局唯一标识符 （GUID）。 此 GUID 有时显示为"对象 ID"`objectID`或 。 |
| `tenantId` | <*Azure-AD-租户-ID*> | 表示逻辑应用现在是其成员的 Azure AD 租户的全局唯一标识符 （GUID）。 在 Azure AD 租户内，服务主体与逻辑应用实例具有相同名称。 |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>启用用户分配的标识

要为逻辑应用设置用户分配的托管标识，必须首先将该标识创建为单独的独立 Azure 资源。 以下是您可以使用的选项：

* [Azure 门户](#azure-portal-user-identity)
* [Azure 资源管理器模板](#template-user-identity)
* Azure PowerShell
  * [创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [添加角色分配](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [添加角色分配](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [添加角色分配](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>在 Azure 门户中创建用户分配的标识

1. 在[Azure 门户](https://portal.azure.com)中，在任何页面上的搜索框中输入`managed identities`，然后选择**托管标识**。

   ![查找并选择"托管标识"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. 在**托管标识**下，选择 **"添加**"。

   ![添加新托管标识](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 提供有关托管标识的信息，然后选择 **"创建**"，例如：

   ![创建用户分配的托管标识](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | properties | 必选 | “值” | 描述 |
   |----------|----------|-------|-------------|
   | 资源名称**** | 是 | <*用户分配标识名称*> | 要提供用户分配的标识的名称。 此示例使用"Fabrikam-用户分配标识"。 |
   | **订阅** | 是 | <*Azure 订阅名称*> | 要使用的 Azure 订阅的名称 |
   | **资源组** | 是 | <*Azure 资源组名称*> | 要使用的资源组的名称。 创建新组，或选择现有组。 本示例创建一个名为"fabrikam 管理身份-RG"的新组。 |
   | **位置** | 是 | <*Azure 区域*> | 要存储有关资源信息的 Azure 区域。 此示例使用“美国西部”。 |
   |||||

   现在，您可以将用户分配的标识添加到逻辑应用。 不能向逻辑应用添加多个用户分配的标识。

1. 在 Azure 门户中的逻辑应用设计器中查找并打开逻辑应用。

1. 在逻辑应用菜单中，在 **"设置"** 下，选择 **"标识**"，然后选择 **"用户分配** > **添加**"。

   ![添加用户分配的托管标识](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. 在"**添加用户分配的托管标识**"窗格中，从 **"订阅"** 列表中选择 Azure 订阅（如果尚未选择）。 从显示该订阅*中所有*托管标识的列表中，查找并选择所需的用户分配的标识。 要筛选列表，请在 **"用户分配"托管标识**搜索框中输入标识或资源组的名称。 完成后，选择 **"添加**"。

   ![选择要使用的用户分配标识](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > 如果收到一个错误，即只能具有单个托管标识，则逻辑应用已与系统分配的标识相关联。 在添加用户分配的标识之前，必须首先禁用逻辑应用中的系统分配标识。

   逻辑应用现在与用户分配的托管标识相关联。

   ![与用户分配的标识关联](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 现在，请按照本主题稍后将[授予该标识对资源访问权限的步骤](#access-other-resources)进行操作。

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>在 Azure 资源管理器模板中创建用户分配的标识

要自动创建和部署 Azure 资源（如逻辑应用），可以使用[Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，支持[用户分配的标识进行身份验证](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)。 在模板部分`resources`中，逻辑应用的资源定义需要以下项：

* `type`属性设置为 的对象`identity``UserAssigned`

* 指定标识`userAssignedIdentities`的资源 ID 的子对象，该 ID 是具有 和`principalId``clientId`属性的另一个子对象

此示例显示 HTTP PUT 请求的逻辑应用资源定义，并包含非参数化`identity`对象。 对 PUT 请求和后续 GET 操作的响应`identity`也具有以下对象：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| 属性 (JSON) | “值” | 描述 |
|-----------------|-------|-------------|
| `principalId` | <*本金 ID*> | Azure AD 租户中用户分配的托管标识的全局唯一标识符 （GUID） |
| `clientId` | <*客户端 ID*> | 逻辑应用的新标识的全球唯一标识符 （GUID），用于运行时的调用 |
||||

如果模板还包括托管标识的资源定义，则可以对`identity`对象进行参数化。 此示例显示子`userAssignedIdentities`对象如何引用您在模板`userAssignedIdentity``variables`部分中定义的变量。 此变量引用用户分配的标识的资源 ID。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| 属性 (JSON) | “值” | 描述 |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-租户-ID*> | 表示 Azure AD 租户的全局唯一标识符 （GUID），用户分配的标识现在是其成员。 在 Azure AD 租户中，服务主体的名称与用户分配的标识名称相同。 |
| `principalId` | <*本金 ID*> | Azure AD 租户中用户分配的托管标识的全局唯一标识符 （GUID） |
| `clientId` | <*客户端 ID*> | 逻辑应用的新标识的全球唯一标识符 （GUID），用于运行时的调用 |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>授予对资源的标识访问权限

在可以使用逻辑应用的托管标识进行身份验证之前，请在计划使用该标识的 Azure 资源上设置该标识的访问权限。 要完成此任务，请为目标 Azure 资源上的该标识分配适当的角色。 以下是您可以使用的选项：

* [Azure 门户](#azure-portal-assign-access)
* [Azure Resource Manager 模板](../role-based-access-control/role-assignments-template.md)
* Azure 电源外壳[（New-AzRole 分配](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)） - 有关详细信息，请参阅[使用 Azure RBAC 和 Azure PowerShell 添加角色分配](../role-based-access-control/role-assignments-powershell.md)。
* Azure CLI（az[角色分配创建](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)） - 有关详细信息，请参阅[使用 Azure RBAC 和 Azure CLI 添加角色分配](../role-based-access-control/role-assignments-cli.md)。
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>在 Azure 门户中分配访问权限

1. 在[Azure 门户](https://portal.azure.com)中，转到 Azure 资源，希望托管标识具有访问权限。

1. 从资源的菜单中选择 **"访问控制 （IAM）** > **角色分配"，** 您可以在其中查看该资源的当前角色分配。 在工具栏上，选择 **"添加** > **角色分配**"。

   ![选择"添加">"添加角色分配"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > 如果禁用"**添加角色分配"** 选项，则很可能没有权限。 有关允许您管理资源角色的权限的详细信息，请参阅[Azure 活动目录中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

1. 在 **"添加角色分配**"下，选择一个角色，该**角色**使您的身份能够对目标资源进行必要的访问权限。

   在本主题示例中，标识需要一个角色[，该角色可以访问 Azure 存储容器中的 blob。](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

   ![选择"存储 Blob 数据参与者"角色](./media/create-managed-service-identity/select-role-for-identity.png)

1. 按照以下步骤进行托管标识：

   * **系统分配的标识**

     1. 在"**分配对访问的访问**"框中，选择**逻辑应用**。 显示 **"订阅"** 属性时，选择与您的标识关联的 Azure 订阅。

        ![选择系统分配标识的访问](./media/create-managed-service-identity/assign-access-system.png)

     1. 在 **"选择"** 框中，从列表中选择逻辑应用。 如果列表太长，请使用 **"选择"** 框筛选列表。

        ![为系统分配的标识选择逻辑应用](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **用户分配的标识**

     1. 在"**分配对访问权限"** 框中，选择 **"用户分配"托管标识**。 显示 **"订阅"** 属性时，选择与您的标识关联的 Azure 订阅。

        ![选择用户分配标识的访问](./media/create-managed-service-identity/assign-access-user.png)

     1. 在 **"选择"** 框中，从列表中选择您的身份。 如果列表太长，请使用 **"选择"** 框筛选列表。

        ![选择用户分配的标识](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. 完成后，选择“保存”****。

   目标资源的角色分配列表现在显示所选的托管标识和角色。 此示例演示如何将系统分配的标识用于一个逻辑应用，以及如何将用户分配的标识用于一组其他逻辑应用。

   ![添加托管标识和角色以定位资源](./media/create-managed-service-identity/added-roles-for-identities.png)

   有关详细信息，请使用[Azure 门户将托管标识访问权限分配给资源](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。

1. 现在，按照以下步骤对支持托管标识的触发器或操作中的[标识进行身份验证](#authenticate-access-with-identity)。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>使用托管标识验证访问权限

[为逻辑应用启用托管标识](#azure-portal-system-logic-app)并[授予对目标资源或实体的标识访问权限](#access-other-resources)后，可以在[支持托管标识的触发器和操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)中使用该标识。

> [!IMPORTANT]
> 如果具有要使用系统分配的标识的 Azure 函数，则首先[为 Azure 函数启用身份验证](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

这些步骤演示如何通过 Azure 门户将托管标识与触发器或操作一起使用。 要在触发器或操作的基础 JSON 定义中指定托管标识，请参阅[托管标识身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 如果尚未这样做，则添加[支持托管标识的触发器或操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

   例如，HTTP 触发器或操作可以使用为逻辑应用启用的系统分配的标识。 通常，HTTP 触发器或操作使用这些属性来指定要访问的资源或实体：

   | properties | 必选 | 描述 |
   |----------|----------|-------------|
   | **方法** | 是 | 要运行的操作使用的 HTTP 方法 |
   | **URI** | 是 | 用于访问目标 Azure 资源或实体的终结点 URL。 URI 语法通常包括 Azure[资源或服务的资源 ID。](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) |
   | **头** | 否 | 需要或想要包含在传出请求中的任何标头值，如内容类型 |
   | **查询** | 否 | 需要或想要包含在请求中的任何查询参数，例如特定操作的参数或要运行的操作的 API 版本 |
   | **身份验证** | 是 | 用于验证对目标资源或实体的访问权限的身份验证类型 |
   ||||

   作为特定示例，假设您希望在 Azure 存储帐户中的 Blob 上运行[快照 Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)，而以前在 Azure 存储帐户中为标识设置访问权限。 但是[，Azure Blob 存储连接器](https://docs.microsoft.com/connectors/azureblob/)当前不提供此操作。 相反，您可以使用[HTTP 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)或其他 Blob 服务 REST [API 操作](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)来运行此操作。

   > [!IMPORTANT]
   > 要使用 HTTP 请求和托管标识访问防火墙后面的 Azure 存储帐户，请确保也设置存储帐户[，但允许受信任的 Microsoft 服务进行访问的除外](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)。

   要运行[快照 Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)，HTTP 操作指定以下属性：

   | properties | 必选 | 示例值 | 描述 |
   |----------|----------|---------------|-------------|
   | **方法** | 是 | `PUT`| 快照 Blob 操作使用的 HTTP 方法 |
   | **URI** | 是 | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Azure 全局（公共）环境中 Azure Blob 存储文件的资源 ID，该环境使用此语法 |
   | **头** | 是，对于 Azure 存储 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure `x-ms-blob-type` `x-ms-version`存储操作所需的 和 标头值。 <p><p>**重要提示**：在 Azure 存储的传出 HTTP 触发器和操作请求`x-ms-version`中，标头需要要运行的操作的属性和 API 版本。 <p>有关详细信息，请参阅以下主题： <p><p>- [请求标头 - 快照 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Azure 存储服务的版本控制](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **查询** | 是，对于此操作 | `comp` = `snapshot` | 快照 Blob 操作的查询参数名称和值。 |
   |||||

   下面是显示所有这些属性值的示例 HTTP 操作：

   ![添加 HTTP 操作以访问 Azure 资源](./media/create-managed-service-identity/http-action-example.png)

1. 现在，将**身份验证**属性添加到 HTTP 操作中。 从 **"添加新参数**"列表中，选择 **"身份验证**"。

   ![将"身份验证"属性添加到 HTTP 操作](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > 并非所有触发器和操作都支持添加身份验证类型。 有关详细信息，请参阅[针对出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 从**身份验证类型**列表中，选择**托管标识**。

   ![对于"身份验证"，请选择"托管标识"](./media/create-managed-service-identity/select-managed-identity.png)

1. 从托管标识列表中，根据方案从可用选项中进行选择。

   * 如果设置系统分配的标识，请选择 **"系统分配托管标识**"（如果尚未选择）。

     ![选择"系统分配的托管标识"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * 如果设置了用户分配的标识，请选择该标识（如果尚未选择）。

     ![选择用户分配的标识](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   此示例继续与**系统分配的托管标识**。

1. 在某些触发器和操作上，还会显示 **"访问群体"** 属性，以便您设置目标资源 ID。 将 **"访问群体"** 属性设置为[目标资源或服务的资源 ID。](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) 否则，默认情况下，**访问群体**属性使用`https://management.azure.com/`资源 ID，这是 Azure 资源管理器的资源 ID。

   > [!IMPORTANT]
   > 确保目标资源 ID 与 Azure 活动目录 （AD） 期望的值*完全匹配*，包括任何必需的尾随斜杠。 例如，所有 Azure Blob 存储帐户的资源 ID 都需要尾随斜杠。 但是，特定存储帐户的资源 ID 不需要尾部斜杠。 检查[支持 Azure AD 的 Azure 服务的资源标识](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

   本示例将 **"访问群体"** 属性集，`https://storage.azure.com/`以便用于身份验证的访问令牌对所有存储帐户都有效。 但是，您还可以为特定存储帐户指定根服务`https://fabrikamstorageaccount.blob.core.windows.net`URL。

   ![将"访问"属性设置为目标资源 ID](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   有关使用 Azure 存储授权使用 Azure AD 进行访问的详细信息，请参阅以下主题：

   * [使用 Azure 活动目录授权对 Azure Blob 和队列的访问](../storage/common/storage-auth-aad.md)
   * [使用 Azure 活动目录授权访问 Azure 存储](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 继续以所需的方式构建逻辑应用。

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>禁用托管标识

要停止对逻辑应用使用托管标识，您有以下选项：

* [Azure 门户](#azure-portal-disable)
* [Azure 资源管理器模板](#template-disable)
* Azure PowerShell
  * [删除角色分配](../role-based-access-control/role-assignments-powershell.md)
  * [删除用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [删除角色分配](../role-based-access-control/role-assignments-cli.md)
  * [删除用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [删除角色分配](../role-based-access-control/role-assignments-rest.md)
  * [删除用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

如果删除逻辑应用，Azure 将自动从 Azure AD 中删除托管标识。

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>禁用 Azure 门户中的托管标识

在 Azure 门户中，首先删除标识对[目标资源](#disable-identity-target-resource)的访问。 接下来，关闭系统分配的标识，或从[逻辑应用](#disable-identity-logic-app)中删除用户分配的标识。

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>从资源中删除身份访问

1. 在[Azure 门户](https://portal.azure.com)中，转到要删除托管标识访问权限的目标 Azure 资源。

1. 从目标资源的菜单中，选择**访问控制 （IAM）。** 在工具栏下，选择**角色分配**。

1. 在角色列表中，选择要删除的托管标识。 在工具栏上，选择 **"删除**"。

   > [!TIP]
   > 如果禁用 **"删除**"选项，则很可能没有权限。 有关允许您管理资源角色的权限的详细信息，请参阅[Azure 活动目录中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

托管标识现已被删除，不再有权访问目标资源。

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>禁用逻辑应用上的托管标识

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单中，在 **"设置"** 下，选择 **"标识**"，然后按照标识的步骤操作：

   * 选择 > **在** > **保存**时**分配的系统**。 当 Azure 提示您确认时，请选择"**是**"。

     ![禁用系统分配的标识](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * 选择**用户分配**和托管标识，然后选择 **"删除**"。 当 Azure 提示您确认时，请选择"**是**"。

     ![删除用户分配的标识](./media/create-managed-service-identity/remove-user-assigned-identity.png)

托管标识现在在逻辑应用中被禁用。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>禁用 Azure 资源管理器模板中的托管标识

如果使用 Azure 资源管理器模板创建了逻辑应用的托管标识，则将`identity`对象的`type`子属性设置为`None`。 对于系统托管标识，此操作还会从 Azure AD 中删除主体 ID。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>后续步骤

* [在 Azure 逻辑应用中保护访问和数据](../logic-apps/logic-apps-securing-a-logic-app.md)