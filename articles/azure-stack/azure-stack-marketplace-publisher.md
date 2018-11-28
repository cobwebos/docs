---
title: 使用市场工具包创建和发布市场项 | Microsoft Docs
description: 了解如何使用发布工具包快速创建市场项
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 4758c29d1cdb7e09c397524574ba1ab6701efeeb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238014"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>使用发布工具添加市场项

将内容添加到 [Azure Stack 市场](azure-stack-marketplace.md)后，你和租户可将你的解决方案用于部署。 Marketplace 工具包创建基于 IaaS Azure 资源管理器模板或 VM 扩展的 Azure Marketplace 包 (.azpkg) 文件。 您还可以使用 Marketplace 工具包发布.azpkg 文件，使用该工具创建或使用[手动](azure-stack-create-and-publish-marketplace-item.md)步骤。 本主题介绍下载该工具，创建 marketplace 项基于 VM 模板，然后将该项发布到 Azure Stack Marketplace。     

## <a name="prerequisites"></a>必备组件

 - 必须在 Azure Stack 主机上运行该工具包或具有[VPN](.\asdk\asdk-connect.md#connect-with-vpn)从其运行该工具的计算机连接到 ASDK 主机。

 - 下载 [Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip)并解压缩。

 - 下载 [Azure 库包工具](https://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe)。 

 - 要发布到市场需有图标和缩略图文件。 可以使用自己的文件，或者将本示例的[示例](azure-stack-marketplace-publisher.md#support-files)文件保存在本地。

## <a name="download-the-tool"></a>下载该工具

您可以下载 Marketplace 工具包[从 Azure Stack 工具存储库](azure-stack-powershell-download.md)。

##  <a name="create-marketplace-items"></a>创建市场项

在本部分中，使用 Marketplace 工具包创建.azpkg 格式的 marketplace 项包。  

### <a name="provide-marketplace-information-with-wizard"></a>在向导中提供市场信息

1. 从 PowerShell 会话运行市场工具包：
   ```PowerShell
   .\MarketplaceToolkit.ps1
   ```

2. 单击“解决方案”选项卡。此屏幕接受市场项相关信息。 请输入想要显示在市场中的项相关信息。 也可以指定[参数文件](azure-stack-marketplace-publisher.md#use-a-parameters-file)来预先填充窗体。  
    
    ![市场工具包第一个屏幕的屏幕截图](./media/azure-stack-marketplace-publisher/image7.png)
3. 单击“浏览”以选择每个图标和屏幕截图字段的图像文件。 可以使用自己的图标，或[支持文件](azure-stack-marketplace-publisher.md#support-files)部分中的示例图标。
4. 填充所有字段后，选择“预览解决方案”来预览市场内的解决方案。 可以修改并编辑文本、图像和屏幕截图，然后单击“下一步”。  

### <a name="import-template-and-create-package"></a>导入模板并创建包

在本部分，我们导入模板并使用解决方案的输入。

1.  单击“浏览”并从所下载模板的 101-Simple-Windows-VM 文件夹中选择 *azuredeploy.json*。

    ![市场工具包第二个屏幕的屏幕截图](./media/azure-stack-marketplace-publisher/image8.png)
2.  部署向导中填入*基本*为模板中指定每个参数的步骤和输入项。 可以添加其他步骤，并在步骤之间移动输入项。 例如，你可能需要为解决方案添加“前端配置”和“后端配置”步骤。
3.  指定 AzureGalleryPackager.exe 的路径。  
4.  单击“创建”。 Marketplace 工具包打包成.azpkg 文件的解决方案。 完成后，该向导将显示您的解决方案文件的路径，并为你提供选项以继续将包发布到 Azure Stack。

## <a name="publish-marketplace-items"></a>发布市场项

在本部分，我们将市场项发布到 Azure Stack 市场。

![市场工具包第一个屏幕的屏幕截图](./media/azure-stack-marketplace-publisher/image9.png)

1.  向导需要以下信息来发布解决方案：
    
    |字段|说明|
    |-----|-----|
    | 服务管理员名称 | 服务管理员帐户。  示例： ServiceAdmin@mydomain.onmicrosoft.com |
    | 密码 | 服务管理员帐户的密码。 |
    | API 终结点 | Azure Stack Azure 资源管理器终结点。 例如：management.local.azurestack.external |
2.  单击“发布”，发布日志随即显示。
3.  现在，可以通过 Azure Stack 门户部署发布的项。

## <a name="use-a-parameters-file"></a>使用参数文件

也可以使用参数文件来填写市场项信息。  

Marketplace 工具包包括*solution.parameters.ps1*可用于创建您自己的参数文件。

## <a name="support-files"></a>支持文件

| 说明 | 示例 |
| ----- | ----- |
| 40x40 .png 图标 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90x90 .png 图标 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| 115x115 .png 图标 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255x115 .png 图标 | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533x324 .png 缩略图 | ![](./media/azure-stack-marketplace-publisher/image5.png) |

## <a name="next-steps"></a>后续步骤

[下载市场项](azure-stack-download-azure-marketplace-item.md)  
[创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)