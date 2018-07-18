---
title: 在 Azure AD 标识下运行 Azure CLI 或 PowerShell 命令以访问 Azure 存储（预览版）| Microsoft Docs
description: Azure CLI 和 PowerShell 支持使用 Azure AD 标识登录，以便对 Azure 存储容器和队列及其数据运行命令。 针对该会话提供了一个访问令牌，该访问令牌用于授权调用操作。 权限取决于分配给 Azure AD 标识的角色。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.openlocfilehash: 98af46707485d1ab49e7d8c6fb1729e6edc6b2ff
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235859"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>使用 Azure AD 标识通过 CLI 或 PowerShell 访问 Azure 存储（预览版）

Azure 存储为 Azure CLI 和 PowerShell 提供预览扩展，使用户可登录并在 Azure Active Directory (Azure AD) 标识下运行脚本命令。 Azure AD 标识可以为用户、组或应用程序服务主体，或者可为[托管服务标识](../../active-directory/managed-service-identity/overview.md)。 可通过基于角色的访问控制 (RBAC) 向 Azure AD 标识分配访问存储资源的权限。 有关 Azure 存储中 RBAC 角色的详细信息，请参阅[通过 RBAC 管理 Azure 存储数据访问权限（预览）](storage-auth-aad-rbac.md)。

使用 Azure AD 标识登录 Azure CLI 或 PowerShell 时，会返回一个用于访问该标识下 Azure 存储的访问令牌。 CLI 或 PowerShell 之后自动使用该令牌针对 Azure 存储进行操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

> [!IMPORTANT]
> 此预览版仅用于非生产用途。 适用于 Azure 存储的 Azure AD 集成正式发布后，生产服务级别协议 (SLA) 方可使用。 如果你的方案尚不支持 Azure AD 集成，请继续使用应用程序中的共享密钥授权或 SAS 令牌。 有关该预览版的其他信息，请参阅[使用 Azure Active Directory进行 Azure 存储访问权限身份验证（预览版）](storage-auth-aad.md)。
>
> 预览期间，RBAC 角色分配可能需要最多五分钟的时间进行传播。
>
> Azure AD 与 Azure 存储集成要求用户使用 HTTPS 进行 Azure 存储操作。

## <a name="supported-operations"></a>支持的操作

预览扩展支持针对容器和队列的操作。 可调用的操作取决于向 Azure AD 标识授予的权限，此类权限用于登录 Azure CLI 或 PowerShell。 Azure 存储容器或队列的权限通过基于角色的访问控制 (RBAC) 进行分配。 例如，如果向该标识分配数据读取者角色，则可运行从容器或队列读取数据的脚本命令。 如果向该标识分配数据贡献者角色，则可运行脚本命令来读取、写入或删除容器、队列或其中所含数据。 

若要详细了解针对容器或队列的每个 Azure 存储操作所需的权限，请参阅[用于调用 REST 操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)。  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>使用 Azure AD 标识调用 CLI 命令

为 Azure CLI 安装预览扩展：

1. 确保已安装 Azure CLI 2.0.32 或更高版本。 运行 `az --version` 以查看已安装版本。
2. 运行如下命令以安装预览扩展： 

    ```azurecli
    az extension add -n storage-preview
    ```

预览扩展将一个新的 `--auth-mode` 参数添加到受支持的命令：

- 将 `--auth-mode` 参数设置为 `login`，以使用 Azure AD 标识登录。
- 将 `--auth-mode` 参数设置为旧的 `key` 值，以便在未提供帐户的身份验证参数时，查询帐户密钥。 

例如，若要使用 Azure AD 标识在 Azure CLI 中下载 blob，首先运行 `az login`，然后调用命令（其中 `--auth-mode` 设置为 `login`）：

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

与 `--auth-mode` 参数关联的环境变量为 `AZURE_STORAGE_AUTH_MODE`。

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>通过 Azure AD 标识调用 PowerShell 命令

使用 Azure PowerShell 通过 Azure AD 标识登录：

1. 确保已安装 PowerShellGet 最新版本。 运行以下命令安装最新版本：
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. 卸载以前的所有 Azure PowerShell 安装。
3. 安装 AzureRM：

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. 安装预览模块：

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. 调用 [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) cmdlet 以创建上下文，并添加 `-UseConnectedAccount` 参数。 
6. 若要通过 Azure AD 标识调用 cmdlet，将上下文传递到 cmdlet。

以下示例演示如何使用 Azure AD 标识从 Azure PowerShell 中列出容器中的 blob： 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[通过 RBAC 管理存储数据访问权限（预览）](storage-auth-aad-rbac.md)。
- 若要了解如何将托管服务标识用于 Azure 存储，请参阅[使用 Azure AD 通过 Azure 托管服务标识进行身份验证（预览）](storage-auth-aad-msi.md)。
- 若要了解如何从存储应用程序内向容器和队列授予访问权限，请参阅[将 Azure AD 用于存储应用程序](storage-auth-aad-app.md)。
- 有关适用于 Azure Blob 和队列的 Azure AD 集成的详细信息，请参阅 Azure 存储团队博客文章[适用于 Azure 存储的 Azure AD 身份验证预览版发布公告](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)。
