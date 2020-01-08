---
title: 创建 Azure 文件共享
titleSuffix: Azure Files
description: 如何使用 Azure 门户、PowerShell 或 Azure CLI 创建 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452874"
---
# <a name="create-a-file-share"></a>创建文件共享
可以使用 [Azure 门户](https://portal.azure.com/)、Azure 存储 PowerShell Cmdlet、azure 存储客户端库或 azure 存储 REST API 在 azure 文件中创建共享。 本文将指导如何进行以下操作：
- 使用 Azure 门户创建 Azure 文件共享
- 使用 PowerShell 创建 Azure 文件共享
- 使用 Azure CLI 创建 Azure 文件共享

## <a name="prerequisites"></a>必备组件
若要创建 Azure 文件共享，可以使用已存在的存储帐户，也可以[创建新的 azure 存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 若要使用 PowerShell 创建 Azure 文件共享，需提供存储帐户的帐户密钥和名称。 如果计划使用 PowerShell 或 CLI，则需要一个存储帐户密钥。

> [!NOTE]
> 如果要创建超过 5 TiB 的文件共享，请参阅[启用大型文件共享](storage-files-how-to-create-large-file-share.md)。

## <a name="create-a-file-share-through-the-azure-portal"></a>通过 Azure 门户创建文件共享

1. 中转到 Azure 门户上的 "**存储帐户**" 窗格。
    ![存储帐户 "窗格](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. 选择 " **+ 文件共享**" 按钮。
    !["添加文件共享" 按钮](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. 输入您的**姓名**和**配额**信息。
    ![新文件共享的名称和配额](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. 查看新的文件共享。
    在 UI 中 ![新文件共享](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. 上传文件。
    ![导航栏中的 "上传" 按钮](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. 浏览到文件共享并管理目录和文件。
    ![文件共享的文件夹视图](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>通过 PowerShell 创建文件共享

下载并安装 Azure PowerShell cmdlet。 有关安装点和安装说明，请参阅 [如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。

> [!Note]  
> 建议你下载并安装或升级到最新的 Azure PowerShell 模块。

1. 新建存储帐户。
    存储帐户是一个存储共享池，你可以在其中部署 Azure 文件共享和存储资源，例如 blob 或队列。

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. 创建新的文件共享。        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> 文件共享的名称必须是全部小写。 有关命名文件共享和文件的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

## <a name="create-a-file-share-through-the-cli"></a>通过 CLI 创建文件共享

1. 下载并安装 Azure CLI。
    请参阅 [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)和[开始 Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)。

1. 创建要在其中创建共享的存储帐户的连接字符串。
    请将以下示例中的  ```<storage-account>``` 和 ```<resource_group>```  替换为自己的存储帐户名称和资源组：

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. 创建文件共享。
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>后续步骤

* [在 Windows 上连接并装载文件共享](storage-how-to-use-files-windows.md)
* [在 Linux 上连接并装载文件共享](storage-how-to-use-files-linux.md)
* [在 macOS 上连接并装载文件共享](storage-how-to-use-files-mac.md)

请参阅以下链接，获取有关 Azure 文件的更多信息：

* [存储文件常见问题](storage-files-faq.md)
* [排查 Windows 中的 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)
* [排查 Linux 中的 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)
