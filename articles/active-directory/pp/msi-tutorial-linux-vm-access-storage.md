---
title: 使用 Linux VM 上用户分配的 MSI 访问 Azure 存储
description: 本教程介绍使用 Linux VM 上用户分配的托管服务标识 (MSI) 访问 Azure 存储的过程。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4a1a2d0c40012649f6cd89193fd3f704f325e38a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611038"
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>使用 Linux VM 上用户分配的托管服务标识 (MSI) 访问 Azure 存储

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教程介绍如何从 Linux 虚拟机创建并使用用户分配的托管服务标识 (MSI)，然后用它来访问 Azure 存储。 学习如何：

> [!div class="checklist"]
> * 创建用户分配的托管服务标识 (MSI)
> * 将用户分配的 MSI 分配给 Linux 虚拟机
> * 向 MSI 授予对 Azure 存储实例的访问权限
> * 使用用户分配的 MSI 标识获取访问令牌，并用它访问 Azure 存储

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

若要运行本教程中的 CLI 脚本示例，你有两种选择：

- 从 Azure 门户中或者通过每个代码块右上角的“试用”按钮使用 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)。
- 如果喜欢使用本地 CLI 控制台，请[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.23 或更高版本）。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Linux 虚拟机

首先，创建新的 Linux VM。 如果愿意，也可以在现有 VM 上启用 MSI。

1. 单击 Azure 门户左上角的“+/创建新服务”按钮。
2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
3. 输入虚拟机信息。 对于“身份验证类型”，选择“SSH 公钥”或“密码”。 使用创建的凭据可以登录 VM。

    ![Alt 图像文本](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
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

使用 [az vm assign-identity](/cli/azure/vm#az-vm-identity-assign) 将用户分配的 MSI 分配给 Linux VM。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 将上一步返回的 `id` 属性用于 `--identities` 参数值：

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>创建存储帐户 

如果还没有存储帐户，可以立即创建一个。 如果愿意，也可以跳过此步骤，使用现有存储帐户。 

1. 单击 Azure 门户左上角的“+/创建新服务”按钮。
2. 依次单击“存储”、“存储帐户”，随即显示新的“创建存储帐户”面板。
3. 输入存储帐户的**名称**，稍后将使用该名称。  
4. “部署模型”和“帐户类型”应分别设置为“资源管理器”和“通用”。 
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。
6. 单击“创建”。

    ![新建存储帐户](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在存储帐户中创建 Blob 容器

由于文件需要 blob 存储，你需要创建用于存储文件的 blob 容器。 然后将文件上传并下载到新存储帐户中的 blob 容器。

1. 导航回新创建的存储帐户。
2. 在“Blob 服务”下单击左侧的“容器”链接。
3. 单击页面顶部的“+ 容器”，将滑出“新建容器”面板。
4. 为容器指定名称，选择访问级别，单击“确定”。 本教程的后面部分也将使用所指定的名称。 

    ![创建存储容器](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 通过以下方式将文件上传到新创建的容器：单击容器名称，单击“上传”，然后选择一个文件，单击“上传”。

    ![上传文本文件](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>向用户分配的 MSI 授予对 Azure 存储容器的访问权限

使用 MSI，代码可以获取访问令牌，对支持 Azure AD 身份验证的资源进行身份验证。 在本教程中，将使用 Azure 存储。

首先，向 MSI 标识授予对 Azure 存储容器的访问权限。 在此例中，使用前面创建的容器。 根据环境的需要更新 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<STORAGE ACCOUNT NAME>` 和 `<CONTAINER NAME>` 的值。 此外，将 `<MSI PRINCIPALID>` 替换为在[创建用户分配的 MSI](#create-a-user-assigned-msi) 中由 `az identity create` 命令返回的 `principalId` 属性：

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

响应包括所创建的角色分配的详细信息：

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>使用用户分配的 MSI 标识获取访问令牌，并用它调用 Azure 存储

在本教程的剩余部分，需从先前创建的 VM 入手。

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 客户端。 如果需要有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](~/articles/virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在 Azure 门户中，导航到“虚拟机”，转到 Linux 虚拟机，然后在“概述”页中单击顶部的“连接”。 复制用于连接到 VM 的字符串。
2. 使用所选的 SSH 客户端连接到 VM。 
3. 在终端窗口中，使用 CURL 向本地 MSI 终结点发出请求，以获取访问 Azure 存储所需的访问令牌。

   以下示例展示了用于获取访问令牌的 CURL 请求。 请务必将 `<CLIENT ID>` 替换为在[创建用户分配的 MSI](#create-a-user-assigned-msi) 中由 `az identity create` 命令返回的 `clientId` 属性：
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > 在上面的请求中，`resource` 参数的值必须与 Azure AD 预期的值完全一致。 如果使用 Azure 存储资源 ID，必须在 URI 的结尾添加斜线。
   > 在下面的响应中，为简洁起见，已缩短了 access_token 元素。

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. 现在使用访问令牌访问 Azure 存储，例如，读取先前上传到容器的示例文件的内容。 将 `<STORAGE ACCOUNT>`、`<CONTAINER NAME>` 和 `<FILE NAME>` 的值替换为先前指定的值，将 `<ACCESS TOKEN>` 替换为上一步返回的令牌。

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   响应包含文件内容：

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>后续步骤

- 有关 MSI 的概述，请参阅[托管服务标识概述](msi-overview.md)。
- 若要了解如何使用存储 SAS 凭据完成此同一教程，请参阅[使用 Linux VM 托管服务标识通过 SAS 凭据访问 Azure 存储](msi-tutorial-linux-vm-access-storage-sas.md)
- 有关 Azure 存储帐户 SAS 功能的详细信息，请参阅：
  - [使用共享访问签名 (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Constructing a Service SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)（构造服务 SAS）

使用以下评论部分提供反馈，帮助我们改进内容。





