---
title: 从 Azure 下载 Marketplace 项 | Microsoft Docs
description: 云操作员可以从 Azure 下载到我的 Azure 堆栈部署应用商店项。
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
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 69148a0ac9a5761eeee0ab47d83862724583619a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>将 Marketplace 项从 Azure 下载到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

作为云操作员，从 Azure 应用商店下载的项目，并提供 Azure 堆栈中。 你可以选择各项是从预测试和支持使用 Azure 堆栈的 Azure 应用商店项策展列表。 其他项经常会添加到此列表，因此继续不久后回来查看新内容。 

有用于连接到 Azure 应用商店的两种方案： 

- **连接的方案**-需要您的 Azure 堆栈环境以连接到 internet。 使用 Azure 堆栈门户查找和下载项。 
- **断开连接或部分连接方案**-，需要访问 internet 使用 marketplace 联合工具下载应用商店项。 然后，将您的下载传输到你已断开连接的 Azure 堆栈安装。 这种情况下使用 PowerShell。

请参阅[Azure 堆栈的 Azure 应用商店项](azure-stack-marketplace-azure-items.md)有关你可以下载的应用商店项的列表。


## <a name="connected-scenario"></a>连接的方案
如果 Azure 堆栈连接到 internet，你可以使用管理门户下载的应用商店项。

### <a name="prerequisites"></a>必备组件
你的 Azure 堆栈部署必须具有 internet 连接，而且必须[向 Azure 注册了](azure-stack-register.md)。

### <a name="use-the-portal-to-download-marketplace-items"></a>使用门户下载的应用商店项  
1. 登录到 Azure 堆栈管理员门户。

2.  下载应用商店项之前查看可用存储空间。 更高版本，当你选择下载的项，你可以为你的可用存储容量比较下载大小。 如果容量有限，请考虑选项[管理可用空间](azure-stack-manage-storage-shares.md#manage-available-space)。 

    若要查看可用空间，在**区域管理**选择你想要浏览，然后转到的区域**资源提供程序** > **存储**。

    ![检查存储空间](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. 打开 Azure 堆栈应用商店并连接到 Azure。 为此，请选择**应用商店管理**，然后选择**从 Azure 中的添加**。

    ![从 Azure 添加](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    门户将显示可从 Azure 应用商店下载项的列表。 你可以单击每个项可查看其说明和其他信息，包括其下载大小。 

    ![应用商店列表](media/azure-stack-download-azure-marketplace-item/image03.png)

4. 选择的项，然后选择**下载**。 下载时间会有差异。

    ![下载消息](media/azure-stack-download-azure-marketplace-item/image04.png)

    下载完成后，你可以部署新的应用商店项目作为 Azure 堆栈运算符或用户。

5. 要部署下载的项，请选择 **+ 新建**，然后搜索之间新的应用商店项目的类别。 接下来选择要开始部署过程的项。 该过程因不同的应用商店项而异。 

## <a name="disconnected-or-a-partially-connected-scenario"></a>断开连接或部分连接的方案

如果 Azure 堆栈处于断开连接模式，而无需 internet 连接，则使用 PowerShell 和*marketplace 联合工具*下载到计算机的 internet 连接的应用商店项。 然后将项传输到你的 Azure 堆栈环境。 在连接断开的环境，不能通过使用 Azure 堆栈门户下载的应用商店项。 

应用商店联合工具还可在连接方案。 

此方案包含两个部分：
- **第 1 部分：** 从 Azure 应用商店下载。 具有 internet 访问的计算机上你配置 PowerShell，请下载联合工具，然后下载项目窗体 Azure 应用商店。  
- **第 2 部分：** 上载和发布到 Azure 堆栈市场。 移动的文件下载到你的 Azure 堆栈环境，将它们导入 Azure 堆栈，然后将它们发布到 Azure 堆栈应用商店。  


### <a name="prerequisites"></a>必备组件
- 必须是 Azure 堆栈部署[向 Azure 注册了](azure-stack-register.md)。  

- 具有 internet 连接的计算机必须安装**Azure 堆栈 PowerShell 模块版本 1.2.11**或更高版本。 如果尚不存在，[安装 Azure 堆栈特定 PowerShell 模块](azure-stack-powershell-install.md)。  

- 若要启用的这样的下载的应用商店项目的导入[PowerShell 环境用于 Azure 堆栈运算符](azure-stack-powershell-configure-admin.md)必须配置。  

- 包含一个可公开访问容器 （这是存储 blob） 的 Azure 堆栈中，必须具有存储帐户。 将容器用作临时存储的应用商店项库文件。 如果你不熟悉与存储帐户和容器，请参阅[兼容 blob 的 Azure 门户](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)Azure 文档中。

- 在第一个过程中下载应用商店联合工具。 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>使用应用商店联合工具下载应用商店项

1. 在建立 Internet 连接的计算机上，以管理员身份打开 PowerShell 控制台。

2. 添加已经用来注册过 Azure Stack 的 Azure 帐户。 若要运行的 PowerShell 中添加帐户，`Add-AzureRmAccount`不带任何参数。 系统将提示你输入你的 Azure 帐户凭据和你可能必须使用基于你的帐户配置的双因素身份验证。

3. 如果有多个订阅，请运行以下命令，选择已经用于注册的那个订阅：  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 通过使用以下脚本下载最新版本的应用商店联合工具：  

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

5. 导入联合模块，然后通过运行以下脚本中启动该工具。 替换*目标文件夹路径*用于存储从 Azure 应用商店下载的文件的位置。   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. 当工具运行时，系统会提示输入 Azure 帐户凭据。 登录到已经用来注册过 Azure Stack 的 Azure 帐户。 登录成功后，你应看到如下屏幕下图中，与可用的应用商店项的列表。  

   ![Azure Marketplace 项弹出窗口](media/azure-stack-download-azure-marketplace-item/image05.png)

7. 选择你想要下载和记下的项*版本*。 (可以通过按住选择多个映像*Ctrl*键。)将引用*版本*导入下一个过程中的项时。 
   
   也可通过“添加条件”选项来筛选映像的列表。

8. 选择**确定**，然后查看并接受法律条款。 

9. 下载花费的时间取决于项的大小。 下载完成后，项是在脚本中指定的文件夹中。 下载内容中包括的 VHD 文件 （虚拟机） 或。（适用于虚拟机扩展） 的 ZIP 文件。 它还包括中的库包 *.azpkg*格式。 (A *.azpkg*包是 *.zip*文件。)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>导入下载并发布到 Azure 堆栈市场
1. 虚拟机映像，或者你有的解决方案模板的文件[以前下载的](#use-the-marketplace-syndication-tool-to-download-marketplace-items)必须可供本地 Azure 堆栈环境。  

2. 导入。VHD 文件复制到 Azure 堆栈。 若要成功导入虚拟机 (VM) 映像，你必须使用有关 VM 的以下信息：
   - *版本*，如前面的过程的步骤 7 中所述。
   - 为虚拟机的值*发布服务器*，*提供*，和*sku*。 若要获取这些值，重命名一份 **.azpkg**文件以更改其文件扩展名 **.zip**。 然后可以使用文本编辑器打开**DeploymentTemplates\CreateUiDefinition.json**。 在.json 文件中，找到*imageReference*部分中，其中包含应用商店项目的以下值。 下面的示例演示此信息的显示方式：

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   通过将映像导入 Azure 堆栈**添加 AzsPlatformimage** cmdlet。 使用此 cmdlet 时，务必将 *publisher*、*offer* 和其他参数值替换为要导入的映像的值。 你可以获取*发布服务器*，*提供*，和*sku*来自以及 AZPKG 文件下载并存储在目标位置的文本文件的映像的值. 

   在下面的示例脚本中，将使用 Windows Server 2016 Datacenter 的服务器内核的虚拟机的值。 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **有关解决方案模板：** 某些模板可以包含小 3 MB。具有名称的 VHD 文件**fixed3.vhd**。 你不必将该文件导入到 Azure 堆栈。 Fixed3.vhd。  此文件是包含某些解决方案模板，以满足发布为 Azure 应用商店的要求。

   查看模板说明并下载，然后导入所需使用的解决方案模板的 Vhd 等其他要求。

3. 使用管理门户将在应用商店项包 （.azpkg 文件） 上载到 Azure 堆栈 Blob 存储。 上载的包使其可供 Azure 堆栈以便更高版本可以发布项 Azure 堆栈应用商店。

   上载要求你拥有了一个可公开访问容器的存储帐户 （请参阅此方案的先决条件）   
   1. 在 Azure 堆栈管理员门户中，转到**更多的服务** > **存储帐户**。  
   
   2. 选择一个存储帐户，从你的订阅，然后在下**BLOB 服务**，选择**容器**。  
      ![Blob 服务](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. 选择你想要使用，然后选择的容器**上载**以打开**上载 blob**窗格。  
      ![容器](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. 在上载 blob 窗格中，浏览到你想要将加载到存储，然后选择文件**上载**。  
      ![上传](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. 你上载的文件会显示在容器窗格中。 选择一个文件，然后复制从 URL **Blob 属性**窗格。 将应用商店项目导入到 Azure 堆栈时，将在下一步中使用此 URL。  在下图中，容器是*blob 测试存储*和该文件是*Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*。  文件 URL *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*。  
      ![Blob 属性](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  使用 PowerShell 通过使用应用商店项目发布到 Azure 堆栈**添加 AzsGalleryItem** cmdlet。 例如：  
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. 您发布的库项后，你可以查看从**更多的服务** > **Marketplace**。  如果你下载的解决方案模板，请确保您添加该解决方案模板的任何依赖 VHD 映像。  
  ![查看应用商店](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> 与 Azure 堆栈 PowerShell 1.3.0 新版现在可以添加虚拟机扩展。

例如：

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>后续步骤
[创建和发布 Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)