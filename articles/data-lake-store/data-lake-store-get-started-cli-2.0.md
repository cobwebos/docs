---
title: 管理 Azure Data Lake Storage Gen1 帐户-Azure CLI
description: 使用 Azure CLI 创建 Data Lake Storage Gen1 帐户并执行基本操作。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 4e278981ce7647a53d2e80c5b835c8ed666db541
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82688174"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>使用 Azure CLI 开始使用 Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [门户](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

了解如何使用 Azure CLI 创建 Azure Data Lake Storage Gen1 帐户并执行基本操作，如创建文件夹、上传和下载数据文件、删除帐户等。有关 Data Lake Storage Gen1 的详细信息，请参阅[Data Lake Storage Gen1 概述](data-lake-store-overview.md)。

Azure CLI 是 Azure 的命令行体验，用于管理 Azure 资源。 它可以在 macOS、Linux 和 Windows 上使用。 有关详细信息，请参阅 [Azure CLI 概述](https://docs.microsoft.com/cli/azure)。 也可查看 [Azure Data Lake Storage Gen1 CLI 参考](https://docs.microsoft.com/cli/azure/dls)，获取命令和语法的完整列表。


## <a name="prerequisites"></a>先决条件
在开始阅读本文前，必须具有：

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure CLI**：请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 以获取说明。

## <a name="authentication"></a>身份验证

本文对 Data Lake Storage Gen1 使用一种较为简单的身份验证方法，你可以在其中以最终用户的身份登录。 系统会根据登录用户的访问级别约束对 Data Lake Storage Gen1 帐户和文件系统的访问权限。 不过，也可以使用其他方法在 Data Lake Storage Gen1 中进行身份验证，即“最终用户身份验证”或“服务到服务身份验证”********。 有关如何进行身份验证的说明和详细信息，请参阅[最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)或[服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)。


## <a name="log-in-to-your-azure-subscription"></a>登录到 Azure 订阅

1. 登录到 Azure 订阅。

    ```azurecli
    az login
    ```

    将获得要在下一步骤中使用的代码。 使用 Web 浏览器打开页面 https://aka.ms/devicelogin，然后输入该代码进行身份验证。 系统会提示使用凭据登录。

2. 登录后，窗口会列出与帐户相关联的所有 Azure 订阅。 通过以下命令来使用特定订阅。
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>创建 Azure Data Lake Storage Gen1 帐户

1. 创建新的资源组。 在以下命令中，提供要使用的参数值。 如果位置名称包含空格，请将其用引号引起来。 例如“美国东部 2”。 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. 创建 Data Lake Storage Gen1 帐户。
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>在 Data Lake Storage Gen1 帐户中创建文件夹

可以在 Azure Data Lake Storage Gen1 帐户下创建文件夹，用于管理和存储数据。 使用以下命令，在 Data Lake Storage Gen1 的根目录下创建名为 **mynewfolder** 的文件夹。

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> `--folder` 参数可确保该命令创建一个文件夹。 如果此参数不存在，则命令会在 Data Lake Storage Gen1 帐户的根目录下创建名为 mynewfolder 的空文件。
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>将数据上传到 Data Lake Storage Gen1 帐户

可以直接将数据上载到 Data Lake Storage Gen1 的根级别，也可以上载到在帐户中创建的文件夹。 下面的代码片段演示了如何将一些示例数据上传到上一节中创建的文件夹 (**mynewfolder**)。

如果正在查找一些示例数据进行上传，可以从 **Azure Data Lake Git 存储库** 获取 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)文件夹。 下载文件，并将其存储在计算机的本地目录中，如 C:\sampledata\。

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> 对于目标，必须指定完整的路径（包括文件名）。
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>列出 Data Lake Storage Gen1 帐户中的文件

使用以下命令列出 Data Lake Storage Gen1 帐户中的文件。

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

命令的输出如下所示：

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>重命名、下载以及删除 Data Lake Storage Gen1 帐户中的数据 

* **若要重命名文件**，请使用以下命令：
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* 使用以下命令，**下载文件**。 请确保指定的目标路径已存在。
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > 如果目标文件夹不存在，该命令会创建目标文件夹。
    > 
    >

* **若要删除文件**，请使用以下命令：
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    要在一个命令中将文件夹 **mynewfolder** 和文件 **vehicle1_09142014_copy.csv** 一起删除，请使用 --recurse 参数

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>使用 Data Lake Storage Gen1 帐户的权限和 ACL

本部分介绍如何使用 Azure CLI 管理 ACL 和权限。 有关如何在 Azure Data Lake Storage Gen1 中实现 ACL 的详细讨论，请参阅 [Data Lake Storage Gen1 中的访问控制](data-lake-store-access-control.md)。

* **若要更新文件/文件夹的所有者**，请使用以下命令：

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **若要更新文件/文件夹的权限**，请使用以下命令：

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **若要获取给定路径的 ACL**，请使用以下命令：

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    输出应与以下内容类似：

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **若要设置 ACL 的条目**，请使用以下命令：

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **若要删除 ACL 的条目**，请使用以下命令：

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **若要删除整个默认的 ACL**，请使用以下命令：

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **若要删除整个非默认的 ACL**，请使用以下命令：

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>删除 Data Lake Storage Gen1 帐户
使用以下命令删除 Data Lake Storage Gen1 帐户。

```azurecli
az dls account delete --account mydatalakestoragegen1
```

出现提示时，输入 **Y** 删除帐户。

## <a name="next-steps"></a>后续步骤
* [使用 Azure Data Lake Storage Gen1 满足大数据要求](data-lake-store-data-scenarios.md) 
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
* [将 Azure Data Lake Analytics 与 Data Lake Storage Gen1 配合使用](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)
