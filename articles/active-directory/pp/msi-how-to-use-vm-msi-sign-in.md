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
ms.date: 01/05/2018
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5f71d27a9e07cc6d6a260b809e91aaa2a50270c
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>使用 VM 用户分配的托管服务标识 (MSI) 登录

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)] 本文提供有关使用用户分配的 MSI 服务主体进行登录的 CLI 脚本示例，以及有关错误处理等重要主题的指导。

## <a name="prerequisites"></a>系统必备

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

若要使用本文中的 Azure CLI 示例，请务必安装最新版本的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

> [!IMPORTANT]
> - 本文中的所有示例脚本假设命令行客户端在已启用 MSI 的虚拟机上运行。 在 Azure 门户中使用 VM 的“连接”功能远程连接到 VM。 有关在 VM 上启用 MSI 的详细信息，请参阅[使用 Azure CLI 配置 VM 托管服务标识 (MSI)](msi-qs-configure-cli-windows-vm.md)，或有关在不同的工具中（使用 PowerShell、门户、模板或 Azure SDK）执行此操作的文章之一。 
> - 为了防止登录和访问资源期间出错，必须在相应的范围（VM 或更高级别）至少为 MSI 授予“读取者”访问权限，以允许在 VM 上执行 Azure 资源管理器操作。 请参阅[使用 Azure CLI 授予托管服务标识 (MSI) 对资源的访问权限](msi-howto-assign-access-cli.md)获取详细信息。

## <a name="overview"></a>概述

MSI 提供一个[服务主体](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object)，该服务主体是在 VM 上[启用 MSI 时创建](msi-overview.md#how-does-it-work)的。 可向此服务主体授予对 Azure 资源的访问权限；脚本/命令行客户端可将此服务主体用作标识进行登录和资源访问。 传统上，若要使用自身的标识访问受保护资源，脚本客户端需要：  

   - 注册到 Azure AD，并同意将 Azure AD 用作机密/Web 客户端应用程序
   - 使用应用的凭据（可能会嵌入在脚本中）以自身的服务主体登录

使用 MSI 时，脚本客户端不再需要采用上述任何做法，因为它可以使用 MSI 服务主体登录。 

## <a name="azure-cli"></a>Azure CLI

以下脚本演示如何：

1. 使用用户分配的 MSI 服务主体登录到 Azure AD。  
2. 调用 Azure 资源管理器，获取 VM 的 Azure 区域位置。 CLI 负责自动管理令牌的获取和使用。 请务必将 `<VM NAME>` 替换为自己的 VM 名称，将 `<MSI ID>` 替换为用户分配的 MSI 资源 ID。 创建用户分配的 MSI 期间，在 `id` 属性中返回 MSI 资源 ID（请参阅[使用 Azure CLI 为 VM 配置用户分配的托管服务标识 (MSI)](msi-qs-configure-cli-windows-vm.md) 获取 `az identity create` 命令的示例）。

    ```azurecli
    az login --msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    响应示例：
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>Azure 服务的资源 ID

有关支持 Azure AD 且已使用 MSI 进行测试的资源列表及其相应资源 ID，请参阅[支持 Azure AD 身份验证的 Azure 服务](msi-overview.md#azure-services-that-support-azure-ad-authentication)。

## <a name="error-handling-guidance"></a>有关错误处理的指导 

以下响应可能表示未正确配置 MSI：

- CLI：“MSI: 无法从 'http://localhost:50342/oauth2/token' 检索令牌，出现错误 'HTTPConnectionPool (主机='localhost'，端口=50342)” 

如果收到以下错误之一，请在 [Azure 门户](https://portal.azure.com)中返回到 Azure VM 并执行以下操作：

- 转到“配置”页，确保“托管服务标识”设置为“是”。
- 转到“扩展”页，确保已成功部署 MSI 扩展。

如果有任一项不正确，可能都需要在资源上再次重新分配 MSI，或排查部署故障。 如需 VM 配置方面的帮助，请参阅[使用 Azure CLI 配置 VM 托管服务标识 (MSI)](msi-qs-configure-cli-windows-vm.md)。

## <a name="next-steps"></a>后续步骤

- 若要在 Azure VM 上启用 MSI，请参阅[使用 Azure CLI 配置 VM 托管服务标识 (MSI)](msi-qs-configure-cli-windows-vm.md)。

使用以下评论部分提供反馈，帮助我们改进内容。








