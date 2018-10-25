---
title: 在 Azure AD 标识下运行 Azure CLI 或 PowerShell 命令以访问 Azure 存储（预览版）| Microsoft Docs
description: Azure CLI 和 PowerShell 支持使用 Azure AD 标识登录，以便对 Azure 存储容器和队列及其数据运行命令。 针对该会话提供了一个访问令牌，该访问令牌用于授权调用操作。 权限取决于分配给 Azure AD 标识的角色。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/20/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 42422580acec0811b0eb65d6fe1c0bed791eb9bd
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344523"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>使用 Azure AD 标识通过 CLI 或 PowerShell 访问 Azure 存储（预览版）

Azure 存储为 Azure CLI 和 PowerShell 提供预览扩展，使用户可登录并在 Azure Active Directory (Azure AD) 标识下运行脚本命令。 Azure AD 标识可以是用户、组或应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 可通过基于角色的访问控制 (RBAC) 向 Azure AD 标识分配访问存储资源的权限。 有关 Azure 存储中 RBAC 角色的详细信息，请参阅[通过 RBAC 管理 Azure 存储数据访问权限（预览）](storage-auth-aad-rbac.md)。

使用 Azure AD 标识登录 Azure CLI 或 PowerShell 时，会返回一个用于访问该标识下 Azure 存储的访问令牌。 CLI 或 PowerShell 之后自动使用该令牌针对 Azure 存储进行操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

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

Azure PowerShell 仅支持通过以下预览模块之一使用 Azure AD 标识登录： 

- 4.4.0-preview 
- 4.4.1-preview 

使用 Azure PowerShell 通过 Azure AD 标识登录：

1. 卸载以前安装的所有 Azure PowerShell：

    - 使用“设置”下的“应用和功能”设置从 Windows 中删除以前安装的所有 Azure PowerShell。
    - 从 `%Program Files%\WindowsPowerShell\Modules` 中删除所有 **Azure*** 模块。

1. 确保已安装 PowerShellGet 最新版本。 打开 Windows PowerShell 窗口，然后运行以下命令以安装最新版本：
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. 安装 PowerShellGet 后关闭并重新打开 PowerShell 窗口。 

1. 安装最新版本的 Azure PowerShell：

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

1. 安装一个支持 Azure AD 的 Azure 存储预览模块：

    ```powershell
    Install-Module Azure.Storage –Repository PSGallery -RequiredVersion 4.4.1-preview  –AllowPrerelease –AllowClobber –Force 
    ```
1. 关闭并重新打开 PowerShell 窗口。
1. 调用 [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) cmdlet 以创建上下文，并添加 `-UseConnectedAccount` 参数。 
1. 若要使用 Azure AD 标识调用 cmdlet，请将新创建的上下文传递给 cmdlet。

以下示例演示如何使用 Azure AD 标识通过 Azure PowerShell 列出容器中的 blob。 请务必将占位符帐户名称和容器名称替换为自己的值： 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName storagesamples -UseConnectedAccount 
Get-AzureStorageBlob -Container sample-container -Context $ctx 
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[通过 RBAC 管理存储数据访问权限（预览）](storage-auth-aad-rbac.md)。
- 若要了解如何将 Azure 资源的托管标识与 Azure 存储一起使用，请参阅[使用 Azure 资源的 Azure 托管标识（预览）验证对 Blob 和队列的访问权限](storage-auth-aad-msi.md)。
- 若要了解如何从存储应用程序内授予容器和队列访问权限，请参阅[将 Azure AD 与存储应用程序配合使用](storage-auth-aad-app.md)。
- 有关适用于 Azure Blob 和队列的 Azure AD 集成的详细信息，请参阅 Azure 存储团队博客文章[适用于 Azure 存储的 Azure AD 身份验证预览版发布公告](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)。
