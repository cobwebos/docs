---
title: 在 Azure Data Lake Storage Gen2 （预览版）中使用 & Acl 的文件 Azure CLI
description: 使用 Azure CLI 管理具有分层命名空间的存储帐户中的目录和文件和目录访问控制列表（ACL）。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 95bb43f1531b6234ccb90eb7d66404ccc66f60f3
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485141"
---
# <a name="use-azure-cli-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>在 Azure Data Lake Storage Gen2 （预览版）中使用 & Acl 的文件 Azure CLI

本文介绍如何使用[Azure 命令行接口（CLI）](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)在具有分层命名空间的存储帐户中创建和管理目录、文件和权限。 

> [!IMPORTANT]
> 本文中所述的 `storage-preview` 扩展目前为公共预览版。

 | [Gen1 到 Gen2 映射的](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)[示例](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [提供反馈](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 已启用分层命名空间（HNS）的存储帐户。 按[这些](data-lake-storage-quickstart-create-account.md)说明创建一个。
> * Azure CLI 版本 `2.0.67` 或更高版本。

## <a name="install-the-storage-cli-extension"></a>安装存储 CLI 扩展

1. 打开[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)，或者如果已在本地[安装](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)了 Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 使用以下命令验证安装的 Azure CLI 版本是否 `2.0.67` 或更高版本。

   ```azurecli
    az --version
   ```
   如果你的 Azure CLI 版本低于 `2.0.67`，则安装更高版本。 请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

3. 安装 `storage-preview` 扩展。

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>连接到帐户

1. 如果要在本地使用 Azure CLI，请运行 login 命令。

   ```azurecli
   az login
   ```

   如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

   否则，请打开[https://aka.ms/devicelogin](https://aka.ms/devicelogin)浏览器页面，并输入终端中显示的授权代码。 然后，在浏览器中用帐户凭据登录。

   若要详细了解不同的身份验证方法，请参阅登录 Azure CLI。

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅的 ID。

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 您可以使用 `az storage container create` 命令创建一个。 

此示例将创建一个名为 `my-file-system`的文件系统。

```azurecli
az storage container create --name my-file-system
```

## <a name="create-a-directory"></a>创建目录

使用 `az storage blob directory create` 命令创建目录引用。 

此示例将名为 `my-directory` 的目录添加到名为 `my-file-system` 的名为 `mystorageaccount`的帐户中的文件系统。

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>显示目录属性

您可以使用 `az storage blob show` 命令将目录的属性打印到控制台。

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

使用 `az storage blob directory move` 命令重命名或移动目录。

此示例将目录从名称 `my-directory` 重命名为 `my-new-directory`的名称。

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>删除目录

使用 `az storage blob directory delete` 命令删除目录。

此示例删除名为 `my-directory`的目录。 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>检查目录是否存在

使用 `az storage blob directory exist` 命令确定文件系统中是否存在特定的目录。

此示例显示 `my-file-system` 文件系统中是否存在名为 `my-directory` 的目录。 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>从目录下载

使用 `az storage blob directory download` 命令从目录下载文件。

此示例从名为 `my-directory`的目录中下载名为 `upload.txt` 的文件。 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

此示例将下载整个目录。

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>列出目录内容

使用 `az storage blob directory list` 命令列出目录的内容。

此示例列出名为 `my-directory` 的目录的内容，该目录位于名为 `mystorageaccount`的存储帐户的 `my-file-system` 文件系统中。 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

使用 `az storage blob directory upload` 命令将文件上传到目录。

此示例将名为 `upload.txt` 的文件上传到名为 `my-directory`的目录。 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

此示例上传整个目录。

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>显示文件属性

您可以使用 `az storage blob show` 命令将文件的属性打印到控制台。

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>重命名或移动文件

使用 `az storage blob move` 命令重命名或移动文件。

此示例将名为的文件从名称 `my-file.txt` 重命名 `my-file-renamed.txt`。

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>删除文件

使用 `az storage blob delete` 命令删除文件。

此示例删除名为 `my-file.txt` 的文件

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>管理权限

你可以获取、设置和更新目录和文件的访问权限。

### <a name="get-directory-and-file-permissions"></a>获取目录和文件权限

使用 `az storage blob directory access show` 命令获取**目录**的 ACL。

此示例获取目录的 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

使用 `az storage blob access show` 命令获取**文件**的访问权限。 

此示例获取文件的 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

下图显示了获取目录的 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

在此示例中，拥有用户具有读取、写入和执行权限。 拥有组仅具有 "读取" 和 "执行" 权限。 有关访问控制列表的详细信息，请参阅[Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="set-directory-and-file-permissions"></a>设置目录和文件权限

使用 `az storage blob directory access set` 命令设置**目录**的 ACL。 

此示例在目录中为所属用户、拥有组或其他用户设置 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

使用 `az storage blob access set` 命令设置**文件**的 acl。 

此示例在文件中为所属用户、拥有组或其他用户设置 ACL，然后将 ACL 打印到控制台。

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
下图显示了设置文件的 ACL 后的输出。

![获取 ACL 输出](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

在此示例中，拥有用户和拥有组只有读取和写入权限。 所有其他用户都具有 "写入" 和 "执行" 权限。 有关访问控制列表的详细信息，请参阅[Azure Data Lake Storage Gen2 中的访问控制](data-lake-storage-access-control.md)。

### <a name="update-directory-and-file-permissions"></a>更新目录和文件权限

设置此权限的另一种方法是使用 `az storage blob directory access update` 或 `az storage blob access update` 命令。 

通过将 `-permissions` 参数设置为 ACL 的简短形式，更新目录或文件的 ACL。

此示例将更新**目录**的 ACL。

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

此示例更新**文件**的 ACL。

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

还可以通过将 `--owner` 或 `group` 参数设置为用户的实体 ID 或用户主体名称（UPN），来更新目录或文件的拥有用户和组。 

此示例将更改目录的所有者。 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

此示例更改文件的所有者。 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>管理用户定义的元数据

您可以通过使用包含一个或多个名称-值对的 `az storage blob directory metadata update` 命令，将用户定义的元数据添加到文件或目录。

此示例将用户定义的元数据添加到名为 `my-directory` directory 的目录中。

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

此示例显示名为 `my-directory`目录的所有用户定义元数据。

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>请参阅

* [示例](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [提供反馈](https://github.com/Azure/azure-cli-extensions/issues)
* [已知功能缺口](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [源代码](https://github.com/Azure/azure-cli-extensions/tree/master/src)

