---
title: "适用于 Azure 堆栈存储工具"
description: "了解有关 Azure 堆栈存储数据传输工具"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 9799498a11449a9ed496d0fdb40312603eda064e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tools-for-azure-stack-storage"></a>适用于 Azure 堆栈存储工具

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Microsoft Azure 堆栈提供了一套用于磁盘、 blob、 表、 队列和帐户管理功能的存储服务。 如果你想要管理或将数据移到或从 Azure 堆栈存储，你可以使用 Azure 存储工具的一组。 本文提供了可用的工具的快速概述。

最适合您的工具取决于你的要求：
* [AzCopy](#azcopy)

    特定于存储的命令行实用工具，你可以下载的用于将数据从一个对象复制到另一个，在你的存储帐户，或存储帐户之间。

* [Azure PowerShell](#azure-powershell)

    一种基于任务的命令行 shell 和脚本语言，专门用于系统管理。

* [Azure CLI](#azure-cli)

    一种开放源代码、 跨平台的工具，用于使用 Azure 和 Azure 堆栈平台提供的一组命令。

* [Microsoft 存储资源管理器 （预览版）](#microsoft-azure-storage-explorer)

    包含用户界面的易于使用的独立应用。

由于 Azure 和 Azure 堆栈之间的存储服务不同，可能有一些特定的要求，为以下各节介绍每个工具。 有关 Azure 堆栈存储和 Azure 存储之间的比较，请参阅[Azure 堆栈存储： 差异和注意事项](azure-stack-acs-differences.md)。


## <a name="azcopy"></a>AzCopy
AzCopy 是一个命令行实用工具，旨在将数据复制到和从 Microsoft Azure Blob 和表存储使用简单的命令以最佳性能。 可在存储帐户中将数据从一个对象复制到另一个对象，或者在存储帐户之间复制。 有两个版本的 AzCopy： 在 Windows 和 Linux 上的 AzCopy 上的 AzCopy。 Azure 堆栈仅支持 Windows 版本。 
 
### <a name="download-and-install-azcopy"></a>下载并安装 AzCopy 
[下载](https://aka.ms/azcopyforazurestack)受支持的 Windows 版本的 Azure 堆栈的 AzCopy。 你可以上安装并使用 AzCopy Azure 堆栈与 Azure 相同的方式。 若要了解详细信息，请参阅[传输数据的 AzCopy 命令行实用工具](../../storage/common/storage-use-azcopy.md)。 

### <a name="azcopy-command-examples-for-data-transfer"></a>AzCopy 命令示例中的数据传输
下面的示例演示用于复制数据到和从 Azure 堆栈 blob 的一些典型方案。 若要了解详细信息，请参阅[传输数据的 AzCopy 命令行实用工具](../../storage/storage-use-azcopy.md)。 
#### <a name="download-all-blobs-to-local-disk"></a>所有 blob 都下载到本地磁盘
```azcopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```
#### <a name="upload-single-file-to-virtual-directory"></a>将单个文件上传到虚拟目录 
```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```
#### <a name="move-data-between-azure-and-azure-stack-storage"></a>Azure 和 Azure 堆栈存储之间移动数据 
不支持 Azure 存储空间和 Azure 堆栈之间的异步数据传输。 你需要指定与传输`/SyncCopy`选项。 
```azcopy 
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

### <a name="azcopy-known-issues"></a>Azcopy 已知问题
* 文件存储任何 AzCopy 操作不可用，因为文件存储尚不可用 Azure 堆栈中。
* 不支持 Azure 存储空间和 Azure 堆栈之间的异步数据传输。 你可以指定与传输`/SyncCopy`选项可将复制数据。
* Azure 堆栈存储不支持 Linux 版本的 Azcopy。 

## <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell 是一个模块，提供用于管理在 Azure 和 Azure 堆栈上的服务的 cmdlet。 这是一种基于任务的命令行外壳和脚本语言，专为系统管理而设计。

### <a name="install-and-configure-powershell-for-azure-stack"></a>安装和配置 PowerShell 以 Azure 堆栈
Azure 堆栈兼容 Azure PowerShell 模块需要使用 Azure 堆栈处理。 有关详细信息，请参阅[安装适用于 Azure 堆栈的 PowerShell](azure-stack-powershell-install.md)和[配置 Azure 堆栈用户 PowerShell 环境](azure-stack-powershell-configure-user.md)若要了解详细信息。

### <a name="powershell-sample-script-for-azure-stack"></a>用于 Azure 堆栈的 PowerShell 示例脚本 
此示例假定您已成功地[安装适用于 Azure 堆栈的 PowerShell](azure-stack-powershell-install.md)。 此脚本将帮助 conplete 配置和要求凭据以将你的帐户添加到本地 PowerShell 磁带的 Azure 堆栈租户。 然后，该脚本将设置默认 Azure 订阅，在 Azure 中创建新的存储帐户，此新的存储帐户中创建一个新容器并将现有图像文件 (blob) 上载到该容器。 在脚本列出该容器中的所有 Blob 后，它会在本地计算机中创建新的目标目录，并下载图像文件。

1. 安装[Azure 堆栈兼容 Azure PowerShell 模块](azure-stack-powershell-install.md)。  
2. 下载[工具需要使用处理 Azure 堆栈](azure-stack-powershell-download.md)。  
3. 打开**Windows PowerShell ISE**和**以管理员身份运行**，单击**文件** > **新建**创建新的脚本文件。
4. 复制下面的脚本并粘贴到新的脚本文件。
5. 更新脚本变量基于你的配置设置。 
6. 注意： 此脚本必须运行的根目录下下载**AzureStack_Tools**。 

```PowerShell 
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase!
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
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudiance

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Login-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

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
```

### <a name="powershell-known-issues"></a>PowerShell 已知问题 
Azure 堆栈的当前兼容 Azure PowerShell 模块版本是 1.2.10。 它是不同的最新版本的 Azure PowerShell。 这种差异会影响存储服务操作：

* 返回值格式`Get-AzureRmStorageAccountKey`1.2.10 版本中具有两个属性：`Key1`和`Key2`，而当前的 Azure 版本返回数组，其中包含所有的帐户密钥。
   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```
   有关详细信息，请参阅[Get-azurermstorageaccountkey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0)。

## <a name="azure-cli"></a>Azure CLI
Azure CLI 是用于管理 Azure 资源的 Azure 的命令行体验。 可以将其安装在 macOS、 Linux 和 Windows，还可以从命令行运行它。 

Azure CLI 进行了优化用于管理和从命令行管理 Azure 资源和生成工作针对 Azure 资源管理器中的自动化脚本。 它提供了许多相同的函数位于 Azure 堆栈门户中，包括丰富的数据的访问。

Azure 堆栈需要 Azure CLI 版本 2.0。 有关安装和配置 Azure CLI 与 Azure 堆栈的详细信息，请参阅[安装和配置 Azure 堆栈 CLI](azure-stack-connect-cli.md)。 有关如何使用 Azure CLI 2.0 执行使用 Azure 堆栈存储帐户中的资源的多个任务的详细信息，请参阅[对 Azure 存储空间使用 Azure CLI2.0](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>用于 Azure 堆栈的 azure CLI 示例脚本 
一旦你完成的 CLI 安装和配置，你可以尝试以下步骤以使用的小型壳示例脚本可与 Azure 堆栈存储资源进行交互。 该脚本首先创建一个新容器，在你的存储帐户，然后将现有文件 （作为 blob) 上载到该容器、 列出容器中的所有 blob 和最后，将文件下载到你指定的本地计算机上的目标。 在运行此脚本之前，请确保已成功连接并登录到目标 Azure 堆栈。 
1. 打开最喜欢的文本编辑器，然后将前面的脚本复制并粘贴到编辑器中。
2. 更新脚本的变量以反映你的配置设置。 
3. 更新了必要的变量后，保存脚本并退出编辑器。 后续步骤假定你已名为脚本 my_storage_sample.sh。
4. 如有必要，将脚本标记为可执行文件：`chmod +x my_storage_sample.sh`
5. 执行脚本。 例如，在 Bash 中：`./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

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
```

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 存储资源管理器

Microsoft Azure 存储资源管理器是从 Microsoft 一个独立应用。 它可以轻松地在 Windows、 macOS 和 Linux 上使用 Azure 存储空间和 Azure 堆栈存储数据。 如果你想管理你的 Azure 堆栈存储数据的简单办法，则可以考虑使用 Microsoft Azure 存储资源管理器。

有关配置 Azure 存储资源管理器，用于 Azure 堆栈的详细信息，请参阅[连接存储资源管理器到 Azure 堆栈订阅](azure-stack-storage-connect-se.md)。

有关 Microsoft Azure 存储资源管理器的详细信息，请参阅[存储资源管理器 （预览版） 入门](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>后续步骤
* [连接到 Azure 堆栈订阅的存储资源管理器](azure-stack-storage-connect-se.md)
* [存储资源管理器 （预览版） 入门](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure 一致存储： 差异和注意事项](azure-stack-acs-differences.md)
* [Microsoft Azure 存储简介](../../storage/common/storage-introduction.md)

