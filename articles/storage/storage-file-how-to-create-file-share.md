---
title: "如何创建 Azure 文件共享 | Microsoft Docs"
description: "如何使用 Azure 门户、PowerShell 和 Azure CLI 在 Azure 文件存储中创建 Azure 文件共享。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: bfe12fbdd50e82404ff49b1e34adc03913e50905
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---
# <a name="create-a-file-share-in-azure-file-storage"></a>在 Azure 文件存储中创建文件共享
可以使用 [Azure 门户](https://portal.azure.com/)、Azure 存储 PowerShell cmdlet、Azure 存储客户端库或 Azure 存储 REST API 来创建 Azure 文件共享。本教程介绍：
* [如何使用 Azure 门户创建 Azure 文件共享](#Create file share through the Portal)
* [如何使用 Powershell 创建 Azure 文件共享](#Create file share using PowerShell)
* [如何使用 CLI 创建 Azure 文件共享](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>先决条件
若要创建 Azure 文件共享，可以使用已存在的存储帐户，也可以[创建新的 Azure 存储帐户](storage-create-storage-account.md)。 若要使用 PowerShell 创建 Azure 文件共享，需提供存储帐户的帐户密钥和名称。 如果计划使用 Powershell 或 CLI，则需存储帐户密钥。

## <a name="create-file-share-through-the-portal"></a>通过门户创建文件共享
1. 转到 Azure 门户中的“存储帐户”边栏选项卡：    
    ![“存储帐户”边栏选项卡](media/storage-file-how-to-create-file-share/create-file-share-portal1.png)

2. 单击”添加文件共享”按钮：    
    ![单击”添加文件共享”按钮](media/storage-file-how-to-create-file-share/create-file-share-portal2.png)

3. 提供名称和配额。配额当前最大可以为 5TB：    
    ![为新的文件共享提供名称和所需配额](media/storage-file-how-to-create-file-share/create-file-share-portal3.png)

4. 查看新的文件共享：![查看新的文件共享](media/storage-file-how-to-create-file-share/create-file-share-portal4.png)

5. 上传文件：![上传文件](media/storage-file-how-to-create-file-share/create-file-share-portal5.png)

6. 浏览到文件共享并管理目录和文件：![浏览文件共享](media/storage-file-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>通过 PowerShell 创建文件共享
若要准备使用 PowerShell，请下载并安装 Azure PowerShell cmdlet。 有关安装点和安装说明，请参阅 [如何安装和配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) 。

> [!Note]  
> 建议你下载并安装最新的 Azure PowerShell 模块或升级到最新模块。

1. 为存储帐户和密钥创建上下文：上下文封装存储帐户名称和帐户密钥。 有关从 [Azure 门户](https://portal.azure.com/)复制帐户密钥的说明，请参阅[查看和复制存储访问密钥](storage-create-storage-account.md#view-and-copy-storage-access-keys)。

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. 创建新的文件共享：    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> 文件共享的名称必须是全部小写。 有关命名文件共享和文件的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

## <a name="create-file-share-through-command-line-interface-cli"></a>通过命令行界面 (CLI) 创建文件共享
1. 若要准备使用命令行界面 (CLI)，请下载并安装 Azure CLI。  
    请参阅[安装 Azure CLI 2.0](/cli/azure/install-az-cli2.md) 和 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli.md)。

2. 创建可连接到存储帐户的连接字符串，你需要在该帐户中创建共享。  
    请将下面示例中的 ```<storage-account>``` 和 ```<resource_group>``` 替换为存储帐户名称和资源组。

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. 创建文件共享
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>后续步骤
* [连接并装载文件共享 - Windows](storage-file-how-to-use-files-windows.md)
* [连接并装载文件共享 - Linux](storage-how-to-use-files-linux.md)
* [连接并装载文件共享 - macOS](storage-file-how-to-use-files-mac.md)

请参阅以下链接以获取有关 Azure 文件存储的更多信息。

* [常见问题](storage-files-faq.md)
* [故障排除](storage-troubleshoot-file-connection-problems.md)
