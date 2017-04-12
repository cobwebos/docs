---
title: "为 Azure 应用商店创建本地虚拟机映像 | Microsoft Docs"
description: "了解并执行相关步骤，以便创建本地 VM 映像并针对要购买的其他用户部署到 Azure 应用商店。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8f6b9a9293dc149586e6e5fd55028170ea825b07
ms.lasthandoff: 04/03/2017


---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>为 Azure 应用商店部署本地虚拟机映像
我们强烈建议使用远程桌面协议直接在云中开发 Azure 虚拟硬盘 (VHD)。 但是，如有必要，可以下载 VHD 并使用本地基础结构进行开发。  

对于本地开发，必须下载所创建 VM 的操作系统 VHD。 这些步骤将作为上述步骤 3.3 的一部分执行。  

## <a name="download-a-vhd-image"></a>下载 VHD 映像
### <a name="locate-a-blob-url"></a>找到 blob URL
若要下载 VHD，首先找到操作系统磁盘的 blob URL。

从新的 [Microsoft Azure 门户](https://portal.azure.com)找到 blob URL：

1. 转到“浏览” > “VM”，然后选择已部署的 VM。
2. 在“配置”下，选择“磁盘”磁贴，这将打开“磁盘”边栏选项卡。
   
   ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. 选择“OS 磁盘”，这将打开显示磁盘属性的另一个边栏选项卡，其中包括 VHD 位置。
4. 复制此 blob URL。
   
   ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. 现在，删除已部署的 VM，无需删除后备磁盘。 也可以停止 VM，而不是删除它。 当 VM 正在运行时，请不要下载操作系统 VHD。
   
   ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>下载 VHD
知道 blob URL 后，可以使用 [Azure 门户](http://manage.windowsazure.com/)或 PowerShell 下载 VHD。  

> [!NOTE]
> 创建本指南时，下载 VHD 的功能尚未在新 Microsoft Azure 门户中提供。  
> 
> 

**通过当前 [Azure 门户](http://manage.windowsazure.com/)下载操作系统 VHD**

1. 如果尚未执行此操作，请登录到 Azure 门户。
2. 单击“存储”选项卡。
3. 选择其中存储 VHD 的存储帐户。
   
   ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. 这将显示存储帐户属性。 选择“容器”选项卡。
   
   ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. 选择其中存储 VHD 的容器。 默认情况下，从门户创建时，VHD 会存储在 vhd 容器中。
   
   ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. 通过将 URL 与已保存的进行比较，选择正确的操作系统 VHD。
7. 单击“下载”。
   
   ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>使用 PowerShell 下载 VHD
除了使用 Azure 门户，还可以使用 [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) cmdlet 来下载操作系统 VHD。

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
例如，Save-AzureVhd -Source “https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\baseimagevm.vhd” -StorageKey <String>

> [!NOTE]
> **Save-AzureVhd** 还具有 **NumberOfThreads** 选项，可用来增加并行度，以便针对下载充分利用可用的带宽。
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>将 VHD 上载到 Azure 存储帐户
如果已准备好 VHD 本地，则需要将它们上载到 Azure 中的存储帐户。 在创建 VHD 本地后执行此步骤，但在获取 VM 映像的认证前执行此步骤。

### <a name="create-a-storage-account-and-container"></a>创建存储帐户和容器
我们建议 VHD 上载到美国某一区域的存储帐户中。 单个 SKU 的所有 VHD 都应放置在单个存储帐户内的单个容器中。

若要创建存储帐户，可以使用 [Microsoft Azure 门户](https://portal.azure.com/)、PowerShell 或 Linux 命令行工具。  

**从 Microsoft Azure 门户中创建存储帐户**

1. 单击“新建” 。
2. 选择“存储”。
3. 填写存储帐户名称，然后选择位置。
   
   ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. 单击“创建” 。
5. 创建的存储帐户的边栏选项卡应该会打开。 如果没有，请依次选择“浏览” > “存储帐户”。 在“存储帐户”边栏选项卡上，选择已创建的存储帐户。
6. 选择“容器”。
   
   ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. 在“容器”边栏选项卡上，选择“添加”，然后输入容器名称和容器权限。 为容器权限选择“私有”。

> [!TIP]
> 我们建议你按 SKU 创建计划发布的容器。
> 
> 

  ![绘制](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>使用 PowerShell 创建存储帐户
借助 PowerShell，使用 [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) cmdlet 创建存储帐户。

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

然后，可以使用 [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) cmdlet 在存储帐户中创建容器。

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> 这些命令假设已在 PowerShell 中设置当前存储帐户上下文。   有关 PowerShell 设置的详细信息，请参阅[设置 Azure PowerShell](marketplace-publishing-powershell-setup.md)。  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>使用适用于 Mac 和 Linux 的命令行工具创建存储帐户
> 通过 [Linux 命令行工具](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)创建存储帐户，如下所示。
> 
> 

        azure storage account create mystorageaccount --location "West US"

创建一个容器，如下所示。

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>上载 VHD
创建存储帐户和容器后，可以上载已准备好的 VHD。 可以使用 PowerShell、Linux 命令行工具或其他 Azure 存储管理工具。

### <a name="upload-a-vhd-via-powershell"></a>通过 PowerShell上载 VHD
使用 [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) cmdlet。

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>使用适用于 Mac 和 Linux 的命令行工具上载 VHD
借助 [Linux 命令行工具](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)，使用以下项：azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>

## <a name="see-also"></a>另请参阅
* [为应用商店创建虚拟机映像](marketplace-publishing-vm-image-creation.md)
* [设置 Azure PowerShell](marketplace-publishing-powershell-setup.md)


