---
title: 从 Azure 下载市场项 | Microsoft Docs
description: 云操作员可将市场项从 Azure 下载到 Azure Stack 部署。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/13/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 73f8616449141ca91f96e9fcebede74597bc4fe3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044911"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>将市场项从 Azure 下载到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

以云操作员的身份从 Azure 市场下载项，并使其适用于 Azure Stack。 可以选择的项来自 Azure 市场项的有序列表，这些项已预先经过测试，支持与 Azure Stack 配合使用。 其他项会不断地添加到此列表中，因此请不时地返回查看新内容。 

Azure 市场有两种连接场景： 

- **联网场景** - 需将 Azure Stack 环境连接到 Internet。 使用 Azure Stack 门户查找和下载项。 
- 
  **离线场景或部分联网场景** - 需要使用市场联合工具访问 Internet，以下载市场项。 然后，将下载内容传输到离线 Azure Stack 安装中。 此场景使用 PowerShell。

有关可下载的市场项的列表，请参阅 [Azure Stack 的 Azure 市场项](azure-stack-marketplace-azure-items.md)。


## <a name="connected-scenario"></a>联网场景
如果 Azure Stack 已连接到 Internet，则你可以使用管理门户下载市场项。

### <a name="prerequisites"></a>必备组件
Azure Stack 部署必须已建立 Internet 连接，并且[已注册到 Azure](azure-stack-register.md)。

### <a name="use-the-portal-to-download-marketplace-items"></a>使用门户下载市场项  
1. 登录到 Azure Stack 管理员门户。

2.  下载市场项之前，查看可用的存储空间。 稍后在选择要下载的项时，可将下载大小与可用存储容量进行比较。 如果容量有限，请考虑使用[管理可用空间](azure-stack-manage-storage-shares.md#manage-available-space)的选项。 

    若要查看可用空间，请在“区域管理”中选择要浏览的区域，然后转到“资源提供程序” > “存储”。

    ![查看存储空间](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. 打开 Azure Stack 市场并连接到 Azure。 为此，请依次选择“市场管理”、“从 Azure 中添加”。

    ![从 Azure 添加](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    门户将显示可从 Azure 市场下载的项的列表。 可以单击每个项查看其说明和附加信息，包括其下载大小。 

    ![市场列表](media/azure-stack-download-azure-marketplace-item/image03.png)

4. 选择所需的项，然后选择“下载”。 下载时间会有差异。

    ![下载消息](media/azure-stack-download-azure-marketplace-item/image04.png)

    下载完成后，可以 Azure Stack 操作员或用户的身份部署新市场项。

5. 若要部署下载的项，请选择“+新建”，在类别中搜索该新市场项。 接下来，选择该项以开始部署过程。 该过程根据市场项的不同而异。 

## <a name="disconnected-or-a-partially-connected-scenario"></a>离线场景或部分联网场景

如果 Azure Stack 处于离线模式且未建立 Internet 连接，请使用 PowerShell 和*市场联合工具*，将市场项下载到已建立 Internet 连接的计算机。 然后，将这些项传输到 Azure Stack 环境。 在离线环境中，无法使用 Azure Stack 门户下载市场项。 

也可以在联网场景中使用市场联合工具。 

此方案包含两个部分：
- 
  **第 1 部分：** 从 Azure 市场下载。 在能够访问 Internet 的计算机上配置 PowerShell，下载联合工具，然后从 Azure 市场下载项。  
- 
  **第 2 部分：** 上传并发布到 Azure Stack 市场。 将下载的文件移到 Azure Stack 环境，将其导入 Azure Stack，然后将其发布到 Azure Stack 市场。  


### <a name="prerequisites"></a>必备组件
- Azure Stack 部署必须[已注册到 Azure](azure-stack-register.md)。  

- 已建立 Internet 连接的计算机上必须已安装 **Azure Stack PowerShell 模块版本 1.2.11** 或更高版本。 如果未安装，请[安装 Azure Stack 特定的 PowerShell 模块](azure-stack-powershell-install.md)。  

- 若要启用已下载市场项的导入，必须配置 [Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)。  

- 必须在 Azure Stack 中拥有一个包含可公开访问的容器（存储 Blob）的[存储帐户](azure-stack-manage-storage-accounts.md)。 该容器将用作市场项库文件的临时存储。 如果你不熟悉存储帐户和容器，请参阅 Azure 文档中的[使用 Blob - Azure 门户](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

- 在执行第一个过程期间，将下载市场联合工具。 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>使用市场联合工具下载市场项

1. 在已建立 Internet 连接的计算机上，以管理员身份打开 PowerShell 控制台。

2. 添加已经用来注册过 Azure Stack 的 Azure 帐户。 若要添加帐户，请在 PowerShell 中不结合任何参数运行 `Add-AzureRmAccount`。 系统会提示输入 Azure 帐户凭据。根据帐户的配置，可能需要使用双重身份验证。

3. 如果有多个订阅，请运行以下命令，选择已经用于注册的那个订阅：  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 使用以下脚本下载最新版本的市场联合工具：  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. 运行以下脚本，导入联合模块并启动工具。 请将 *destination folder path* 替换为从 Azure 市场下载的文件的存储位置。   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. 当工具运行时，系统会提示输入 Azure 帐户凭据。 登录到已经用来注册过 Azure Stack 的 Azure 帐户。 登录成功以后，应会看到下图所示的屏幕，其中显示了可用市场项的列表。  

   ![Azure 市场项弹出窗口](media/azure-stack-download-azure-marketplace-item/image05.png)

7. 选择要下载的项，并记下版本。 (可以容纳*Ctrl*键以选择多个映像。)你只需引用*版本*导入下一个过程中的项时。 
   
   也可通过“添加条件”选项来筛选映像的列表。

8. 选择“确定”，然后查看并接受法律条款。 

9. 所需的下载时间取决于项的大小。 下载完成后，该项会出现在脚本中指定的文件夹内。 下载内容中包括一个 VHD 文件（适用于虚拟机）或 .ZIP 文件（适用于虚拟机扩展）。 此外，还包括一个 *.azpkg* 格式的库包。 （*.azpkg* 包是一个 *.zip* 文件。）
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>导入下载内容并发布到 Azure Stack 市场
1. 必须在本地将[前面下载的](#use-the-marketplace-syndication-tool-to-download-marketplace-items)虚拟机映像文件或解决方案模板文件提供给 Azure Stack 环境。  

2. 通过使用到 Azure Stack 中导入 VHD 映像**添加 AzsPlatformimage** cmdlet。 当使用此 cmdlet 时，替换*发布服务器*，*产品/服务*，和其他参数值与要导入图像的值。 

   可以获取*发布服务器*，*提供*，并*sku*文本文件下载的 AZPKG 文件中的图像的值。 该文本文件存储在目标位置。
 
   以下示例脚本使用了 Windows Server 2016 Datacenter - Server Core 虚拟机的值。 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **关于解决方案模板：** 某些模板可能包含名为 **fixed3.vhd** 的 3 MB .VHD 小文件。 无需将该文件导入 Azure Stack。 Fixed3.vhd。  某些解决方案模板包含此文件的目的是为了满足 Azure 市场的发布要求。

   请查看模板说明，然后下载并导入其他必需的项，例如，使用解决方案模板所需的 VHD。

3. 使用管理员门户将市场项包（.azpkg 文件）上传到 Azure Stack Blob 存储。 上传该包会使其可供 Azure Stack 使用，以便稍后能够将项发布到 Azure Stack 市场。

   需要有一个包含可公开访问的容器的存储帐户才能完成上传（请参阅此场景的先决条件）   
   1. 在 Azure Stack 管理员门户中，转到“更多服务” > “存储帐户”。  
   
   2. 从订阅中选择一个存储帐户，然后在“BLOB 服务”下选择“容器”。  
      ![Blob 服务](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. 选择要使用的容器，然后选择“上传”打开“上传 Blob”窗格。  
      ![容器](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. 在“上传 Blob”窗格中，浏览到要载入存储的文件，然后选择“上传”。  
      ![上传](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. 上传的文件会显示在容器窗格中。 选择一个文件，然后复制“Blob 属性”窗格的 URL。 在下一步骤中将市场项导入 Azure Stack 时，将要使用此 URL。  在下图中，容器为 *blob-test-storage*，文件为 *Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*。  文件 URL 为 *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*。  
      ![Blob 属性](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  在 PowerShell 中使用 **Add-AzsGalleryItem** cmdlet 将市场项发布到 Azure Stack。 例如：  
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. 发布库项后，可以查看通过“更多服务” > “Marketplace”查看该项。  如果下载内容是解决方案模板，请确保为该解决方案模板添加任何依赖的 VHD 映像。  
  
  ![查看市场](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> 现在，可以使用 Azure Stack PowerShell 1.3.0 版添加虚拟机扩展。

例如：

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>后续步骤

  [创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)