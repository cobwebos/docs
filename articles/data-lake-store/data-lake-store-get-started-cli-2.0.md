---
title: "通过 Azure 命令行 2.0 界面开始使用 Azure Data Lake Store | Microsoft Docs"
description: "使用 Azure 跨平台命令行 2.0 来创建 Data Lake Store 帐户以及执行基本操作"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 4ffa0f4a-1cca-46ac-803d-1fc8538c685b
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: c5971a137d9081be8c5978f481ec42a1f91e5a56
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli-20"></a>Azure Data Lake Store 入门（使用 Azure CLI 2.0）
> [!div class="op_single_selector"]
> * [门户](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

了解如何使用 Azure CLI 2.0 来创建 Azure Data Lake Store 帐户以及执行基本操作，如创建文件夹、上传和下载数据文件、删除帐户等。有关 Data Lake Store 的详细信息，请参阅 [Data Lake Store 概述](data-lake-store-overview.md)。

Azure CLI 2.0 是 Azure 的新命令行体验，用于管理 Azure 资源。 它可以在 macOS、Linux 和 Windows 上使用。 有关详细信息，请参阅 [Overview of Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview)（Azure CLI 2.0 概述）。 也可查看 [Azure Data Lake Store CLI 2.0 reference](https://docs.microsoft.com/cli/azure/dls)（Azure Data Lake Store CLI 2.0 参考），获取命令和语法的完整列表。


## <a name="prerequisites"></a>先决条件
在开始阅读本文前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure CLI 2.0** - 请参阅 [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（安装 Azure CLI 2.0）以获取说明。

## <a name="authentication"></a>身份验证

本文对 Data Lake Store 使用一种较为简单的身份验证方法，可以在其中以最终用户的身份登录。 系统会根据登录用户的访问级别监管对 Data Lake Store 帐户和文件系统的访问权限。 不过，也可以使用其他方法在 Data Lake Store 中进行身份验证，即**最终用户身份验证**或**服务到服务身份验证**。 有关如何进行身份验证的说明和详细信息，请参阅[最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)或[服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)。


## <a name="log-in-to-your-azure-subscription"></a>登录到 Azure 订阅

1. 登录到 Azure 订阅。

    ```azurecli
    az login
    ```

    你会获得可在下一步使用的代码。 使用 Web 浏览器打开页面 https://aka.ms/devicelogin，输入代码进行身份验证。 系统会提示你使用凭据登录。

2. 你登录后，窗口会列出与帐户相关联的所有 Azure 订阅。 通过以下命令来使用特定订阅。
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>创建 Azure Data Lake Store 帐户

1. 创建新的资源组。 在以下命令中，提供要使用的参数值。 如果位置名称包含空格，请将其用引号引起来。 例如“美国东部 2”。 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. 创建 Data Lake Store 帐户。
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>在 Data Lake Store 帐户中创建文件夹

可以在 Azure Data Lake Store 帐户下创建文件夹，用于管理和存储数据。 使用以下命令，在 Data Lake Store 的根目录下创建一个名为 **mynewfolder** 的文件夹。

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> `--folder` 参数可确保该命令创建一个文件夹。 如果该参数不存在，则命令会在 Data Lake Store 帐户的根目录处创建名为 mynewfolder 的空文件。
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>将数据上传到 Data Lake Store 帐户

可以直接将数据上传到 Data Lake Store 的根级别，也可以上传到在帐户中创建的文件夹。 下面的代码片段演示了如何将一些示例数据上传到上一节中创建的文件夹 (**mynewfolder**)。

如果正在查找一些示例数据进行上传，可以从 **Azure Data Lake Git 存储库** 获取 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)文件夹。 下载文件，并将其存储在计算机的本地目录中，如 C:\sampledata\。

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> 对于目标，必须指定完整的路径（包括文件名）。
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>列出 Data Lake Store 帐户中的文件

使用以下命令列出 Data Lake Store 帐户中的文件。

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
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

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>重命名、下载以及删除 Data Lake Store 帐户中的数据 

* 使用以下命令，**重命名文件**：
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* 使用以下命令，**下载文件**。 请确保指定的目标路径已存在。
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > 如果目标文件夹不存在，该命令会创建目标文件夹。
    > 
    >

* 使用以下命令，**删除文件**：
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    若要在一个命令中将文件夹 **mynewfolder** 和文件 **vehicle1_09142014_copy.csv** 一起删除，请使用 --recurse 参数

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>使用 Data Lake Store 帐户的权限和 ACL

本部分介绍如何使用 Azure CLI 2.0 管理 ACL 和权限。 有关如何在 Azure Data Lake Store 中实现 ACL 的详细讨论，请参阅 [Data Lake Store 中的访问控制](data-lake-store-access-control.md)。

* **若要更新文件/文件夹的所有者**，请使用以下命令：

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **若要更新文件/文件夹的权限**，请使用以下命令：

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **若要获取给定路径的 ACL**，请使用以下命令：

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    输出应如下所示：

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
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **若要删除 ACL 的条目**，请使用以下命令：

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **若要删除整个默认的 ACL**，请使用以下命令：

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **若要删除整个非默认的 ACL**，请使用以下命令：

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>删除 Data Lake Store 帐户
使用以下命令删除 Data Lake Store 帐户。

```azurecli
az dls account delete --account mydatalakestore
```

出现提示时，输入 **Y** 删除帐户。

## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Store CLI 2.0 参考](https://docs.microsoft.com/cli/azure/dls)
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md

