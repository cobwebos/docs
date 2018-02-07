---
title: "如何使用 Azure VM 托管服务标识登录"
description: "有关使用 Azure VM MSI 服务主体进行脚本客户端登录和资源访问的逐步说明与示例。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 2e4da8cd02a1d07a3225a0c1fda4c60928dba8a4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>如何使用 Azure VM 托管服务标识 (MSI) 登录 

[!INCLUDE [preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
本文提供有关使用 MSI 服务主体进行登录的 PowerShell 和 CLI 脚本示例，以及有关错误处理等重要主题的指导。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

如果打算使用本文中的 Azure PowerShell 或 Azure CLI 示例，请务必安装最新版本的 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) 或 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

> [!IMPORTANT]
> - 本文中的所有示例脚本假设命令行客户端在已启用 MSI 的虚拟机上运行。 在 Azure 门户中使用 VM 的“连接”功能远程连接到 VM。 有关在 VM 上启用 MSI 的详细信息，请参阅[使用 Azure 门户配置 VM 托管服务标识 (MSI)](msi-qs-configure-portal-windows-vm.md)，或有关在不同的工具中（使用 PowerShell、CLI、模板或 Azure SDK）执行此操作的文章之一。 
> - 为了防止访问资源期间出错，必须在相应的范围（VM 或更高）至少为 VM 的 MSI 授予“读取者”访问权限，以允许在 VM 上执行 Azure 资源管理器操作。 有关详细信息，请参阅[使用 Azure 门户授予托管服务标识 (MSI) 对资源的访问权限](msi-howto-assign-access-portal.md)。

## <a name="overview"></a>概述

MSI 提供一个[服务主体](develop/active-directory-dev-glossary.md#service-principal-object)，该服务主体是在 VM 上[启用 MSI 时创建](msi-overview.md#how-does-it-work)的。 可向此服务主体授予对 Azure 资源的访问权限；脚本/命令行客户端可将此服务主体用作标识进行登录和资源访问。 传统上，若要使用自身的标识访问受保护资源，脚本客户端需要：  

   - 注册到 Azure AD，并同意将 Azure AD 用作机密/Web 客户端应用程序
   - 使用应用的凭据（可能会嵌入在脚本中）以自身的服务主体登录

使用 MSI 时，脚本客户端不再需要采用上述任何做法，因为它可以使用 MSI 服务主体登录。 

## <a name="azure-cli"></a>Azure CLI

以下脚本演示如何：

1. 使用 VM 的 MSI 服务主体登录到 Azure AD  
2. 调用 Azure 资源管理器并获取 VM 的服务主体 ID。 CLI 负责自动管理令牌的获取和使用。 请务必将 `<VM-NAME>` 替换为自己的虚拟机名称。  

   ```azurecli
   az login --msi
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

以下脚本演示如何：

1. 获取 VM 的 MSI 访问令牌。  
2. 使用该访问令牌以相应的 MSI 服务主体登录到 Azure AD。   
3. 调用 Azure 资源管理器 cmdlet 获取有关 VM 的信息。 PowerShell 负责自动管理令牌的使用。  

   ```azurepowershell
   # Get an access token for the MSI
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                                 -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
   $content =$response.Content | ConvertFrom-Json
   $access_token = $content.access_token
   echo "The MSI access token is $access_token"

   # Use the access token to sign in under the MSI service principal. -AccountID can be any string to identify the session.
   Login-AzureRmAccount -AccessToken $access_token -AccountId "MSI@50342"

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Azure 服务的资源 ID

有关支持 Azure AD 且已使用 MSI 进行测试的资源列表及其相应资源 ID，请参阅[支持 Azure AD 身份验证的 Azure 服务](msi-overview.md#azure-services-that-support-azure-ad-authentication)。

## <a name="error-handling-guidance"></a>有关错误处理的指导 

如下所示的响应可能表示未正确配置 VM 的 MSI：

- PowerShell：“Invoke-WebRequest: 无法连接到远程服务器”
- CLI：“MSI: 无法从 'http://localhost:50342/oauth2/token' 检索令牌，出现错误 'HTTPConnectionPool (主机='localhost'，端口=50342)” 

如果收到以下错误之一，请在 [Azure 门户](https://portal.azure.com)中返回到 Azure VM 并执行以下操作：

- 转到“配置”页，确保“托管服务标识”设置为“是”。
- 转到“扩展”页，确保已成功部署 MSI 扩展。

如果有任一项不正确，可能都需要在资源上再次重新部署 MSI，或排查部署故障。 如需 VM 配置方面的帮助，请参阅[使用 Azure 门户配置 VM 托管服务标识 (MSI)](msi-qs-configure-portal-windows-vm.md)。

## <a name="related-content"></a>相关内容

- 若要在 Azure VM 上启用 MSI，请参阅[使用 PowerShell 配置 VM 托管服务标识 (MSI)](msi-qs-configure-powershell-windows-vm.md)，或[使用 Azure CLI 配置 VM 托管服务标识 (MSI)](msi-qs-configure-cli-windows-vm.md)

使用以下评论部分提供反馈，帮助我们改进内容。








