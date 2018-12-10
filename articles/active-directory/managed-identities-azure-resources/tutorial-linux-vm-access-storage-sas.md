---
title: 使用 Linux VM 系统分配的托管标识通过 SAS 凭据访问 Azure 存储
description: 本教程介绍了如何使用 Linux VM 系统分配的托管标识通过 SAS 凭据（而不是存储帐户访问密钥）访问 Azure 存储。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 16bcc56dd05c86b7c2adb324de480e7482f80fef
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724292"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-identity-to-access-azure-storage-via-a-sas-credential"></a>教程：使用 Linux VM 系统分配的标识通过 SAS 凭据访问 Azure 存储

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教程介绍如何使用系统分配的托管标识为 Linux 虚拟机 (VM) 获取存储共享访问签名 (SAS) 凭据。 具体而言，是[服务 SAS 凭据](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures)。 

服务 SAS 提供了在不公开帐户访问密钥的情况下授权特定的服务（在我们的示例中为 blob 服务）在有限时间内访问存储帐户中对象的权限。 可以像平常在执行存储操作时一样使用 SAS 凭据，例如使用存储 SDK 时。 对于本教程，我们将演示使用 Azure 存储 CLI 上传和下载 blob。 将了解如何执行以下操作：


> [!div class="checklist"]
> * 创建存储帐户
> * 在存储帐户中创建 Blob 容器
> * 向 VM 授予对资源管理器中的存储帐户 SAS 的访问权限 
> * 使用 VM 的标识获取一个访问令牌，并使用它从资源管理器检索 SAS 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>创建存储帐户 

如果还没有存储帐户，现在将创建存储帐户。  也可以跳过此步骤，并向 VM 的系统分配的托管标识授予对现有存储帐户密钥的访问权限。 

1. 单击 Azure 门户左上角的“+/创建新服务”按钮。
2. 依次单击“存储”、“存储帐户”，并将显示新的“创建存储帐户”面板。
3. 输入存储帐户的**名称**，稍后将使用该名称。  
4. “部署模型”和“帐户类型”应分别设置为“资源管理器”和“通用”。 
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。
6. 单击“创建”。

    ![新建存储帐户](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在存储帐户中创建 Blob 容器

稍后我们会将文件上传并下载到新存储帐户。 由于文件需要 blob 存储，我们需要创建用于存储文件的 blob 容器。

1. 导航回新创建的存储帐户。
2. 在左侧面板上，单击“Blob 服务”下的“容器”链接。
3. 单击页面顶部的“+ 容器”，将滑出“新建容器”面板。
4. 为容器指定名称，选择访问级别，单击“确定”。 在本教程中的后面部分将使用所指定的名称。 

    ![创建存储容器](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>授权 VM 的系统分配的托管标识使用存储 SAS 

Azure 存储原本不支持 Azure AD 身份验证。  但是，可以使用 VM 的系统分配的托管标识从资源管理器检索存储 SAS，然后使用 SAS 来访问存储。  在此步骤中，将向 VM 的系统分配的托管标识授予对存储帐户 SAS 的访问权限。   

1. 导航回新创建的存储帐户。
2. 单击左侧面板中的“访问控制(IAM)”链接。  
3. 单击页面顶部的“+ 添加角色分配”，为 VM 添加新的角色分配
4. 在页面左侧，将“角色”设置为“存储帐户参与者”。 
5. 在下一个下拉列表中，把“将访问权限分配给”设置为资源“虚拟机”。  
6. 接下来，确保“订阅”下拉列表中列出了正确的订阅，然后将“资源组”设置为“所有资源组”。  
7. 最后，在“选择”下，从下拉列表中选择你的 Linux 虚拟机，然后单击“保存”。  

    ![Alt 图像文本](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 标识获取访问令牌，并使用它调用 Azure 资源管理器

在本教程的剩余部分中，我们从先前创建的 VM 入手。

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/install_guide)中使用 SSH 客户端。 如果需要有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](../../virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](../../virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在 Azure 门户中，导航到“虚拟机”，转到 Linux 虚拟机，然后在“概述”页中单击顶部的“连接”。 复制用于连接到 VM 的字符串。 
2. 使用 SSH 客户端连接到 VM。  
3. 接下来，将提示你输入创建“Linux VM”时添加的“密码”。 然后应可以成功登录。  
4. 使用 CURL 获取 Azure 资源管理器的访问令牌。  

    下面是用于获取访问令牌的 CURL 请求和响应：
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
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

在本教程中，你已学习了如何使用 Linux VM 系统分配的托管标识通过 SAS 凭据来访问 Azure 存储。  若要深入了解 Azure 存储 SAS，请参阅：

> [!div class="nextstepaction"]
>[使用共享访问签名 (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
