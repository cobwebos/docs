---
title: "使用 Linux VM MSI 通过 SAS 凭据访问 Azure 存储"
description: "本教程介绍如何使用 Linux VM 托管服务标识 (MSI) 通过 SAS 凭据（而不是存储帐户访问密钥）访问 Azure 存储。"
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f947d1c0a26a92e6ec179b82ca5015df5e45a634
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>使用 Linux VM 托管服务标识通过 SAS 凭据访问 Azure 存储

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教程介绍如何为 Linux 虚拟机启用托管服务标识 (MSI)，然后使用 MSI 来获取存储共享访问签名 (SAS) 凭据。 具体而言，是[服务 SAS 凭据](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures)。 

服务 SAS 提供了在不公开帐户访问密钥的情况下授权特定的服务（在我们的示例中为 blob 服务）在有限时间内访问存储帐户中对象的权限。 可以像平常在执行存储操作时一样使用 SAS 凭据，例如使用存储 SDK 时。 对于本教程，我们将演示使用 Azure 存储 CLI 上传和下载 blob。 将了解如何执行以下操作：


> [!div class="checklist"]
> * 在 Linux 虚拟机上启用 MSI 
> * 向 VM 授予对资源管理器中的存储帐户 SAS 的访问权限 
> * 使用 VM 的标识获取一个访问令牌，并使用它从资源管理器检索 SAS 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>登录 Azure
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Linux 虚拟机

本教程将新建一个 Linux VM。 另外，还可以在现有 VM 上启用 MSI。

1. 单击 Azure 门户左上角的“+/创建新服务”按钮。
2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
3. 输入虚拟机信息。 对于“身份验证类型”，选择“SSH 公钥”或“密码”。 使用创建的凭据可以登录 VM。

    ![Alt 图像文本](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 在“订阅”下拉列表中，选择虚拟机对应的订阅。
5. 若要在新资源组中创建虚拟机，请选择“资源组”中的“新建”。 完成后，单击“确定”。
6. 选择 VM 大小。 若要查看更多大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在设置边栏选项卡中保留默认值，然后单击“确定”。

## <a name="enable-msi-on-your-vm"></a>在 VM 上启用 MSI

通过虚拟机 MSI，可以从 Azure AD 获取访问令牌，而无需在代码中插入凭据。 事实上，启用 MSI 会执行两项操作：在 VM 上安装 MSI VM 扩展，以及为 VM 启用托管服务标识。  

1. 导航到新虚拟机的资源组，并选择已在上一步中创建的虚拟机。
2. 在左侧的 VM“设置”下，单击“配置”。
3. 若要注册并启用 MSI，请选择“是”，若要禁用，请选择“否”。
4. 务必单击“保存”，以保存配置。

    ![Alt 图像文本](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 若要检查 VM 上安装了哪些扩展，请单击“扩展”。 如果 MSI 已启用，列表中会显示“ManagedIdentityExtensionforLinux”。

    ![Alt 图像文本](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>创建存储帐户 

如果还没有存储帐户，现在将创建存储帐户。  也可以跳过此步骤，并授予 VM 对现有存储帐户密钥的 MSI 访问权限。 

1. 单击 Azure 门户左上角的“+/创建新服务”按钮。
2. 依次单击“存储”、“存储帐户”，并将显示新的“创建存储帐户”面板。
3. 输入存储帐户的**名称**，稍后将使用该名称。  
4. “部署模型”和“帐户类型”应分别设置为“资源管理器”和“通用”。 
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。
6. 单击“创建”。

    ![新建存储帐户](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在存储帐户中创建 Blob 容器

稍后我们会将文件上传并下载到新存储帐户。 由于文件需要 blob 存储，我们需要创建用于存储文件的 blob 容器。

1. 导航回新创建的存储帐户。
2. 在左侧面板上，单击“Blob 服务”下的“容器”链接。
3. 单击页面顶部的“+ 容器”，将滑出“新建容器”面板。
4. 为容器指定名称，选择访问级别，单击“确定”。 在本教程中的后面部分将使用所指定的名称。 

    ![创建存储容器](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>向 VM 的 MSI 授予访问权限以使用存储 SAS 

Azure 存储原本不支持 Azure AD 身份验证。  但是，可以使用 MSI 从资源管理器检索存储 SAS，然后使用 SAS 来访问存储。  在此步骤中，将向 VM MSI 授予对存储帐户 SAS 的访问权限。   

1. 导航回新创建的存储帐户。   
2. 单击左侧面板中的“访问控制(IAM)”链接。  
3. 单击页面顶部的“+ 添加”，为 VM 添加新的角色分配
4. 在页面左侧，将“角色”设置为“存储帐户参与者”。 
5. 在下一个下拉列表中，把“将访问权限分配给”设置为资源“虚拟机”。  
6. 接下来，确保“订阅”下拉列表中列出了正确的订阅，然后将“资源组”设置为“所有资源组”。  
7. 最后，在“选择”下，从下拉列表中选择你的 Linux 虚拟机，然后单击“保存”。  

    ![Alt 图像文本](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 标识获取访问令牌，并使用它调用 Azure 资源管理器

在本教程的剩余部分中，我们从先前创建的 VM 入手。

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/install_guide)中使用 SSH 客户端。 如果需要有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](~/articles/virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在 Azure 门户中，导航到“虚拟机”，转到 Linux 虚拟机，然后在“概述”页中单击顶部的“连接”。 复制用于连接到 VM 的字符串。 
2. 使用 SSH 客户端连接到 VM。  
3. 接下来，将提示你输入创建“Linux VM”时添加的“密码”。 然后应可以成功登录。  
4. 使用 CURL 获取 Azure 资源管理器的访问令牌。  

    下面是用于获取访问令牌的 CURL 请求和响应：
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > 在上面的请求中，“resource”参数的值必须与 Azure AD 预期的值完全一致。 如果使用 Azure 资源管理器资源 ID，必须在 URI 的结尾添加斜线。
    > 在下面的响应中，为简洁起见，已缩短了 access_token 元素。
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>从 Azure 资源管理器中获取 SAS 凭据，以便调用存储

现在，使用在上一部分中检索到的访问令牌通过 CURL 调用资源管理器，来创建存储 SAS 凭据。 获取 SAS 凭据后，便可以调用存储上传/下载操作。

对于此请求，我们将使用以下 HTTP 请求参数来创建 SAS 凭据：

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

这些参数包括在针对 SAS 凭据的请求的 POST 正文中。 有关用于创建 SAS 凭据的参数的详细信息，请参阅 [List Service SAS REST reference](/rest/api/storagerp/storageaccounts/listservicesas)（列出服务 SAS REST 参考）。

使用以下 CURL 请求来获取 SAS 凭据。 请务必将 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<STORAGE ACCOUNT NAME>`、`<CONTAINER NAME>` 和 `<EXPIRATION TIME>` 参数值替换为你自己的值。 将 `<ACCESS TOKEN>` 值替换为前面检索的访问令牌：

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> 上述 URL 中的文本区分大小写，因此如果对资源组使用了大小写格式，请务必在 URL 中相应地体现出来。 此外必须知道，这是 POST 请求，而不是 GET 请求。

CURL 响应返回 SAS 凭据：  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

创建要上传到 blob 存储容器的示例 blob 文件。 在 Linux VM 上，可使用以下命令执行该操作。 

```bash
echo "This is a test file." > test.txt
```

接下来，运行 CLI `az storage` 命令使用 SAS 凭据进行身份验证，并将文件上传到 blob 容器。 对于此步骤，需要在 VM 上[安装最新的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)（如果尚未安装）。

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

响应： 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

此外，可以使用 Azure CLI 下载文件，并使用 SAS 凭据对其进行身份验证。 

请求： 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

响应： 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>后续步骤

- 有关 MSI 的概述，请参阅[托管服务标识概述](msi-overview.md)。
- 若要了解如何使用存储帐户密钥完成此同一教程，请参阅[使用 Linux VM 托管服务标识访问 Azure 存储](msi-tutorial-linux-vm-access-storage.md)
- 有关 Azure 存储帐户 SAS 功能的详细信息，请参阅：
  - [使用共享访问签名 (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Constructing a Service SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)（构造服务 SAS）

使用以下评论部分提供反馈，帮助我们改进内容。
