---
title: 适用于 Azure 堆栈存储工具 |Microsoft 文档
description: 了解 Azure Stack 存储数据传送工具
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 3d9bd187a70e8b8292e9c47497c2c6b13764045d
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604720"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>用于 Azure 堆栈存储的数据传输工具

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Microsoft Azure 堆栈提供了一套用于磁盘、 blob、 表、 队列和帐户管理功能的存储服务。 如果你想要管理或将数据移到或从 Azure 堆栈存储，你可以使用 Azure 存储工具的一组。 本文概述了可用的工具。

你的需求决定了以下哪些工具最适合你：

* [AzCopy](#azcopy)

    一个特定于存储的命令行实用工具，下载后即可在存储帐户中将数据从一个对象复制到另一个对象，或者在存储帐户之间复制。

* [Azure PowerShell](#azure-powershell)

    一种基于任务的命令行 Shell 和脚本语言，专为系统管理而设计。

* [Azure CLI](#azure-cli)

    一种开源的跨平台工具，提供了一组适用于 Azure 和 Azure Stack 平台的命令。

* [Microsoft 存储资源管理器](#microsoft-azure-storage-explorer)

    一个易于使用的独立应用，带有用户界面。

由于 Azure 和 Azure Stack 之间具有存储服务差异，因此，以下部分中描述的每个工具可能存在一些特定的要求。 有关 Azure 堆栈存储和 Azure 存储之间的比较，请参阅[Azure 堆栈存储： 差异和注意事项](azure-stack-acs-differences.md)。

## <a name="azcopy"></a>AzCopy

AzCopy 是一个命令行实用工具，旨在将数据复制到和从 Microsoft Azure blob 和表存储使用简单的命令以最佳性能。 可在存储帐户中将数据从一个对象复制到另一个对象，或者在存储帐户之间复制。

### <a name="download-and-install-azcopy"></a>下载并安装 AzCopy

有两种版本的 AzCopy 实用工具：Windows 上的 AzCopy 和 Linux 上的 AzCopy。

 - **Windows 上的 AzCopy**
    - 下载 Azure Stack 支持的 AzCopy 版本。 可以采用与 Azure 一样的方式在 Azure Stack 上安装和使用 AzCopy。 若要了解详细信息，请参阅 [Windows 上的 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)。
        - 对于 1802 更新或更高版本，请[下载 AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417)。
        - 对于以前的版本，请[下载 AzCopy 5.0.0](https://aka.ms/azcopyforazurestack20170417)。

 - **Linux 上的 AzCopy**

    - Linux 上的 AzCopy 支持 Azure Stack 1802 更新或更高版本。 可以采用与 Azure 一样的方式在 Azure Stack 上安装和使用 AzCopy。 若要了解详细信息，请参阅 [Linux 上的 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux)。

### <a name="azcopy-command-examples-for-data-transfer"></a>针对数据传输的 AzCopy 命令示例

以下示例展示了将数据复制到 Azure Stack Blob 以及从这些位置复制数据的典型方案。 若要了解详细信息，请参阅 [Windows 上的 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) 和 [Linux 上的 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux)。

### <a name="download-all-blobs-to-a-local-disk"></a>将所有 Blob 下载到本地磁盘

**Windows**

````AzCopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
````

### <a name="upload-single-file-to-virtual-directory"></a>将单个文件上传到虚拟目录

**Windows**

```AzCopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

````AzCopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
````

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Azure 和 Azure 堆栈存储之间移动数据

Azure 存储空间和 Azure 堆栈之间的异步数据传输不支持。 需要使用 **/SyncCopy** 或 **--sync-copy** 选项来指定该传输。

**Windows**

````AzCopy
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
````

### <a name="azcopy-known-issues"></a>Azcopy 已知问题

 - 在文件存储上执行的任何 AzCopy 操作都不可用，因为文件存储在 Azure Stack 中不可用。
 - Azure 存储空间和 Azure 堆栈之间的异步数据传输不支持。 可以使用 **/SyncCopy** 选项来指定传输，以便复制数据。
 - Azcopy 的 Linux 版本仅支持 1802 更新或更高版本。 它不支持表服务。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 是一个模块，它提供的 cmdlet 用于管理 Azure 和 Azure Stack 上的服务。 这是一种基于任务的命令行 Shell 和脚本语言，专为系统管理而设计。

### <a name="install-and-configure-powershell-for-azure-stack"></a>安装和配置适用于 Azure Stack 的 PowerShell

需要安装与 Azure Stack 兼容的 Azure PowerShell 模块才能使用 Azure Stack。 有关详细信息，请参阅[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md) 和[配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)。

### <a name="powershell-sample-script-for-azure-stack"></a>适用于 Azure Stack 的 PowerShell 示例脚本 

此示例假定你已成功[安装了适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 此脚本会帮助你完成配置，然后要求你提供 Azure Stack 租户凭据，以便将你的帐户添加到本地 PowerShell 环境。 然后，该脚本会设置默认的 Azure 订阅、在 Azure 中创建新的存储帐户、在此新的存储帐户中创建新容器，并将现有图像文件 (Blob) 上传到该容器。 在脚本列出该容器中的所有 Blob 后，它会在本地计算机中创建新的目标目录，并下载图像文件。

1. 安装 [Azure Stack 兼容的 Azure PowerShell 模块](azure-stack-powershell-install.md)。
2. 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。
3. 打开 **Windows PowerShell ISE**，选择“以管理员身份运行”，然后单击“文件” > “新建”以创建新的脚本文件。
4. 复制下面的脚本并将其粘贴到新的脚本文件。
5. 根据配置设置更新脚本变量。
   > [!NOTE]
   > 此脚本必须在 **AzureStack_Tools** 的根目录中运行。

```PowerShell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
````

### <a name="powershell-known-issues"></a>PowerShell 已知问题

目前兼容 Azure Stack 的 Azure PowerShell 模块版本为 1.3.0。 它不同于最新版本的 Azure PowerShell。 这种差异影响存储服务操作：

* 在版本 1.3.0 中，`Get-AzureRmStorageAccountKey` 的返回值格式有两个属性：`Key1` 和 `Key2`，而当前的 Azure 版本返回的数组包含所有帐户密钥。

   ```
   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```

   有关详细信息，请参阅 [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是 Azure 的命令行体验，用于管理 Azure 资源。 可以将其安装在 macOS、Linux 和 Windows 上，然后从命令行运行。

Azure CLI 经过优化，可用于从命令行管理 Azure 资源，以及生成可以针对 Azure 资源管理器运行的自动化脚本。 它提供 Azure Stack 门户所提供的许多功能，包括各种数据访问功能。

Azure Stack 需要 Azure CLI 2.0 版。 若要详细了解如何通过 Azure Stack 来安装和配置 Azure CLI，请参阅[安装和配置 Azure Stack CLI](azure-stack-version-profiles-azurecli2.md)。 有关如何使用 Azure CLI 2.0 执行使用 Azure 堆栈存储帐户中的资源的多个任务的详细信息，请参阅[Azure CLI2.0 使用 Azure 存储空间](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>适用于 Azure Stack 的 Azure CLI 示例脚本

一旦你完成的 CLI 安装和配置，你可以尝试以下步骤以使用的小型壳示例脚本可与 Azure 堆栈存储资源进行交互。 此脚本完成以下操作：

* 在存储帐户中创建一个新容器。
* 将一个现有文件（作为 Blob）上传到该容器。
* 列出该容器中的所有 Blob。
* 将文件下载到本地计算机上的指定目标。

运行此脚本之前，请确保可以成功连接并登录到目标 Azure Stack。

1. 打开最喜欢的文本编辑器，然后将前面的脚本复制并粘贴到编辑器中。
2. 更新脚本的变量，使之反映配置设置。
3. 更新所需的变量后，保存脚本并退出编辑器。 后续步骤假定已将脚本命名为 **my_storage_sample.sh**。
4. 如有必要，将脚本标记为可执行文件：`chmod +x my_storage_sample.sh`
5. 执行脚本。 例如，在 Bash 中：`./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
````

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 存储资源管理器

Microsoft Azure 存储资源管理器是从 Microsoft 一个独立应用。 它可以轻松地使用 Azure 存储空间和 Azure 堆栈存储数据在 Windows、 macOS 和 Linux 计算机上。 如果你想管理你的 Azure 堆栈存储数据的简单办法，则可以考虑使用 Microsoft Azure 存储资源管理器。

* 若要了解有关配置 Azure 存储资源管理器用于 Azure 堆栈的详细信息，请参阅[连接到 Azure 堆栈订阅的存储资源管理器](azure-stack-storage-connect-se.md)。
* 若要了解有关 Microsoft Azure 存储资源管理器的详细信息，请参阅[开始使用存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>后续步骤

* [连接到 Azure 堆栈订阅的存储资源管理器](azure-stack-storage-connect-se.md)
* [要开始使用存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [与 Azure 一致的存储：差异和注意事项](azure-stack-acs-differences.md)
* [Microsoft Azure 存储空间简介](../../storage/common/storage-introduction.md)
