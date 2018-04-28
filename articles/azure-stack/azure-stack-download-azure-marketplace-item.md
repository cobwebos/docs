---
title: 从 Azure 下载 Marketplace 项 | Microsoft Docs
description: 可以将 Marketplace 项从 Azure 下载到 Azure Stack 部署。
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
ms.date: 02/27/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: c7d28831edf028657b7dd2a6a404a692a4a3bcb0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>将 Marketplace 项从 Azure 下载到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*


在决定要在 Azure Stack Marketplace 中包括什么内容时，应考虑可以从 Azure Marketplace 获得的内容。 可以从特选的 Azure Marketplace 项列表中下载，这些项已经过预先测试，可以在 Azure Stack 上运行。 新项会不断地添加到此列表中，因此请务必经常返回查看新内容。

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>在联网场景中（具有 Internet 连接）下载 Marketplace 项

1. 若要下载 Marketplace 项，必须先[将 Azure Stack 注册到 Azure](azure-stack-register.md)。
2. 登录 Azure Stack 管理员门户 (https://portal.local.azurestack.external)。
3. 一些 Marketplace 项可能占用很大存储空间。 请单击“资源提供程序” > “存储”进行检查，确保系统有足够的空间。

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. 单击“更多服务” > “Marketplace 管理”。

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. 单击“从 Azure 添加”，此时会看到可供下载的项的列表。 可以单击列表中的每个项，查看其说明和下载大小。

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. 在列表中选择所需项，然后单击“下载”。 所选项的 VM 映像开始下载。 下载时间会有差异。

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. 下载完成后，即可以 Azure Stack 操作员或用户的身份部署新的 Marketplace 项。 单击“+新建”，在类别中搜索这个新的 Marketplace 项，然后将其选中。
7. 单击“创建”，开始新下载项的创建体验。 按照分步说明部署你的项。

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>在联网或部分联网的场景中（Internet 连接受到限制）下载 Marketplace 项

在离线模式下（没有任何 Internet 连接）部署 Azure Stack 时，不能通过 Azure Stack 门户下载 Marketplace 项。 但是，可以使用 Marketplace 联合工具将 Marketplace 项下载到有 Internet 连接的计算机，然后再将这些项转移到 Azure Stack 环境。

### <a name="prerequisites"></a>必备组件
在使用 Marketplace 联合工具之前，请确保已[将 Azure Stack 注册到 Azure 订阅](azure-stack-register.md)。  

在具有 Internet 连接的计算机上，使用以下步骤下载所需的 Marketplace 项：

1. 以管理员身份打开 PowerShell 控制台，然后[安装 Azure Stack 特定的 PowerShell 模块](azure-stack-powershell-install.md)。 确保安装 **PowerShell 1.2.11 或更高版本**。  

2. 添加已经用来注册过 Azure Stack 的 Azure 帐户。 若要添加的帐户，运行**连接 AzureRmAccount**不带任何参数的 cmdlet。 系统会提示输入 Azure 帐户凭据。可能必须使用双重身份验证，具体取决于帐户的配置。  

3. 如果有多个订阅，请运行以下命令，选择已经用于注册的那个订阅：  

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 使用以下脚本下载最新版的 Marketplace 联合工具：  

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
   cd \AzureStack-Tools-master

   ```

5. 通过运行以下命令导入联合模块并启动工具：  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. 当工具运行时，系统会提示输入 Azure 帐户凭据。 登录到已经用来注册过 Azure Stack 的 Azure 帐户。 登录成功以后，会看到以下屏幕，显示可用 Marketplace 项的列表。  

   ![Azure Marketplace 项弹出窗口](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. 选择要下载的映像，记下映像版本。 可以通过按住 Ctrl 键来选择多个映像。 在下一部分，请使用映像版本来导入映像。  接下来，单击“确定”，然后通过单击“是”接受法律条款。 也可通过“添加条件”选项来筛选映像的列表。 

   下载需要一段时间，具体取决于映像的大小。 映像在下载后位于此前提供的目标路径中。 此下载项包含 VHD 文件和 Azpkg 格式的库项。

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>导入该映像，将其发布到 Azure Stack Marketplace

1. 在下载映像和库包以后，将它们和 AzureStack-Tools-master 文件夹中的内容保存到可移动磁盘驱动器，并将其复制到 Azure Stack 环境（可以将其以本地方式复制到任何位置，例如“C:\MarketplaceImages”）。     

2. 在导入此映像之前，必须连接到 Azure Stack 操作员的环境，所用步骤参见[配置 Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)。  

3. 使用 Add-AzsVMImage cmdlet 将映像导入到 Azure Stack。 使用此 cmdlet 时，务必将 *publisher*、*offer* 和其他参数值替换为要导入的映像的值。 可以从此前下载的 Azpkg 文件的 imageReference 对象获取映像的 *publisher*、*offer* 和 *sku* 值，以及从上一部分的步骤 6 获取 *version* 值。

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   替换这些参数值并运行以下命令：

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

4. 使用门户将 Marketplace 项 (.Azpkg) 上传到 Azure Stack Blob 存储。 可以上传到本地 Azure Stack 存储或上传到 Azure 存储。 （这是包的临时位置。）请确保 Blob 可公开访问并记下 URI。  

5. 使用 **Add-AzsGalleryItem** 将 Marketplace 项发布到 Azure Stack。 例如：

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. 库项发布后，可以通过“新建” > “Marketplace”窗格来查看。  

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>后续步骤

[创建和发布 Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)
