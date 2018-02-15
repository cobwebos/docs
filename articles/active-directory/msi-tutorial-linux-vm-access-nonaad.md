---
title: "使用 Linux VM MSI 访问 Azure Key Vault"
description: "本教程逐步介绍了如何使用 Linux VM 托管服务标识 (MSI) 访问 Azure 资源管理器。"
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 0e9eba9baeec00f13880ba6b32d87be8e5872bc8
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>使用 Linux VM 托管服务标识 (MSI) 访问 Azure Key Vault 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教程介绍如何为 Linux 虚拟机启用托管服务标识 (MSI)，然后使用该标识访问 Azure Key Vault。 作为引导，Key Vault 随后可让客户端应用程序使用机密访问未受 Azure Active Directory (AD) 保护的资源。 托管服务标识由 Azure 自动管理，可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中插入凭据了。 

学习如何：

> [!div class="checklist"]
> * 在 Linux 虚拟机上启用 MSI 
> * 授予 VM 对 Key Vault 中存储的密钥的访问权限 
> * 使用 VM 标识获取访问令牌，并使用它来检索 Key Vault 中的密钥 
 
## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

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
6. 选择 VM 大小。 若要查看更多大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在“设置”页中保留默认值，然后单击“确定”。

## <a name="enable-msi-on-your-vm"></a>在 VM 上启用 MSI

通过虚拟机 MSI，可以从 Azure AD 获取访问令牌，而无需在代码中插入凭据。 幕后运行机制是，启用 MSI 可以在 VM 上安装 MSI VM 扩展，并为 VM 启用 MSI。  

1. 选择要在其上启用 MSI 的虚拟机。
2. 在左侧导航栏中，单击“配置”。
3. 此时，将会看到托管服务标识。 若要注册并启用 MSI，请选择“是”，若要禁用，请选择“否”。
4. 务必单击“保存”，以保存配置。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 若要检查在此 Linux VM 上安装了哪些扩展，请单击“扩展”。 如果 MSI 已启用，列表中会显示“ManagedIdentityExtensionforLinux”。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>授予 VM 对 Key Vault 中存储的密钥的访问权限  

使用 MSI，代码可以获取访问令牌，通过支持 Azure Active Directory 身份验证的资源的身份验证。 但是，并非所有 Azure 服务都支持 Azure AD 身份验证。 若要将 MSI 用于这些服务，请将服务凭据存储在 Azure Key Vault 中，然后使用 MSI 访问 Key Vault 以检索凭据。 

首先，需要创建 Key Vault 并授予 VM 的标识对 Key Vault 的访问权限。   

1. 在左侧导航栏的顶部，依次选择“+ 新建”、“安全性 + 标识”以及“Key Vault”。  
2. 为新 Key Vault 提供一个名称。 
3. 定位到之前创建的 VM 所在的订阅和资源组中的 Key Vault。 
4. 选择“访问策略”，然后单击“添加新”。 
5. 在模板中的“配置”中，选择“密钥管理”。 
6. 选择“选择主体”，并在搜索字段中输入之前创建的 VM 的名称。  选择结果列表中的 VM，并单击“选择”。 
7. 单击“确定”完成添加新的访问策略，然后单击“确定”完成访问策略选择。 
8. 单击“创建”完成创建 Key Vault。 

    ![Alt 图像文本](media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

接下来，将密钥添加到 Key Vault，以便稍后可以使用在 VM 中运行的代码检索此密钥： 

1. 选择“所有资源”，找到并选择已创建的 Key Vault。 
2. 选择“密钥”，然后单击“添加”。 
3. 从“上传选项”中选择“手动”。 
4. 输入密钥的名称和值。  该值可以是任何需要的内容。 
5. 明确指定激活日期和到期日期，并将“已启用”设置为“是”。 
6. 单击“创建”以创建密钥。 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>使用 VM 标识获取访问令牌，并使用它来检索 Key Vault 中的密钥  

若要完成这些步骤，需要使用 SSH 客户端。  如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 客户端。 如果需要有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](../virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](../virtual-machines/linux/mac-create-ssh-keys.md)。
 
1. 在门户中，转到 Linux VM，并单击“概述”中的“连接”。 
2. 使用所选的 SSH 客户端连接到 VM。 
3. 在终端窗口中，使用 CURL 向本地 MSI 终结点发出请求，以获取访问 Azure Key Vault 所需的访问令牌。  
 
    下面是用于获取访问令牌的 CURL 请求。  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    响应包括访问资源管理器所需的访问令牌。 
    
    响应：  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    可以使用此访问令牌对 Azure Key Vault 进行身份验证。  下一个 CURL 请求显示如何使用 CURL 和 Key Vault REST API 从 Key Vault 读取密钥。  将需要 Key Vault 的 URL，该 URL 位于 Key Vault 的“概述”页的“软件包”部分。  另外，还需要在前面的调用中获取的访问令牌。 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    响应将如下所示： 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
在检索 Key Vault 中的密钥后，可以使用该密钥对需要名称和密码的服务进行身份验证。


## <a name="related-content"></a>相关内容

- 有关 MSI 的概述，请参阅[托管服务标识概述](../active-directory/msi-overview.md)。

使用以下评论部分提供反馈，帮助我们改进内容。




