---
title: "使用 Linux VM 用户分配的 MSI 访问 Azure 资源管理器"
description: "本教程介绍使用 Linux VM 上用户分配的托管服务标识 (MSI) 访问 Azure 资源管理器的过程。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7013cb48738d4dccb328728fe1e47b3f43bff4f6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>使用 Linux VM 上用户分配的托管服务标识 (MSI) 访问 Azure 资源管理器

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教程介绍如何创建用户分配的托管服务标识 (MSI)、将其分配给 Linux 虚拟机 (VM)，再使用此标识访问 Azure 资源管理器 API。 

托管服务标识由 Azure 自动管理。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中嵌入凭据了。

学习如何：

> [!div class="checklist"]
> * 创建用户分配的 MSI
> * 将 MSI 分配到 Linux VM 
> * 授予 MSI 对 Azure 资源管理器中资源组的访问权限 
> * 使用 MSI 获取访问令牌，并使用它调用 Azure 资源管理器 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

若要运行本教程中的 CLI 脚本示例，你有两种选择：

- 从 Azure 门户中或者通过每个代码块右上角的“试用”按钮使用 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)。
- 如果喜欢使用本地 CLI 控制台，请[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.23 或更高版本）。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Linux 虚拟机

在本教程中，首先新建一个 Linux VM。 也可以使用现有 VM。

1. 单击 Azure 门户左上角的“创建资源”。
2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
3. 输入虚拟机信息。 对于“身份验证类型”，选择“SSH 公钥”或“密码”。 使用创建的凭据可以登录 VM。

    ![创建 Linux VM](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 在“订阅”下拉列表中，选择虚拟机对应的订阅。
5. 若要在新资源组中创建虚拟机，请选择“资源组”中的“新建”。 完成后，单击“确定”。
6. 选择 VM 大小。 若要查看更多大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在设置边栏选项卡中保留默认值，然后单击“确定”。

## <a name="create-a-user-assigned-msi"></a>创建用户分配的 MSI

1. 如果使用的是 CLI 控制台（而不是 Azure Cloud Shell 会话），则先登录到 Azure。 使用与要在其下创建新 MSI 的 Azure 订阅关联的帐户：

    ```azurecli
    az login
    ```

2. 使用 [az identity create](/cli/azure/identity#az_identity_create) 创建用户分配的 MSI。 `-g` 参数指定要创建 MSI 的资源组，`-n` 参数指定其名称。 请务必将 `<RESOURCE GROUP>` 和 `<MSI NAME>` 参数值替换为自己的值：

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    响应包含所创建的用户分配 MSI 的详细信息，与以下示例类似。 记下 MSI 的 `id` 值，后续步骤会用到它：

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>将用户分配的 MSI 分配给 Linux VM

与系统分配的 MSI 不同，用户分配的 MSI 可由多个 Azure 资源上的客户端使用。 在本教程中，将其分配给单个 VM。 也可以将其分配给多个 VM。

使用 [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity) 将用户分配的 MSI 分配给 Linux VM。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 将上一步返回的 `id` 属性用于 `--identities` 参数值：

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>向用户分配的 MSI 授予对 Azure 资源管理器中资源组的访问权限 

使用 MSI，代码可以获取访问令牌，对支持 Azure AD 身份验证的资源 API 进行身份验证。 在本教程中，代码访问 Azure 资源管理器 API。 

需先向 MSI 的标识授予对 Azure 资源管理器中资源的访问权限，代码才能访问 API。 在此情况下，即为包含 VM 的资源组。 根据环境的需要更新 `<SUBSCRIPTION ID>` 和 `<RESOURCE GROUP>` 的值。 此外，将 `<MSI PRINCIPALID>` 替换为在[创建用户分配的 MSI](#create-a-user-assigned-msi) 中由 `az identity create` 命令返回的 `principalId` 属性：

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
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

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 客户端。 如果需要有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](~/articles/virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在 Azure 门户中，导航到“虚拟机”，转到 Linux 虚拟机，然后在“概述”页中单击顶部的“连接”。 复制用于连接到 VM 的字符串。
2. 使用所选的 SSH 客户端连接到 VM。  
3. 在终端窗口中，使用 CURL 向本地 MSI 终结点发出请求，以获取访问 Azure 资源管理器所需的访问令牌。  

   以下示例展示了用于获取访问令牌的 CURL 请求。 请务必将 `<CLIENT ID>` 替换为在[创建用户分配的 MSI](#create-a-user-assigned-msi) 中由 `az identity create` 命令返回的 `clientId` 属性： 
    
   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<CLIENT ID>"   
   ```
    
    > [!NOTE]
    > `resource` 参数值必须与 Azure AD 预期值完全一致。 如果使用资源管理器资源 ID，必须在 URI 的结尾添加斜线。 
    
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

4. 现在使用访问令牌访问 Azure 资源管理器，并读取之前授予用户分配的 MSI 访问权限以使其有权访问的资源组的属性。 确保将 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>` 的值替换为先前指定的值，将 `<ACCESS TOKEN>` 替换为上一步返回的令牌。

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

- 有关 MSI 的概述，请参阅[托管服务标识概述](msi-overview.md)。

使用以下评论部分提供反馈，帮助我们改进内容。
