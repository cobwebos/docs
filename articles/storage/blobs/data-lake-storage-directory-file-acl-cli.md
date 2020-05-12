---
title: 将 Azure CLI 用于 & 中的 Acl Azure Data Lake Storage Gen2
description: 使用 Azure CLI 管理具有分层命名空间的存储帐户中的目录和文件和目录访问控制列表（ACL）。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 79a87f02c8730e0b2110e7475de721f11beda568
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120600"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Azure CLI 管理 Azure Data Lake Storage Gen2 中的目录、文件和 Acl

本文介绍如何使用[Azure 命令行接口（CLI）](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)在具有分层命名空间的存储帐户中创建和管理目录、文件和权限。 

[Gen1 到 Gen2 的映射](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)  | [提供反馈](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](data-lake-storage-quickstart-create-account.md)说明创建一个。
> * Azure CLI 版本 `2.5.1` 或更高版本。

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>确保已安装正确版本的 Azure CLI

1. 打开[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或者如果已在本地[安装](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)了 Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 使用以下命令验证安装的 Azure CLI 版本是否为 `2.5.1` 或更高版本。

   ```azurecli
    az --version
   ```
   如果你的 Azure CLI 版本低于 `2.5.1` ，则安装更高版本。 请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="connect-to-the-account"></a>连接到帐户

1. 如果要在本地使用 Azure CLI，请运行 login 命令。

   ```azurecli
   az login
   ```

   如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

   否则，请在处打开浏览器页面 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ，并输入终端中显示的授权代码。 然后，在浏览器中用帐户凭据登录。

   若要详细了解不同的身份验证方法，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](../common/authorize-data-operations-cli.md)。

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅的 ID。

> [!NOTE]
> 本文中提供的示例演示 Azure Active Directory （AD）授权。 若要了解有关授权方法的详细信息，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](../common/authorize-data-operations-cli.md)。

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 您可以使用命令创建一个 `az storage fs create` 。 

此示例创建名为 `my-file-system` 的文件系统。

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>显示文件系统属性

您可以使用命令将文件系统的属性打印到控制台 `az storage fs show` 。

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>列出文件系统内容

使用命令列出目录的内容 `az storage fs file list` 。

此示例列出了名为的文件系统的内容 `my-file-system` 。

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>删除文件系统

使用命令删除文件系统 `az storage fs delete` 。

此示例删除名为的文件系统 `my-file-system` 。 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>创建目录

使用命令创建目录引用 `az storage fs directory create` 。 

此示例将一个名为 `my-directory` 的目录添加到名为的文件系统 `my-file-system` 中，该文件位于名为的帐户中 `mystorageaccount` 。

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>显示目录属性

您可以使用命令将目录的属性打印到控制台 `az storage fs directory show` 。

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

使用命令重命名或移动目录 `az storage fs directory move` 。

此示例将目录从名称重命名 `my-directory` 为 `my-new-directory` 同一文件系统中的名称。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

此示例将目录移动到名为的文件系统 `my-second-file-system` 。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>删除目录

使用命令删除目录 `az storage fs directory delete` 。

此示例删除名为 `my-directory` 的目录。 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>检查目录是否存在

使用命令确定文件系统中是否存在特定的目录 `az storage fs directory exists` 。

此示例显示 `my-directory` 文件系统中是否存在名为的目录 `my-file-system` 。 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>从目录下载

使用命令从目录下载文件 `az storage fs file download` 。

此示例从名为 `my-directory` 的目录中下载名为 `upload.txt` 的文件。 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>列出目录内容

使用命令列出目录的内容 `az storage fs file list` 。

此示例列出名为的目录的内容 `my-directory` ，该目录位于 `my-file-system` 名为的存储帐户的文件系统中 `mystorageaccount` 。 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

使用命令将文件上传到目录 `az storage fs directory upload` 。

此示例将名为 `upload.txt` 的文件上传到名为 `my-directory` 的目录。 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>显示文件属性

您可以使用命令将文件的属性打印到控制台 `az storage fs file show` 。

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>重命名或移动文件

使用命令重命名或移动文件 `az storage fs file move` 。

此示例将文件从名称重命名 `my-file.txt` 为名称 `my-file-renamed.txt` 。

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>删除文件

使用命令删除文件 `az storage fs file delete` 。

此示例删除名为`my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>管理权限

可以获取、设置和更新目录与文件的访问权限。

> [!NOTE]
> 若要使用 Azure Active Directory (Azure AD) 为命令授权，请确保已为安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

### <a name="get-an-acl"></a>获取 ACL

使用命令获取**目录**的 ACL `az storage fs access show` 。

此示例获取某个目录的 ACL，然后将 ACL 输出到控制台。

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用命令获取**文件**的访问权限 `az storage fs access show` 。 

此示例获取某个文件的 ACL，然后将 ACL 输出到控制台。

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

下图显示了获取目录 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

在此示例中，所有者用户拥有读取、写入和执行权限。 所有者组仅拥有读取和执行权限。 有关这些访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="set-an-acl"></a>设置 ACL

使用 `az storage fs access set` 命令设置**目录**的 ACL。 

此示例针对所有者用户、所有者组或其他用户的目录设置 ACL，然后将 ACL 输出到控制台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

此示例在目录中为所属用户、拥有组或其他用户设置*默认*acl，然后将 ACL 打印到控制台。

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用 `az storage fs access set` 命令设置**文件**的 acl。 

此示例针对所有者用户、所有者组或其他用户的文件设置 ACL，然后将 ACL 输出到控制台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

下图显示了设置文件 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

在此示例中，所有者用户和所有者组只拥有读取和写入权限。 所有其他用户拥有写入和执行权限。 有关这些访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="update-an-acl"></a>更新 ACL

设置此权限的另一种方法是使用 `az storage fs access set` 命令。 

通过将 `-permissions` 参数设置为 acl 的简短形式，更新目录或文件的 ACL。

此示例将更新**目录**的 ACL。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

此示例更新**文件**的 ACL。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

还可以通过将 `--owner` 或 `group` 参数设置为用户的实体 ID 或用户主体名称（UPN），来更新目录或文件的拥有用户和组。 

此示例将更改目录的所有者。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

此示例更改文件的所有者。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>另请参阅

* [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [提供反馈](https://github.com/Azure/azure-cli-extensions/issues)
* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


