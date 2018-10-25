---
title: 使用托管标识进行身份验证 - Azure 逻辑应用 | Microsoft Docs
description: 若要在不登录的情况下进行身份验证，可以创建一个托管标识（以前称为托管服务标识或 MSI），以便你的逻辑应用可以在不提供凭据或机密的情况下访问其他 Azure Active Directory (Azure AD) 租户中的资源。
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/05/2018
ms.openlocfilehash: 2964869933dd096b96e892cf08b8d4b66a8f210c
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48817052"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>使用 Azure 逻辑应用中的托管标识进行身份验证并访问资源

若要在不登录的情况下访问其他 Azure Active Directory (Azure AD) 租户中的资源并对你的标识进行身份验证，逻辑应用可以使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)（以前称为托管服务标识或 MSI），而非使用凭据或机密。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。 本文展示了如何为逻辑应用创建和使用系统分配的托管标识。 有关托管标识的详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> 当前，每个 Azure 订阅中最多可以有 10 个具有系统分配的托管标识的逻辑应用工作流。

## <a name="prerequisites"></a>先决条件

* Azure 订阅，如果没有订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

* 要在其中使用系统分配的托管标识的逻辑应用。 如果没有逻辑应用，请参阅[创建第一个逻辑应用工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>创建托管标识

可以通过 Azure 门户、Azure 资源管理器模板或 Azure PowerShell 为逻辑应用创建或启用系统分配的托管标识。 

### <a name="azure-portal"></a>Azure 门户

若要通过 Azure 门户为逻辑应用创建系统分配的托管标识，请打开逻辑应用工作流设置中的“通过 Azure Active Directory 注册”设置。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 执行以下步骤: 

   1. 在逻辑应用菜单的“设置”下，选择“工作流设置”。 

   1. 在“托管服务标识”> 
   “通过 Azure Active Directory 注册”下，选择“打开”************。

   1. 完成后，选择工具栏上的“保存”。

      ![打开托管标识设置](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      你的逻辑应用现在已在 Azure Active Directory 中注册了系统分配的托管标识，该标识具有以下属性和值：

      ![主体 ID 和租户 ID 的 GUID](./media/create-managed-service-identity/principal-tenant-id.png)

      | 属性 | 值 | 说明 | 
      |----------|-------|-------------| 
      | **主体 ID** | <*principal-ID*> | 全局唯一标识符 (GUID)，表示 Azure AD 租户中的逻辑应用 | 
      | **租户 ID** | <*Azure-AD-tenant-ID*> | 全局唯一标识符 (GUID)，表示逻辑应用为其中成员的 Azure AD 租户。 在 Azure AD 租户内，服务主体与逻辑应用实例具有相同名称。 | 
      ||| 

### <a name="deployment-template"></a>部署模板

希望自动创建和部署 Azure 资源（例如逻辑应用）时，可以使用 [Azure 资源管理器模板](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)。 若要通过模板为逻辑应用创建系统分配的托管标识，请将 `"identity"` 元素和 `"type"` 属性添加到部署模板中的逻辑应用工作流定义中。 

```json
"identity": {
   "type": "SystemAssigned"
}
```

例如：

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

当 Azure 创建逻辑应用时，该逻辑应用的工作流定义包括这些附加属性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 属性 | 值 | Description | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | 全局唯一标识符 (GUID)，表示 Azure AD 租户中的逻辑应用 | 
| **tenantId** | <*Azure-AD-tenant-ID*> | 全局唯一标识符 (GUID)，表示逻辑应用为其中成员的 Azure AD 租户。 在 Azure AD 租户内，服务主体与逻辑应用实例具有相同名称。 | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>通过托管标识访问资源

为逻辑应用创建系统分配的托管标识后，可以[授予该标识访问其他 Azure 资源的权限](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。 然后可以使用该标识进行身份验证，就像任何其他[服务主体](../active-directory/develop/app-objects-and-service-principals.md)一样。 

> [!NOTE]
> 系统分配的托管标识和你希望在其中分配访问权限的资源必须具有相同的 Azure 订阅。

### <a name="assign-access-to-managed-identity"></a>为托管标识分配访问权限

若要将对其他 Azure 资源的访问权限授予逻辑应用的系统分配托管标识，请执行以下步骤：

1. 在 Azure 门户中，转到你要在其中为托管标识分配访问权限的 Azure 资源。 

1. 从该资源的菜单中，选择“访问控制(标识和访问管理)”，然后选择“添加”。 

   ![添加权限](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. 在“添加权限”下，选择该标识所需的**角色**。 

1. 在“分配其访问权限”属性中，选择“Azure AD 用户、组或应用程序”（如果尚未选择）。

1. 在“选择”框中，从逻辑应用名称中的第一个字符开始，输入你的逻辑应用的名称。 当你的逻辑应用出现时，选择该逻辑应用。

   ![选择具有托管标识的逻辑应用](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. 完成后，选择“保存”。

### <a name="authenticate-with-managed-identity-in-logic-app"></a>使用逻辑应用中的托管标识进行身份验证

为逻辑应用设置系统分配的托管标识并为该标识分配对资源的所需权限后，现在可以使用该标识进行身份验证。 例如，可以使用一个 HTTP 操作，以便逻辑应用可以向该资源发送 HTTP 请求或调用。 

1. 在逻辑应用中，添加“HTTP”操作。 

1. 为该操作提供必要的详细信息，例如要调用的资源的请求“方法”和“URI”位置。

1. 在 HTTP 操作中，选择“显示高级选项”。 

1. 从“身份验证”列表中选择“托管服务标识”，随后将显示用于设置的“受众”属性：

   ![选择“托管服务标识”](./media/create-managed-service-identity/select-managed-service-identity.png)

1. 继续按照所需方式生成逻辑应用。

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>删除托管标识

若要禁用逻辑应用上的系统分配的托管标识，可以按照与通过 Azure 门户、Azure 资源管理器部署模板或 Azure PowerShell 创建标识类似的步骤进行操作。 

删除逻辑应用时，Azure 会自动从 Azure AD 中删除系统分配的逻辑应用标识。

### <a name="azure-portal"></a>Azure 门户

1. 在逻辑应用设计器中，打开逻辑应用。

1. 执行以下步骤: 

   1. 在逻辑应用菜单的“设置”下，选择“工作流设置”。 
   
   1. 在“托管服务标识”下，为“通过 Azure Active Directory 注册”属性选择“关闭”。

   1. 完成后，选择工具栏上的“保存”。

      ![关闭托管标识设置](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>部署模板

如果使用 Azure 资源管理器部署模板创建了逻辑应用的系统分配的托管标识，请将 `"identity"` 元素的 `"type"` 属性设置为 `"None"`。 此操作还会从 Azure AD 中删除主体 ID。 

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

