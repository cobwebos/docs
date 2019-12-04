---
title: 使用托管标识进行身份验证
description: 通过使用托管标识，无需登录凭据或机密即可访问其他 Azure Active Directory 租户中的资源
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 49c925cfe61084d8fedfdf953d469db4bd2c10b1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792672"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>使用 Azure 逻辑应用中的托管标识对 Azure 资源的访问权限进行身份验证

若要访问其他 Azure Active Directory （Azure AD）租户中的资源并在未登录的情况下验证身份，你的逻辑应用可以使用系统分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)（以前称为托管服务标识或 MSI），而不是凭据或机密。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。 本文介绍如何在逻辑应用中设置和使用系统分配的托管标识。 目前，托管标识仅适用[于特定内置触发器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-to-outbound-calls)，不能用于托管连接器或连接。

有关详细信息，请参阅以下主题：

* [支持托管标识的触发器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [支持通过托管标识进行 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [出站调用上支持的身份验证类型](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [逻辑应用的托管标识限制](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>必备组件

* Azure 订阅，如果没有订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 要访问的托管标识和目标 Azure 资源都需要使用同一 Azure 订阅。

* [Azure AD 管理员权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)，可以将角色分配给与目标资源相同 Azure AD 租户中的托管标识。 若要为托管标识提供对 Azure 资源的访问权限，需要在目标资源上添加该标识的角色。

* 要访问的目标 Azure 资源

* 使用[支持托管标识的触发器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)的逻辑应用

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>启用系统分配的标识

不同于用户分配的标识，无需手动创建系统分配的标识。 若要设置逻辑应用的系统分配的标识，可以使用以下选项：

* [Azure 门户](#azure-portal-system-logic-app)
* [Azure 资源管理器模板](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>在 Azure 门户中启用系统分配的标识

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单上的 "**设置**" 下，选择 "**标识** > **系统分配**"。 **在 "** **状态**" 下，选择 > **保存** > **是 "** 。

   ![启用系统分配的标识](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   逻辑应用现在可以使用系统分配的标识，该标识注册到 Azure Active Directory，由对象 ID 表示。

   ![系统分配的标识的对象 ID](./media/create-managed-service-identity/object-id.png)

   | properties | Value | 描述 |
   |----------|-------|-------------|
   | **对象 ID** | <*identity-resource-ID*> | 全局唯一标识符（GUID），表示 Azure AD 租户中逻辑应用的系统分配的标识 |
   ||||

1. 接下来，请执行[为标识授予对资源的访问权限的步骤](#access-other-resources)。

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中启用系统分配的标识

若要自动创建和部署 Azure 资源（如逻辑应用），可以使用[azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。 若要在模板中为逻辑应用启用系统分配的托管标识，请在模板中将 `identity` 对象和 `type` 子属性添加到逻辑应用的资源定义中，例如：

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

当 Azure 创建逻辑应用资源定义时，`identity` 对象将获取以下附加属性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 属性（JSON） | Value | 描述 |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | 托管标识的服务主体对象的全局唯一标识符（GUID），表示 Azure AD 租户中的逻辑应用。 此 GUID 有时显示为 "对象 ID" 或 `objectID`。 |
| `tenantId` | <*Azure-AD-tenant-ID*> | 全局唯一标识符（GUID），表示逻辑应用现在是其成员的 Azure AD 租户。 在 Azure AD 租户内，服务主体与逻辑应用实例具有相同名称。 |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>授予对资源的标识访问权限

为逻辑应用设置托管标识后，可以为[该标识提供对其他 Azure 资源的访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。 然后，可以使用该标识进行身份验证。

1. 在[Azure 门户](https://portal.azure.com)中，请访问希望托管标识具有访问权限的 Azure 资源。

1. 在资源的菜单中，选择 "**访问控制（IAM）** " > **角色分配**，你可以在其中查看该资源的当前角色分配。 在工具栏上，选择 "**添加** > **添加角色分配**"。

   ![选择 "添加" > "添加角色分配"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > 如果 "**添加角色分配**" 选项处于禁用状态，则很可能没有权限。 有关可用于管理资源角色的权限的详细信息，请参阅[Azure Active Directory 中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

1. 在 "**添加角色分配**" 下，选择为标识提供对目标资源所需的访问权限的**角色**。

   在本主题的示例中，标识需要[可以访问 Azure 存储容器中的 blob 的角色](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)：

   ![选择 "存储 Blob 数据参与者" 角色](./media/create-managed-service-identity/assign-role.png)

1. 在“分配访问权限至”框中，选择“Azure AD 用户、组或服务主体”。

   ![为系统分配的标识选择访问权限](./media/create-managed-service-identity/assign-access-system.png)

1. 在**选择**框中，查找并选择逻辑应用。

   ![为系统分配的标识选择逻辑应用](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. 完成后，选择“保存”。

   目标资源的 "角色分配" 列表现在显示所选的托管标识和角色。

   ![向目标资源添加了托管标识和角色](./media/create-managed-service-identity/added-roles-for-identities.png)

1. 现在，请遵循这些步骤，通过支持托管标识的触发器或操作中[的标识对访问进行身份验证](#authenticate-access-with-identity)。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>使用托管标识对访问进行身份验证

[为逻辑应用启用托管标识](#azure-portal-system-logic-app)并[向该标识授予对目标资源或实体的访问权限](#access-other-resources)后，可以在[支持托管标识的触发器和操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)中使用该标识。

> [!IMPORTANT]
> 如果你的 Azure 函数要使用系统分配的标识，请首先[为 azure 函数启用身份验证](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

以下步骤演示了如何通过 Azure 门户将托管标识与触发器或操作一起使用。 若要在触发器或操作的基础 JSON 定义中指定托管标识，请参阅[托管标识身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 如果尚未这样做，请添加[支持托管标识的触发器或操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

   例如，HTTP 触发器或操作可使用为逻辑应用启用的系统分配的标识。 通常，HTTP 触发器或操作使用这些属性来指定要访问的资源或实体：

   | properties | 需要 | 描述 |
   |----------|----------|-------------|
   | **方法** | 是 | 要运行的操作所使用的 HTTP 方法 |
   | **URI** | 是 | 用于访问目标 Azure 资源或实体的终结点 URL。 URI 语法通常包含 Azure 资源或服务的[资源 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) 。 |
   | **标头** | No | 需要包含在传出请求中的任何标头值，例如内容类型。 |
   | **查询** | No | 需要包含在请求中的任何查询参数，如特定操作的参数或要运行的操作的 API 版本 |
   | **身份验证** | 是 | 用于对目标资源或实体访问进行身份验证的身份验证类型 |
   ||||

   作为特定示例，假设要在之前为标识设置访问权限的 Azure 存储帐户中的 Blob 上运行[快照 Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)。 但是， [Azure Blob 存储连接器](https://docs.microsoft.com/connectors/azureblob/)当前不提供此操作。 您可以改为使用[HTTP 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)或 REST API 操作的另一个[Blob 服务](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)运行此操作。

   > [!IMPORTANT]
   > 若要通过使用 HTTP 请求和托管标识访问防火墙后面的 Azure 存储帐户，请确保你还设置了存储帐户，但该[例外允许受信任的 Microsoft 服务进行访问](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)。

   若要运行[快照 Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)，HTTP 操作将指定以下属性：

   | properties | 需要 | 示例值 | 描述 |
   |----------|----------|---------------|-------------|
   | **方法** | 是 | `PUT`| 快照 Blob 操作使用的 HTTP 方法 |
   | **URI** | 是 | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Azure 全局（公共）环境中使用此语法的 Azure Blob 存储文件的资源 ID |
   | **标头** | 是，适用于 Azure 存储 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure 存储操作所需的 `x-ms-blob-type` 和 `x-ms-version` 标头值。 <p><p>**重要提示**：在 Azure 存储的传出 HTTP 触发器和操作请求中，标头需要 `x-ms-version` 属性以及要运行的操作的 API 版本。 <p>有关详细信息，请参阅以下主题： <p><p>- [请求标头-快照 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>[Azure 存储服务的 - 版本管理](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **查询** | 是，对于此操作 | `comp` = `snapshot` | 快照 Blob 操作的查询参数名称和值。 |
   | **身份验证** | 是 | `Managed Identity` | 用于对 Azure blob 的访问进行身份验证的身份验证类型 |
   |||||

   下面是示例 HTTP 操作，它显示所有这些属性值：

   ![添加 HTTP 操作以访问 Azure 资源](./media/create-managed-service-identity/http-action-example.png)

   有关所有可用 Azure REST API 操作的详细信息，请参阅[Azure REST API 参考](https://docs.microsoft.com/rest/api/azure/)。

1. 从“身份验证”列表选择“托管标识”。 如果 " [**身份验证**" 属性受支持](logic-apps-securing-a-logic-app.md#add-authentication-outbound)但处于隐藏状态，请打开 "**添加新参数**" 列表，然后选择 "**身份验证**"。

   > [!NOTE]
   > 并非所有触发器和操作都允许您选择身份验证类型。 有关详细信息，请参阅[将身份验证添加到出站调用](logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

   ![在 "身份验证" 属性中，选择 "托管标识"](./media/create-managed-service-identity/select-managed-identity.png)

1. 选择**托管标识**后，将显示某些触发器和操作的**受众**属性。 如果支持但隐藏**访问群体**属性，请打开 "**添加新参数**" 列表，然后选择 "**受众**"。

1. 请确保将**受众**值设置为目标资源或服务的资源 ID。 否则，默认情况下，**受众**属性使用 `https://management.azure.com/` 资源 id，该 Id 是 Azure 资源管理器的资源 id。

   > [!IMPORTANT]
   > 请确保目标资源 ID 与 Azure Active Directory （AD）预期的值（包括任何必需的尾部斜杠）*完全匹配*。 例如，所有 Azure Blob 存储帐户的资源 ID 都需要尾部斜杠。 不过，特定存储帐户的资源 ID 不需要尾部斜杠。 检查[支持 Azure AD 的 Azure 服务的资源 id](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

   此示例将**受众**属性设置为 `https://storage.azure.com/`，以便用于身份验证的访问令牌对所有存储帐户都有效。 但是，你还可以为特定的存储帐户指定根服务 URL `https://fabrikamstorageaccount.blob.core.windows.net`。

   ![在 "访问群体" 属性中指定目标资源 ID](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   有关使用 Azure 存储 Azure AD 授权访问权限的详细信息，请参阅以下主题：

   * [使用 Azure Active Directory 授予对 Azure blob 和队列的访问权限](../storage/common/storage-auth-aad.md)
   * [使用 Azure Active Directory 授予对 Azure 存储的访问权限](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>删除系统分配的标识

若要停止对逻辑应用使用系统分配的标识，你可以使用以下选项：

* [Azure 门户](#azure-portal-disable)
* [Azure 资源管理器模板](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

如果删除逻辑应用，Azure 将从 Azure AD 中自动删除托管标识。

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>在 Azure 门户中删除系统分配的标识

在 Azure 门户中，[从逻辑应用](#disable-identity-logic-app)中删除系统分配的标识，并[从目标资源](#disable-identity-target-resource)中删除该标识的访问权限。

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>从逻辑应用中删除系统分配的标识

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单上的 "**设置**" 下，选择 "**标识** > **系统分配**"。 在 "**状态**" 下，选择 "**关闭**" > **保存** > **是 "** 。

   ![停止使用系统分配的标识](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>从资源中删除标识访问权限

1. 在[Azure 门户](https://portal.azure.com)中，请访问要在其中删除托管标识访问权限的目标 Azure 资源。

1. 从目标资源的菜单中，选择 "**访问控制（IAM）** "。 在工具栏中，选择 "**角色分配**"。

1. 在 "角色" 列表中，选择要删除的托管标识。 在工具栏上，选择 "**删除**"。

   > [!TIP]
   > 如果禁用 "**删除**" 选项，则很可能没有权限。 有关可用于管理资源角色的权限的详细信息，请参阅[Azure Active Directory 中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

托管标识现在已删除，不再具有对目标资源的访问权限。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中禁用托管标识

如果使用 Azure 资源管理器模板启用了逻辑应用的系统管理的标识，请将 `identity` 对象的 `type` 子属性设置为 "`None`"。 此操作还将从 Azure AD 中删除系统管理的标识的主体 ID。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>后续步骤

* [Azure 逻辑应用中的安全访问和数据](../logic-apps/logic-apps-securing-a-logic-app.md)
