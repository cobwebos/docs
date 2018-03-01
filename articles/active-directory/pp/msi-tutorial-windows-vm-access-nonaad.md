---
title: "使用 Windows VM MSI 访问 Azure Key Vault"
description: "本教程逐步介绍了如何使用 Windows VM 托管服务标识 (MSI) 访问 Azure Key Vault。"
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 151a0a1fee72c7bd7adcda8e23ebb06d96e822ae
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>使用 Windows VM 托管服务标识 (MSI) 访问 Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教程介绍如何为 Windows 虚拟机启用托管服务标识 (MSI)，然后使用该标识访问 Azure Key Vault。 作为引导，Key Vault 随后可让客户端应用程序使用机密访问未受 Azure Active Directory (AD) 保护的资源。 托管服务标识由 Azure 自动管理，可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中插入凭据了。 

学习如何：


> [!div class="checklist"]
> * 在 Windows 虚拟机上启用托管服务标识 
> * 授予 VM 对 Key Vault 中存储的密钥的访问权限 
> * 使用 VM 标识获取访问令牌，并使用它来检索 Key Vault 中的密钥 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Windows 虚拟机

本教程将新建 Windows VM。 另外，还可以在现有 VM 上启用 MSI。

1.  单击 Azure 门户左上角的“创建资源”。
2.  选择“计算”，然后选择“Windows Server 2016 Datacenter”。 
3.  输入虚拟机信息。 此处创建的用户名和密码是用于登录虚拟机的凭据。
4.  在下拉列表中为虚拟机选择正确的订阅。
5.  若要在新资源组中创建虚拟机，请选择“资源组”中的“新建”。 完成后，单击“确定”。
6.  选择 VM 大小。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在设置边栏选项卡中保留默认值，然后单击“确定”。

    ![Alt 图像文本](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>在 VM 上启用 MSI 

通过虚拟机 MSI，可以从 Azure AD 获取访问令牌，而无需在代码中插入凭据。 启用 MSI 会告诉 Azure 为虚拟机创建托管标识。 事实上，启用 MSI 会执行两项操作：在 VM 上安装 MSI VM 扩展，以及在 Azure 资源管理器中启用 MSI。

1.  选择要在其上启用 MSI 的虚拟机。  
2.  在左侧导航栏中，单击“配置”。 
3.  此时，将会看到托管服务标识。 若要注册并启用 MSI，请选择“是”，若要禁用，请选择“否”。 
4.  务必单击“保存”，以保存配置。  

    ![Alt 图像文本](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 若要查看并确认在此 VM 上安装了哪些扩展，请单击“扩展”。 如果 MSI 已启用，列表中会显示现“ManagedIdentityExtensionforWindows”。

    ![Alt 图像文本](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>授予 VM 对 Key Vault 中存储的密钥的访问权限 
 
使用 MSI，代码可以获取访问令牌，对支持 Azure AD 身份验证的资源进行身份验证。  但是，并非所有 Azure 服务都支持 Azure AD 身份验证。 若要将 MSI 用于这些服务，请将服务凭据存储在 Azure Key Vault 中，然后使用 MSI 访问 Key Vault 以检索凭据。 

首先，需要创建 Key Vault 并授予 VM 的标识对 Key Vault 的访问权限。   

1. 在左侧导航栏的顶部，依次选择“+ 新建”、“安全性 + 标识”以及“Key Vault”。  
2. 为新 Key Vault 提供一个名称。 
3. 定位到之前创建的 VM 所在的订阅和资源组中的 Key Vault。 
4. 选择“访问策略”，然后单击“添加新”。 
5. 在模板中的“配置”中，选择“密钥管理”。 
6. 选择“选择主体”，并在搜索字段中输入之前创建的 VM 的名称。  选择结果列表中的 VM，并单击“选择”。 
7. 单击“确定”完成添加新的访问策略，然后单击“确定”完成访问策略选择。 
8. 单击“创建”完成创建 Key Vault。 

    ![Alt 图像文本](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


接下来，将密钥添加到 Key Vault，以便稍后可以使用在 VM 中运行的代码检索此密钥： 

1. 选择“所有资源”，找到并选择已创建的 Key Vault。 
2. 选择“密钥”，然后单击“添加”。 
3. 从“上传选项”中选择“手动”。 
4. 输入密钥的名称和值。  该值可以是任何需要的内容。 
5. 明确指定激活日期和到期日期，并将“已启用”设置为“是”。 
6. 单击“创建”以创建密钥。 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>使用 VM 标识获取访问令牌，并使用它来检索 Key Vault 中的密钥  

如果未安装 PowerShell 4.3.1 或更高版本，则需要[下载并安装最新版本](https://docs.microsoft.com/powershell/azure/overview)。

首先，我们使用 VM 的 MSI 获取访问令牌，向 Key Vault 进行身份验证：
 
1. 在门户中，导航到“虚拟机”并转到 Windows 虚拟机，然后在“概述”中，单击“连接”。
2. 输入创建 Windows VM 时添加的用户名和密码。  
3. 现在，已经创建了与虚拟机的远程桌面连接，请在远程会话中打开 PowerShell。  
4. 在 PowerShell 中，调用租户上的 Web 请求，为 VM 特定端口中的本地主机获取令牌。  

    PowerShell 请求：
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    接下来，提取完整响应，响应以 JavaScript 对象表示法 (JSON) 格式字符串的形式存储在 $response 对象中。  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    接下来，从响应中提取访问令牌。  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    最后，使用 PowerShell 的 Invoke-WebRequest 命令检索之前在 Key Vault 中创建的密钥，在授权标头中传递访问令牌。  将需要 Key Vault 的 URL，该 URL 位于 Key Vault 的“概述”页的“软件包”部分。  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    响应将如下所示： 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
在检索 Key Vault 中的密钥后，可以使用该密钥对需要名称和密码的服务进行身份验证。 

## <a name="related-content"></a>相关内容

- 有关 MSI 的概述，请参阅[托管服务标识概述](msi-overview.md)。

使用以下评论部分提供反馈，帮助我们改进内容。
