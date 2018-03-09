---
title: "从 Azure 下载应用商店项 |Microsoft 文档"
description: "我可以从 Azure 到我的 Azure 堆栈部署下载的应用商店项。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: cdadf48aa23e3dd76d8a511794f00725f073611d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>从 Azure 应用商店项下载到 Azure 堆栈

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*


当您决定在 Azure 堆栈应用商店中包括哪些内容，应考虑从 Azure 应用商店的可用内容。 你可以从策展已预先测试，在 Azure 堆栈上运行的 Azure 应用商店项列表中进行下载。 新项经常会添加到此列表，因此请务必不久后回来查看新内容。

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>下载 （具有 internet 连接） 的连接方案中的应用商店项

1. 若要下载应用商店项，你必须首先[向 Azure 注册 Azure 堆栈](azure-stack-register.md)。
2. 登录到 Azure 堆栈管理员门户 (https://portal.local.azurestack.external)。
3. 一些应用商店项可能很大。 请检查以确保通过单击你的系统上有足够的空间**资源提供程序** > **存储**。

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. 单击**更多的服务** > **应用商店管理**。

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. 单击**从 Azure 中的添加**以查看可供下载项的列表。 你可以单击要查看其说明和下载大小的列表中每一项。

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. 选择你要在列表中，然后单击的项**下载**。 若要下载的启动选项 VM 映像。 下载时间会有所不同。

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. 下载完成后，你可以部署新应用商店项目作为 Azure 堆栈运算符或用户。 单击**+ 新建**，新的应用商店项目中，类别中搜索，然后选择项。
7. 单击**创建**以打开新下载的项的创建体验。 按照分步说明操作中，若要部署你的项目。

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>下载中断开连接的应用商店项或部分连接的方案 （具有有限的 internet 连接）

在断开连接 （不带任何 internet 连接） 模式下部署 Azure 堆栈时，你无法通过 Azure 堆栈门户下载的应用商店项。 但是，可以使用应用商店联合工具下载到计算机具有 internet 连接的应用商店项，然后将它们传输到 Azure 堆栈环境。

### <a name="prerequisites"></a>必备组件
你可以使用应用商店联合工具之前，请确保你有[注册您的 Azure 订阅的 Azure 堆栈](azure-stack-register.md)。  

从具有 internet 连接的计算机，使用以下步骤来下载所需的应用商店项：

1. 打开 PowerShell 控制台以管理员身份和[安装 Azure 堆栈特定 PowerShell 模块](azure-stack-powershell-install.md)。 请确保你安装**PowerShell 版本 1.2.11 或更高版本**。  

2. 添加具有用于注册 Azure 堆栈的 Azure 帐户。 若要添加的帐户，运行**添加 AzureRmAccount**不带任何参数的 cmdlet。 系统将提示你输入你的 Azure 帐户凭据，并且你可能必须为使用双因素身份验证，具体情况视你的帐户的配置。  

3. 如果你有多个订阅，运行以下命令以选择的一个具有用于注册：  

   ```powershell
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
   cd \AzureStack-Tools-master

   ```

5. 导入联合模块，并通过运行以下命令启动该工具：  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. 工具运行时，系统会提示你输入你的 Azure 帐户的凭据。 登录到你已使用来注册 Azure 堆栈的 Azure 帐户。 登录名是成功之后, 你应看到与可用的应用商店项的列表下面的屏幕。  

   ![Azure 应用商店项弹出窗口](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. 选择你想要下载和记下映像版本的映像。 可以通过按住 Ctrl 键来选择多个映像。 你使用的映像版本导入下一节中的图像。  接下来，单击**确定**，然后通过单击接受法律条款**是**。 此外可以通过使用筛选的映像列表**添加条件**选项。 

   下载需要一段时间，具体取决于映像的大小。 一次图像下载，它可用于您之前提供的目标路径。 下载包含 Azpkg 格式中的 VHD 文件和库项。

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>导入映像并将其发布到 Azure 堆栈市场

1. 下载映像和库包后，将它们和内容保存到可移动磁盘驱动器的 AzureStack 工具主文件夹中，并将其复制到 Azure 堆栈环境 (你可以复制该本地到的任何位置如:"C:\MarketplaceImages")。     

2. 然后再导入映像，你必须通过连接到 Azure 堆栈运算符的环境中所述的步骤[配置 Azure 堆栈运算符 PowerShell 环境](azure-stack-powershell-configure-admin.md)。  

3. 通过使用添加 AzsVMImage cmdlet 导入到 Azure 堆栈的映像。 使用此 cmdlet 时，请确保将*发布服务器*，*提供*，和其他参数值与你要导入的图像的值。 你可以获取*发布服务器*，*提供*，和*sku*来自前面下载的 Azpkg 文件 imageReference 对象的映像的值与*版本*步骤 6 中上一节中的值。

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   替换参数值并运行以下命令：

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

4. 使用门户来上载应用商店项 (。Azpkg) 到 Azure 堆栈 Blob 存储。 你可以将上载到 Azure 堆栈的本地存储或上载到 Azure 存储。 （它是包的临时位置。）请确保 blob 是可公开访问，请注意 URI。  

5. 使用将应用商店项目发布到 Azure 堆栈**添加 AzsGalleryItem**。 例如：

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. 发布库项后，你可以查看从**新建** > **Marketplace**窗格。  

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>后续步骤

[创建和发布的应用商店项](azure-stack-create-and-publish-marketplace-item.md)
