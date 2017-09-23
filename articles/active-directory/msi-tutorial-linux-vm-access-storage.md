---
title: "使用 Linux VM MSI 访问 Azure 存储"
description: "本教程将指导你完成使用 Linux VM 托管服务标识 (MSI) 访问 Azure 存储的过程。"
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 9fec51528d1203a7107558408ced42fa2b2b884a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---


# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-storage-credentials"></a>使用 Linux VM 托管服务标识访问存储凭据

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教程演示了如何为 Linux 虚拟机启用托管服务标识 (MSI)，然后使用该标识访问存储密钥。 可以像平常在执行存储操作时一样使用存储密钥，例如使用存储 SDK 时。 本教程将使用 Azure CLI 上传和下载 blob。 将了解如何执行以下操作：


> [!div class="checklist"]
> * 在 Linux 虚拟机上启用 MSI 
> * 新建存储帐户
> * 授予 VM 访问存储的权限
> * 使用 VM 标识获取存储帐户的访问令牌，以访问存储密钥 


如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Linux 虚拟机

本教程将新建一个 Linux VM。 另外，还可以在现有 VM 上启用 MSI。

1. 单击 Azure 门户左上角的“新建”按钮。
2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
3. 输入虚拟机信息。 对于“身份验证类型”，选择“SSH 公钥”或“密码”。 使用创建的凭据可以登录 VM。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 在下拉列表中为虚拟机选择一个订阅。
5. 若要选择要在其中创建虚拟机的新资源组，请选择“新建”。 完成后，单击“确定”。
6. 选择 VM 大小。 若要查看更多大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在设置边栏选项卡中保留默认值，然后单击“确定”。

## <a name="enable-msi-on-your-vm"></a>在 VM 上启用 MSI

通过虚拟机 MSI，可以从 Azure AD 获取访问令牌，而无需在代码中插入凭据。 事实上，启用 MSI 会执行两项操作：在 VM 上安装 MSI VM 扩展，以及为 VM 启用托管服务标识。  

1. 选择要在其上启用 MSI 的虚拟机。
2. 在左侧导航栏中，单击“配置”。
3. 此时，将会看到“托管服务标识”。 若要注册并启用 MSI，请选择“是”，若要禁用，请选择“否”。
4. 务必单击“保存”，以保存配置。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 若要检查在此 Linux VM 上安装了哪些扩展，请单击“扩展”。 如果 MSI 已启用，列表中会显示“ManagedIdentityExtensionforLinux”。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="create-a-new-storage-account"></a>新建存储帐户 

可以像平常在执行存储操作时一样使用存储密钥，在本示例中，我们将重点介绍使用 Azure CLI 上传和下载 blob。 

1. 导航到侧栏并选择“存储”。  
2. 新建“存储帐户”。  
3. 在“部署模型”中，输入“资源管理器”、帐户类型”以及“常规用途”。  
4. 确保“订阅”和“资源组”与在上述步骤中创建 Linux 虚拟机时所使用的完全一致。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>授予 VM 标识访问权限以使用存储密钥 

使用 MSI，代码可以获取访问令牌，对支持 Azure AD 身份验证的资源进行身份验证。   

1. 导航到“存储”选项卡。  
2. 选择之前创建的特定“存储帐户”。   
3. 转到左侧面板中的“访问控制(IAM)”。  
4. 然后为 VM 添加一个新的角色分配，选取角色作为“存储帐户密钥运算符服务角色”。  
5. 在下一个下拉列表中，为资源虚拟机分配访问权限。  
6. 接下来，请确保“订阅”下拉列表中列出的订阅正确无误。 对于“资源组”，请选择“所有资源组”。  
7. 最后，在“选择”中，选择下拉列表中的 Linux 虚拟机并单击“保存”。 
    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 标识获取访问令牌，并使用它调用 Azure 资源管理器

需要使用 Bash 终端，然后在[此处](https://msdn.microsoft.com/commandline/wsl/install_guide)选择并下载 Linux 分发版本。


1. 在门户中，导航到 Linux 虚拟机，并在“概述”中单击“连接”。 系统将提示你使用 Bash，请记录警报中的 SSH 和 VM IP。 
2. 打开并连接到 Bash。  
3. 在此终端中，输入想要连接到的“SSH”和“VM”，例如“ssh admin@12.61.219.35”。  
4. 接下来，将提示你输入创建“Linux VM”时添加的“密码”。 然后应可以成功登录。  
5. 然后，可以使用 CURL 发出请求，获取已登录到的 Linux VM 的授权令牌。 “Azure 资源管理器”终结点是 https://management.azure.com。  

    下面是对访问令牌的 CURL 请求：
    
    ```bash
    curl --data "authority= https://login.microsoftonline.com/<TENANT ID>&&resource=https://management.azure.com/"  -H Metadata:true http://localhost:50432/oauth2/token   
    ```
    
    > [!NOTE]
    > 请确保你尝试请求访问的资源的 URL 包含正确的格式（以斜杠结尾，例如“https:<RESOURCE>/”）
    
    ```powershell
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="the-curl-request-to-get-storage-keys-from-azure-resource-manager"></a>用于获取 Azure 资源管理器中的存储密钥的 CURL 请求  

> [!NOTE]
> URL 中的文本区分大小写，因此如果对资源组使用了大小写格式，请务必在 URL 中相应地体现出来。 另外，请注意，这是 POST 请求而不是 GET 请求，请务必使用 -d 来传递一个值，以捕获长度限制，此值可以为 NULL。  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –request POST -d"" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

CURL 响应将提供一个密钥列表：  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


创建一个文件并将其上传。此文件将成为你能够在所创建的容器内的存储帐户上使用存储密钥上传的示例 blob 文件。 

在 Linux VM 上，可使用以下命令执行该操作。 

```bash
echo "This is a test file." > test.txt
```
 接下来，可以使用 Azure CLI 上传文件，并使用存储密钥对其进行身份验证。
 

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name 
                        [--account-name] 
                        [--account-key] 
```

响应： 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

此外，可以使用 Azure CLI 下载文件，并使用存储密钥对其进行身份验证。 

请求： 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         [--account-name]
                         [--account-key]  
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
    "lastModified": "2017-09-12T03:58:56+00:00",
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

## <a name="related-content"></a>相关内容

有关 MSI 的概述，请参阅[托管服务标识概述](../active-directory/msi-overview.md)。

使用以下评论部分提供反馈，帮助我们改进内容。


