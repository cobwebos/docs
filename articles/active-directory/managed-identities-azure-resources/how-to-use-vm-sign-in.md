---
title: 使用 Azure VM 上的托管标识进行登录 Azure AD
description: 有关使用 Azure 资源服务主体的 Azure VM 托管标识进行脚本客户端登录和资源访问的逐步说明与示例。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f4dc749c0254b5aa4e9ff018d2a869832de3f0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547386"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>如何在 Azure VM 上使用 Azure 资源的托管标识进行登录 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
本文提供有关使用 Azure 资源服务主体的托管标识进行登录的 PowerShell 和 CLI 脚本示例，以及有关错误处理等重要主题的指导。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

如果打算使用本文中的 Azure PowerShell 或 Azure CLI 示例，请务必安装最新版本的 [Azure PowerShell](/powershell/azure/install-az-ps) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

> [!IMPORTANT]
> - 本文中的所有示例脚本均假设命令行客户端在已启用 Azure 资源的托管标识的 VM 上运行。 在 Azure 门户中使用 VM 的“连接”功能远程连接到 VM。 有关在 VM 上启用 Azure 资源的托管标识的详细信息，请参阅[使用 Azure 门户在 VM 上配置 Azure 资源的托管标识](qs-configure-portal-windows-vm.md)，或有关在不同工具（使用 PowerShell、CLI、模板或 Azure SDK）中执行此操作的文章之一。 
> - 为了防止访问资源期间出错，必须在相应的范围（VM 或更高）至少为 VM 的托管标识授予“读取者”的访问权限，从而能够在 VM 上执行 Azure 资源管理器操作。 有关详细信息，请参阅[使用 Azure 门户授予 Azure 资源的托管标识对资源的访问权限](howto-assign-access-portal.md)。

## <a name="overview"></a>概述

Azure 资源的托管标识提供一个[服务主体对象](../develop/developer-glossary.md#service-principal-object)，此对象是在 VM 上[启用 Azure 资源的托管标识时创建的](overview.md#how-does-the-managed-identities-for-azure-resources-work)。 可向此服务主体授予对 Azure 资源的访问权限；脚本/命令行客户端可将此服务主体用作标识进行登录和资源访问。 传统上，若要使用自身的标识访问受保护资源，脚本客户端需要：  

   - 注册到 Azure AD，并同意将 Azure AD 用作机密/Web 客户端应用程序
   - 使用应用的凭据（可能会嵌入在脚本中）以自身的服务主体登录

凭借 Azure 资源的托管标识，脚本客户端无需再执行任何操作，因为它可以通过 Azure 资源服务主体的托管标识进行登录。 

## <a name="azure-cli"></a>Azure CLI

以下脚本演示如何：

1. 通过 VM 的 Azure 资源服务主体的托管标识登录 Azure AD  
2. 调用 Azure 资源管理器并获取 VM 的服务主体 ID。 CLI 负责自动管理令牌的获取和使用。 请务必将 `<VM-NAME>` 替换为自己的虚拟机名称。  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

以下脚本演示如何：

1. 通过 VM 的 Azure 资源服务主体的托管标识登录 Azure AD  
2. 调用 Azure 资源管理器 cmdlet 获取有关 VM 的信息。 PowerShell 负责自动管理令牌的使用。  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Azure 服务的资源 ID

有关支持 Azure AD 且已使用 Azure 资源的托管标识进行测试的资源列表及其相应资源 ID，请参阅[支持 Azure AD 身份验证的 Azure 服务](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

## <a name="error-handling-guidance"></a>有关错误处理的指导 

如下所示的响应可能表示未正确配置 VM 的 Azure 资源的托管标识：

- PowerShell：“Invoke-WebRequest: 无法连接到远程服务器”
- CLI： *MSI：无法从 `http://localhost:50342/oauth2/token` 检索令牌，出现错误 "HTTPConnectionPool （主机 = ' localhost '，端口 = 50342）"* 

如果收到以下错误之一，请在 [Azure 门户](https://portal.azure.com)中返回到 Azure VM 并执行以下操作：

- 转到“标识”页面，确保“系统分配的”已设置为“是”。
- 转到“扩展”页面，确保已成功部署 Azure 资源扩展的托管标识（计划在 2019 年 1 月弃用）。

如果有任何一项不正确，可能都需要在资源上再次重新部署 Azure 资源的托管标识，或排查部署故障。 如需 VM 配置方面的帮助，请参阅[使用 Azure 门户在 VM 上配置 Azure 资源的托管标识](qs-configure-portal-windows-vm.md)。

## <a name="next-steps"></a>后续步骤

- 若要在 Azure VM 上启用 Azure 资源的托管标识，请参阅[使用 PowerShell 在 Azure VM 上配置 Azure 资源的托管标识](qs-configure-powershell-windows-vm.md)或[使用 Azure CLI 在 Azure VM 上配置 Azure 资源的托管标识](qs-configure-cli-windows-vm.md)






