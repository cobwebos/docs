---
title: 将 Azure CLI 用于 Azure Data Lake Storage Gen2 中的文件和 ACL
description: 使用 Azure CLI 管理具有分层命名空间的存储帐户中的目录、文件和目录访问控制列表 (ACL)。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 8fdcad18ccec2748761cf35f2cd0b8efe9749958
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84466130"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 Azure CLI 管理 Azure Data Lake Storage Gen2 中的目录、文件和 ACL

本文介绍如何使用 [Azure 命令行接口 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 在具有分层命名空间的存储帐户中创建和管理目录、文件和权限。 

[Gen1 到 Gen2 的映射](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [提供反馈](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 已启用分层命名空间 (HNS) 的存储帐户。 请按照[此处](data-lake-storage-quickstart-create-account.md)的说明创建一个存储帐户。
> * Azure CLI 版本 `2.6.0` 或更高版本。

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>确保安装正确版本的 Azure CLI

1. 打开 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或者，如果已在本地[安装](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 使用以下命令验证安装的 Azure CLI 版本是否是 `2.6.0` 或更高版本。

   ```azurecli
    az --version
   ```
   如果 Azure CLI 版本低于 `2.6.0`，则安装更高版本。 请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="connect-to-the-account"></a>连接到帐户

1. 如果在本地使用 Azure CLI，请运行 login 命令。

   ```azurecli
   az login
   ```

   如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

   否则，请在浏览器中打开 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，然后输入终端中显示的授权代码。 然后，在浏览器中使用帐户凭据登录。

   若要详细了解不同的身份验证方法，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](../common/authorize-data-operations-cli.md)。

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

> [!NOTE]
> 本文中提供的示例演示 Azure Active Directory (AD) 授权。 若要详细了解身份验证方法，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](../common/authorize-data-operations-cli.md)。

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 可以使用 `az storage fs create` 命令创建文件系统。 

下面的示例创建名为 `my-file-system` 的文件系统。

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>显示文件系统属性

可以使用 `az storage fs show` 命令将文件系统属性打印到控制台。

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>列出文件系统内容

使用 `az storage fs file list` 命令列出目录内容。

下面的示例列出了名为 `my-file-system` 的文件系统的内容。

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>删除文件系统

使用 `az storage fs delete` 命令删除文件系统。

下面的示例删除名为 `my-file-system` 的文件系统。 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>创建目录

使用 `az storage fs directory create` 命令创建目录引用。 

下面的示例将名为 `my-directory` 的目录添加到名为 `my-file-system` 的文件系统，该文件系统位于 `mystorageaccount` 帐户中。

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>显示目录属性

可以使用 `az storage fs directory show` 命令将目录属性打印到控制台。

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

使用 `az storage fs directory move` 命令重命名或移动目录。

下面的示例在同一文件系统中将目录名称 `my-directory` 重命名为名称 `my-new-directory`。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

下面的示例将目录移到名为 `my-second-file-system` 的文件系统。

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>删除目录

使用 `az storage fs directory delete` 命令删除目录。

下面的示例删除名为 `my-directory` 的目录。 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>检查目录是否存在

使用 `az storage fs directory exists` 命令确定文件系统中是否存在特定目录。

下面的示例展示 `my-file-system` 文件系统中是否存在名为 `my-directory` 的目录。 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>从目录下载

使用 `az storage fs file download` 命令从目录下载文件。

下面的示例从名为 `my-directory` 的目录中下载名为 `upload.txt` 的文件。 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>列出目录内容

使用 `az storage fs file list` 命令列出目录内容。

下面的示例列出名为 `my-directory` 的目录的内容，该目录位于名为 `mystorageaccount` 的存储帐户的 `my-file-system` 文件系统中。 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

使用 `az storage fs directory upload` 命令将文件上传到目录。

下面的示例将名为 `upload.txt` 的文件上传到名为 `my-directory` 的目录。 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>显示文件属性

可以使用 `az storage fs file show` 命令将文件属性打印到控制台。

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>重命名或移动文件

使用 `az storage fs file move` 命令重命名或移动文件。

下面的示例将文件从名称 `my-file.txt` 重命名为名称 `my-file-renamed.txt`。

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>删除文件

使用 `az storage fs file delete` 命令删除文件。

下面的示例删除名为 `my-file.txt` 的文件

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>管理权限

可以获取、设置和更新目录和文件的访问权限。

> [!NOTE]
> 如果使用 Azure Active Directory (Azure AD) 来授权命令，请确保已为安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改这些权限的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

### <a name="get-an-acl"></a>获取 ACL

使用 `az storage fs access show` 命令获取目录的 ACL。

下面的示例获取目录的 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用 `az storage fs access show` 命令获取文件的访问权限。 

下面的示例获取文件的 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

下图显示了获取目录 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

在本示例中，负责人用户具有读取、写入和执行权限。 负责人组仅具有读取和执行权限。 有关访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="set-an-acl"></a>设置 ACL

使用 `az storage fs access set` 命令设置目录的 ACL。 

下面的示例在目录中为负责人用户、负责人组或其他用户设置 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

下面的示例在目录中为负责人用户、负责人组或其他用户设置默认 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

使用 `az storage fs access set` 命令设置文件的 ACL。 

下面的示例在文件中为负责人用户、负责人组或其他用户设置 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

下图显示了设置文件 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

在本示例中，负责人用户和负责人组只有读取和写入权限。 所有其他用户都具有写入和执行权限。 有关访问控制列表的详细信息，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="update-an-acl"></a>更新 ACL

设置此权限的另一种方法是使用 `az storage fs access set` 命令。 

通过将 `-permissions` 参数设置为 ACL 的简短形式，来更新目录或文件的 ACL。

下面的示例更新目录的 ACL。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

下面的示例更新文件的 ACL。

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

另外，还可以通过将 `--owner` 或 `group` 参数设置为用户的实体 ID 或用户主体名称 (UPN)，更新目录或文件的负责人用户和组。 

下面的示例更改目录所有者。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

下面的示例更改文件所有者。 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>另请参阅

* [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [提供反馈](https://github.com/Azure/azure-cli-extensions/issues)
* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


