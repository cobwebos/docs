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
ms.date: 09/19/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 8e4b3466143dc8b19df399913baca864b0af9bc0
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---


# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage"></a>使用 Linux VM 托管服务标识访问 Azure 存储

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教程演示了如何为 Linux 虚拟机启用托管服务标识 (MSI)，然后使用该标识访问存储密钥。 可以像平常在执行存储操作时一样使用存储密钥，例如使用存储 SDK 时。 本教程使用 Azure CLI 上传和下载 blob。 将了解如何执行以下操作：


> [!div class="checklist"]
> * 在 Linux 虚拟机上启用 MSI 
> * 授予 VM 对资源管理器中存储密钥的访问权限
> * 使用 VM 标识获取访问令牌并使用它从资源管理器检索存储密钥 


如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Linux 虚拟机

本教程将新建一个 Linux VM。 另外，还可以在现有 VM 上启用 MSI。

1. 单击 Azure 门户左上角的“新建”按钮。
2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
3. 输入虚拟机信息。 对于“身份验证类型”，选择“SSH 公钥”或“密码”。 使用创建的凭据可以登录 VM。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 在“订阅”下拉列表中，选择虚拟机对应的订阅。
5. 若要在新资源组中创建虚拟机，请选择“资源组”中的“新建”。 完成后，单击“确定”。
6. 选择 VM 大小。 若要查看更多大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在设置边栏选项卡中保留默认值，然后单击“确定”。

## <a name="enable-msi-on-your-vm"></a>在 VM 上启用 MSI

通过虚拟机 MSI，可以从 Azure AD 获取访问令牌，而无需在代码中插入凭据。 事实上，启用 MSI 会执行两项操作：在 VM 上安装 MSI VM 扩展，以及为 VM 启用托管服务标识。  

1. 导航到新虚拟机的资源组，并选择已在上一步中创建的虚拟机。
2. 在左侧的 VM 设置中，单击“配置”。
3. 若要注册并启用 MSI，请选择“是”，若要禁用，请选择“否”。
4. 务必单击“保存”，以保存配置。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 若要检查 VM 上安装了哪些扩展，请单击“扩展”。 如果 MSI 已启用，列表中会显示“ManagedIdentityExtensionforLinux”。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>创建存储帐户 

如果还没有存储帐户，现在将创建存储帐户。  也可以跳过此步骤，并授予 VM 对现有存储帐户密钥的 MSI 访问权限。 

1. 单击 Azure 门户左上角的“新建”按钮。
2. 依次单击“存储”、“存储帐户”，并将显示新的“创建存储帐户”面板。
3. 输入存储帐户的**名称**，稍后将使用该名称。  
4. “部署模型”和“帐户类型”应分别设置为“资源管理器”和“通用”。 
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。
6. 单击“创建” 。

    ![新建存储帐户](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在存储帐户中创建 Blob 容器

稍后我们会将文件上传并下载到新存储帐户。 由于文件需要 blob 存储，我们需要创建用于存储文件的 blob 容器。

1. 导航回新创建的存储帐户。
2. 在左侧导航栏上的“Blob 服务”下单击“容器”链接。
3. 单击页面顶部的“+ 容器”，将滑出“新建容器”面板。
4. 为容器指定名称，选择访问级别，单击“确定”。 在本教程中的后面部分将使用所指定的名称。 

    ![创建存储容器](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-identity-access-to-use-storage-keys"></a>授予 VM 标识访问权限以使用存储密钥 

Azure 存储原本不支持 Azure AD 身份验证。  但是，可以使用 MSI 从资源管理器检索存储密钥，并使用这些密钥访问存储。  在此步骤中，将授予 VM 对存储帐户密钥的 MSI 访问权限。   

1. 导航到“存储”选项卡。  
2. 选择之前创建的特定“存储帐户”。   
3. 转到左侧面板中的“访问控制(IAM)”。  
4. 然后为 VM 添加一个新的角色分配，选取角色作为“存储帐户密钥运算符服务角色”。  
5. 在下一个下拉列表中，为资源虚拟机分配访问权限。  
6. 接下来，请确保“订阅”下拉列表中列出的订阅正确无误。 对于“资源组”，请选择“所有资源组”。  
7. 最后，在“选择”中，选择下拉列表中的 Linux 虚拟机并单击“保存”。 
    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 标识获取访问令牌，并使用它调用 Azure 资源管理器

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/install_guide)中使用 SSH 客户端。

1. 在门户中，导航到 Linux 虚拟机，并在“概述”中单击“连接”。 系统将提示你使用 Bash，请记录警报中的 SSH 和 VM IP。 
2. 打开并连接到 Bash。  
3. 在此终端中，输入想要连接到的“SSH”和“VM”，例如“ssh admin@12.61.219.35”。  
4. 接下来，将提示你输入创建“Linux VM”时添加的“密码”。 然后应可以成功登录。  
5. 使用 CURL 获取 Azure 资源管理器的访问令牌。  

    下面是对访问令牌的 CURL 请求：
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > “资源”参数的值必须完全匹配 Azure AD 预期的值。 如果使用 Azure 资源管理器资源 ID，必须在 URI 的结尾添加斜线。
    
    ```bash
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="use-curl-to-get-storage-keys-from-azure-resource-manager"></a>使用 CURL 从 Azure 资源管理器获取存储密钥  

现在，使用在上一部分中检索到的访问令牌通过 CURL 调用资源管理器，以便检索存储访问密钥。 获得存储访问密钥后，便可以调用存储上传/下载操作。

> [!NOTE]
> URL 中的文本区分大小写，因此如果对资源组使用了大小写格式，请务必在 URL 中相应地体现出来。 另外，请注意，这是 POST 请求而不是 GET 请求，请务必使用 -d 来传递一个值，以捕获长度限制，此值可以为 NULL。  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –-request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

CURL 响应将提供一个密钥列表：  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


创建要上传到 blob 存储容器的示例 blob 文件。 在 Linux VM 上，可使用以下命令执行该操作。 

```bash
echo "This is a test file." > test.txt
```

接下来，使用 Azure CLI 上传文件，并使用存储密钥进行身份验证。
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
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
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

响应： 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
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
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
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


