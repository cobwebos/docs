---
title: "使用应用商店工具包来创建和发布的应用商店项 |Microsoft 文档"
description: "了解如何使用发布工具包快速创建应用商店项"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 61ee3296429f9641643f1c9268ae89e3691fcfa1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>添加使用发布工具的应用商店项
你将内容添加到[Azure 堆栈应用商店](azure-stack-marketplace.md)使你的解决方案可供您和您的租户以进行部署。  应用商店工具包创建基于 IaaS Azure 资源管理器模板或 VM 扩展的 Azure 应用商店包 (.azpkg) 文件。  你还可以使用应用商店工具包发布.azpkg 文件，使用该工具创建或使用[手动](azure-stack-create-and-publish-marketplace-item.md)步骤。  本主题将指导您完成下载该工具、 创建基于 VM 模板中，应用商店项和再将该项目发布到 Azure 堆栈应用商店。     


## <a name="prerequisites"></a>必备组件
 - 你必须在 Azure 堆栈主机上运行该工具包或具有[VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)从运行该工具的计算机的连接。

 - 下载[Azure 堆栈快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip)和提取。

 - 下载[Azure 库打包工具](http://aka.ms/azurestackmarketplaceitem)(AzureGalleryPackage.exe)。 

 - 发布到应用商店要求图标和缩略图文件。  您可以使用你自己，或保存[示例](azure-stack-marketplace-publisher.md#support-files)本地为此示例中的文件。

## <a name="download-the-tool"></a>下载工具
应用商店工具包可以是[从 Azure 堆栈 Tools 存储库下载](azure-stack-powershell-download.md)。


##  <a name="create-marketplace-items"></a>创建应用商店项
在此部分中，你可以使用应用商店工具包以.azpkg 格式创建应用商店项包。  

### <a name="provide-marketplace-information-with-wizard"></a>提供使用向导的应用商店信息
1. 从 PowerShell 会话中运行应用商店工具包：
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. 单击**解决方案**选项卡。此屏幕接受你的应用商店项目的信息。 如您希望其出现在应用商店，请输入你的项目的信息。  你还可以指定[参数文件](azure-stack-marketplace-publisher.md#use-a-parameters-file)用于预先填充窗体。  
    
    ![应用商店工具包第一个屏幕的屏幕快照](./media/azure-stack-marketplace-publisher/image7.png)
3. 单击**浏览**并选择每个图标和屏幕截图字段图像文件。  你可以使用你自己的图标或中的示例图标[支持文件](azure-stack-marketplace-publisher.md#support-files)部分。
4. 后填充所有字段，选择"预览解决方案"内应用商店的解决方案的预览。  可以修改并单击前编辑文本、 图像和屏幕截图**下一步**。  

### <a name="import-template-and-create-package"></a>导入模板，创建包
在本部分中，可以将模板导入和处理输入你的解决方案。

1.  单击**浏览**和选择*azuredeploy.json*从下载的模板中的 101 简单的 Windows 虚拟机文件夹。

    ![应用商店工具包第二个屏幕的屏幕快照](./media/azure-stack-marketplace-publisher/image8.png)
2.  使用填充部署向导*基本*步骤和输入项模板中指定每个参数。  您可以添加其他步骤，并将输入移动步骤之间。  例如，你可能想"前端配置"和"后端配置"步骤为你的解决方案。
3.  指定 AzureGalleryPackager.exe 的路径。  
4.  单击**创建**，应用商店工具包到.azpkg 文件打包你的解决方案。  完成后，该向导将显示你的解决方案文件的路径，并为你提供的选项以继续发布到 Azure 堆栈的你的包。


## <a name="publish-marketplace-items"></a>发布应用商店项
在此部分中，你将应用商店项目发布到 Azure 堆栈应用商店。

![应用商店工具包第一个屏幕的屏幕快照](./media/azure-stack-marketplace-publisher/image9.png)

1.  此向导需要发布你的解决方案的信息：
    
    |字段|说明|
    |-----|-----|
    | 服务管理员名称 | 服务管理员帐户。  示例：ServiceAdmin@mydomain.onmicrosoft.com |
    | 密码 | 服务管理员帐户密码。 |
    | API 终结点 | Azure 堆栈 Azure 资源管理器终结点。  示例： management.local.azurestack.external |
2.  单击**发布**，发布的日志会显示。
3.  现在已能够部署你已发布的项，通过 Azure 堆栈门户。


## <a name="use-a-parameters-file"></a>使用参数文件
参数文件还可用于完成的应用商店项信息。  

应用商店工具包包括*solution.parameters.ps1*可用于创建你自己的参数文件。


## <a name="support-files"></a>支持文件
| 说明 | 示例 |
| ----- | ----- |
| 40 x 40 个.png 图标 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90 x 90.png 图标 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| 115 x 115.png 图标 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255 x 115.png 图标 | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324.png 缩略图 | ![](./media/azure-stack-marketplace-publisher/image5.png) |


