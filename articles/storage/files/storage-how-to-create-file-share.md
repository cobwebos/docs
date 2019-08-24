---
title: 如何创建 Azure 文件共享 | Microsoft Docs
description: 如何使用 Azure 门户、PowerShell 和 Azure CLI 在 Azure 文件中创建 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 000dacb7530b52784a68663d295fde9784d50e29
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013563"
---
# <a name="create-a-file-share-in-azure-files"></a>在 Azure 文件中创建文件共享
可以使用  [Azure 门户](https://portal.azure.com/)、Azure 存储 PowerShell cmdlet、Azure 存储客户端库或 Azure 存储 REST API 来创建 Azure 文件共享。 本教程介绍：
* 如何使用 Azure 门户创建 Azure 文件共享
* [如何使用 Powershell 创建 Azure 文件共享](#create-file-share-through-powershell)
* [如何使用 CLI 创建 Azure 文件共享](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>先决条件
若要创建 Azure 文件共享，可以使用已存在的存储帐户，也可以[创建新的 Azure 存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 若要使用 PowerShell 创建 Azure 文件共享，需提供存储帐户的帐户密钥和名称。 如果计划使用 Powershell 或 CLI，则需存储帐户密钥。

## <a name="create-a-file-share-through-the-azure-portal"></a>通过 Azure 门户创建文件共享
1. **转到 Azure 门户中的“存储帐户”边栏选项卡**：    
    ![“存储帐户”边栏选项卡](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. 单击”添加文件共享”按钮：    
    ![单击”添加文件共享”按钮](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **提供名称和配额。配额目前的最大值为 5 TiB**：    
    ![为新的文件共享提供名称和所需配额](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **查看新的文件共享**：![查看新的文件共享](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **上传文件**：![上传文件](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **浏览到文件共享并管理目录和文件**：![浏览文件共享](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>通过 PowerShell 创建文件共享
若要准备使用 PowerShell，请下载并安装 Azure PowerShell cmdlet。 有关安装点和安装说明，请参阅 [如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。

> [!Note]  
> 建议下载并安装最新的 Azure PowerShell 模块或升级到最新模块。

1. **创建新的存储帐户:** 存储帐户是一个存储共享池, 你可以在其中部署 Azure 文件共享和其他存储资源, 例如 blob 或队列。

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

2. 创建新的文件共享：    
    
    ```powershell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> 文件共享的名称必须是全部小写。 若要全面且详细地了解如何为文件共享和文件命名，请参阅 [命名和引用共享、目录、文件和元数据](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

## <a name="create-file-share-through-command-line-interface-cli"></a>通过命令行界面 (CLI) 创建文件共享
1. **若要准备使用命令行界面 (CLI)，请下载并安装 Azure CLI。**  
    请参阅 [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 和 [Azure CLI 入门](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)。

2. 创建可连接到存储帐户的连接字符串，你需要在该帐户中创建共享。  
    请将以下示例中的  ```<storage-account>``` 和 ```<resource_group>```  替换为自己的存储帐户名称和资源组：

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **创建文件共享**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>后续步骤
* [连接并装载文件共享 - Windows](storage-how-to-use-files-windows.md)
* [连接并装载文件共享 - Linux](../storage-how-to-use-files-linux.md)
* [连接并装载文件共享 - macOS](storage-how-to-use-files-mac.md)

请参阅以下链接，获取有关 Azure 文件的更多信息。

* [常见问题](../storage-files-faq.md)
* [在 Windows 上进行故障排除](storage-troubleshoot-windows-file-connection-problems.md)      
* [在 Linux 上进行故障排除](storage-troubleshoot-linux-file-connection-problems.md)   
