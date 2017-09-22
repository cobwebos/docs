---
title: "使用 Windows VM MSI 访问 Azure 存储"
description: "本教程逐步介绍了如何使用 Windows VM 托管服务标识 (MSI) 访问 Azure 存储。"
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
ms.openlocfilehash: 86d88e3d44f442171f69d0baea5e7d689b963277
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---

# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-storage"></a>使用 Windows VM 托管服务标识 (MSI) 访问 Azure 存储

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教程演示了如何为 Linux 虚拟机启用托管服务标识 (MSI)，然后使用该标识访问存储密钥。 可以像平常在执行存储操作时一样使用存储密钥，例如使用存储 SDK 时。 本教程将使用 Azure CLI 上传和下载 blob。 将了解如何执行以下操作：


> [!div class="checklist"]
> * 在 Windows 虚拟机上启用 MSI 
> * 新建存储帐户
> * 授予 VM 访问存储的权限 
> * 使用 VM 标识获取存储帐户的访问令牌 


如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Windows 虚拟机

本教程将新建 Windows VM。 另外，还可以在现有 VM 上启用 MSI。

1.  单击 Azure 门户左上角的“新建”按钮。
2.  选择“计算”，然后选择“Windows Server 2016 Datacenter”。 
3.  输入虚拟机信息。 此处创建的用户名和密码是用于登录虚拟机的凭据。
4.  在下拉列表中为虚拟机选择正确的订阅。
5.  若要在新资源组中创建虚拟机，请选择“资源组”中的“新建”。 完成后，单击“确定”。
6.  选择 VM 大小。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在设置边栏选项卡中保留默认值，然后单击“确定”。

    ![Alt 图像文本](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>在 VM 上启用 MSI

通过虚拟机 MSI，可以从 Azure AD 获取访问令牌，而无需在代码中插入凭据。 事实上，启用 MSI 会执行两项操作：在 VM 上安装 MSI VM 扩展，以及为虚拟机启用 MSI。  

1. 选择要在其上启用 MSI 的虚拟机。
2. 在左侧导航栏中，单击“配置”。
3. 此时，将会看到“托管服务标识”。 若要注册并启用 MSI，请选择“是”，若要禁用，请选择“否”。
4. 务必单击“保存”，以保存配置。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 若要检查在此 Windows VM 上安装了哪些扩展，请单击“扩展”。 如果 MSI 已启用，列表中会显示“ManagedIdentityExtensionforWindows”。

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
7. 最后，在“选择”中，选择下拉列表中的 Windows 虚拟机并单击“保存”。 

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 标识获取访问令牌，并使用它调用 Azure 资源管理器 

在此部分中将需要使用 PowerShell。  如果尚未安装，请在[此处](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1)进行下载。 

1. 在门户中，导航到“虚拟机”并转到 Windows 虚拟机，然后在“概述”中，单击“连接”。 
2. 输入创建 Windows VM 时添加的用户名和密码。 
3. 现在，已经创建了与虚拟机的远程桌面连接，请在远程会话中打开 PowerShell。 
4. 使用 Powershell 的 Invoke-WebRequest，向本地 MSI 终结点发出请求以获取 Azure 资源管理器的访问令牌。

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost/50342/oauth2/token -Method GET -Body @resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > “资源”参数的值必须完全匹配 Azure AD 预期的值。 如果使用 Azure 资源管理器资源 ID，必须在 URI 的结尾添加斜线。
    
    接下来，提取完整响应，响应以 JavaScript 对象表示法 (JSON) 格式字符串的形式存储在 $response 对象中。 
    
    ```powershell
    $content = $repsonse.Content | ConvertFrom-Json
    ```
    接下来，从响应中提取访问令牌。
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    最后，使用访问令牌调用 Azure 资源管理器。 在此示例中，我们还使用 PowerShell 的 Invoke-WebRequest 调用 Azure 资源管理器，并将访问令牌包含在授权标头中。
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE]
    > URL 区分大小写。因此，请确保大小写与之前在命名资源组时使用的大小写完全相同，并确保“resourceGroup”使用的是大写“G”。
    
## <a name="get-the-storage-keys-from-azure-resource-manager"></a>获取 Azure 资源管理器中的存储密钥 

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/97f51385-2edc-4b69-bed8-7778dd4cb761/resourceGroups/SKwan_Test/providers/Microsoft.Storage/storageAccounts/skwanteststorage/listKeys/?api-version=2016-12-01 -Method POST$ -Headers @{Authorization="Bearer $ARMToken"}
```

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
```

```powershell
PS C:\> $key = $keysContent.keys[0].value
```

**使用 Azure CLI 创建要上传的文件**

```bash
echo "This is a test text file." > test.txt
```

**使用 Azure CLI 上传文件，并使用存储密钥对其进行身份验证**

> [!NOTE]
> 首先请记得安装 Azure 存储 commandlet“Install-Module Azure.Storage”。 

PowerShell 请求：


```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName skwanteststorage -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container testcontainer -Blob testblob -Context $ctx
```

响应：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

**使用 Azure CLI 下载文件，并使用存储密钥对其进行身份验证**

PowerShell 请求：

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <container name> -Destination <file> -Context $ctx
```

响应：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```





