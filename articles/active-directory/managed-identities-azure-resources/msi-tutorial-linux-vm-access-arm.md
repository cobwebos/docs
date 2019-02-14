---
title: 使用 Linux VM 用户分配托管标识访问 Azure 资源管理器
description: 本教程介绍使用 Linux VM 上用户分配托管标识访问 Azure 资源管理器的过程。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: priyamo
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11ea866e0ebf78b85f6802324478978654d97ebf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167892"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>教程：使用 Linux VM 上的用户分配托管标识访问 Azure 资源管理器

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教程介绍如何创建用户分配托管标识、将其分配给 Linux 虚拟机 (VM)，再使用此标识访问 Azure 资源管理器 API。 Azure 资源的托管标识由 Azure 自动管理。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中嵌入凭据了。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建用户分配的托管标识
> * 将用户分配托管标识分配给 Linux VM 
> * 向用户分配托管标识授予对 Azure 资源管理器中资源组的访问权限 
> * 使用用户分配托管标识获取访问令牌，并用它调用 Azure 资源管理器 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [登录到 Azure 门户](https://portal.azure.com)

- [创建 Linux 虚拟机](/azure/virtual-machines/linux/quick-create-portal)

- 如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

1. 如果使用的是 CLI 控制台（而不是 Azure Cloud Shell 会话），则先登录到 Azure。 使用与要在其下新建用户分配托管标识的 Azure 订阅关联的帐户：

    ```azurecli
    az login
    ```

2. 使用 [az identity create](/cli/azure/identity#az-identity-create) 创建用户分配托管标识。 `-g` 参数指定要创建用户分配托管标识的资源组，`-n` 参数指定其名称。 请务必将 `<RESOURCE GROUP>` 和 `<UAMI NAME>` 参数值替换为自己的值：
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

响应包含所创建的用户分配托管标识的详细信息，与以下示例类似。 记下用户分配托管标识的 `id` 值，因为下一步会用到它：

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-a-user-assigned-managed-identity-to-your-linux-vm"></a>将用户分配托管标识分配给 Linux VM

用户分配托管标识可以由多个 Azure 资源上的客户端使用。 使用以下命令将用户分配托管标识分配给单个 VM。 将上一步返回的 `Id` 属性用于 `-IdentityID` 参数。

使用 [az vm identity assign](/cli/azure/vm) 将用户分配托管标识分配给 Linux VM。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 将上一步返回的 `id` 属性用于 `--identities` 参数值。

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-your-user-assigned-managed-identity-access-to-a-resource-group-in-azure-resource-manager"></a>向用户分配托管标识授予对 Azure 资源管理器中资源组的访问权限 

Azure 资源的托管标识提供了相关标识，你的代码可使用这些标识来请求访问令牌，以向支持 Azure AD 身份验证的资源 API 进行身份验证。 在本教程中，你的代码将访问 Azure 资源管理器 API。  

需先向标识授予对 Azure 资源管理器中资源的访问权限，代码才能访问 API。 在此情况下，即为包含 VM 的资源组。 根据环境适当地更新 `<SUBSCRIPTION ID>` 和 `<RESOURCE GROUP>` 的值。 另外，请将 `<UAMI PRINCIPALID>` 替换为在[创建用户分配托管标识](#create-a-user-assigned-managed-identity)中由 `az identity create` 命令返回的 `principalId` 属性：

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

响应包含所创建的角色分配的详细信息，与以下示例类似：

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>使用 VM 标识获取访问令牌，并使用它调用资源管理器 

在本教程的剩余部分中，我们从先前创建的 VM 入手。

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 客户端。 

1. 登录 Azure [门户](https://portal.azure.com)。
2. 在门户中，导航到“虚拟机”并转到 Linux 虚拟机，然后在“概述”中，单击“连接”。 复制用于连接到 VM 的字符串。
3. 使用所选的 SSH 客户端连接到 VM。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 客户端。 如果需要有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](~/articles/virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)。
4. 在终端窗口中，使用 CURL 向 Azure 实例元数据服务 (IMDS) 标识终结点发出请求，以获取访问 Azure 资源管理器所需的访问令牌。  

   以下示例展示了用于获取访问令牌的 CURL 请求。 请务必将 `<CLIENT ID>` 替换为在[创建用户分配托管标识](#create-a-user-assigned-managed-identity)中由 `az identity create` 命令返回的 `clientId` 属性： 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > `resource` 参数值必须与 Azure AD 预期值完全一致。 如果使用资源管理器资源 ID，必须在 URI 的结尾添加斜线。 
    
    响应包括访问 Azure 资源管理器所需的访问令牌。 
    
    响应示例：  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. 使用访问令牌访问 Azure 资源管理器，并读取之前授予用户分配托管标识访问权限以使其有权访问的资源组的属性。 确保将 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>` 的值替换为先前指定的值，将 `<ACCESS TOKEN>` 替换为上一步返回的令牌。

    > [!NOTE]
    > URL 区分大小写。因此，请确保使用的大小写与之前在命名资源组时使用的大小写完全相同，以及 `resourceGroups` 使用的是大写“G”。  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    响应包含特定资源组信息，类似于下面的示例： 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建用户分配托管标识并将其附加到 Linux 虚拟机，以访问 Azure 资源管理器 API。  若要详细了解 Azure 资源管理器，请参阅：

> [!div class="nextstepaction"]
>[Azure 资源管理器](/azure/azure-resource-manager/resource-group-overview)

