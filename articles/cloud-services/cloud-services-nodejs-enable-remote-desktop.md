---
title: "对云服务启用远程桌面 (Node.js)"
description: "了解如何对托管 Azure Node.js 应用程序的虚拟机进行远程桌面访问。"
services: cloud-services
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: a0141904-c9bc-478d-82af-5bceaca5cf6a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: c1551b250ace3aa6775932c441fcfe28431f8f57
ms.openlocfilehash: 09878cccc847040c59cbf285f40ac6a1a310c993


---
# <a name="enabling-remote-desktop-in-azure"></a>在 Azure 中启用远程桌面
你可以通过远程桌面访问在 Azure 中运行的角色实例的桌面。 您可以使用远程桌面连接配置虚拟机，或者排查应用程序问题。

> [!NOTE]
> 本文适用于托管为 Azure 云服务的 Node.js 应用程序。
> 
> 

## <a name="prerequisites"></a>先决条件
* 安装和配置 [Azure PowerShell](/powershell/azureps-cmdlets-docs)。
* 将 Node.js 应用部署到 Azure 云服务。 有关详细信息，请参阅[生成 Node.js 应用程序并将其部署到 Azure 云服务](cloud-services-nodejs-develop-deploy-app.md)。

## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>步骤 1：使用 Azure PowerShell 配置服务以进行远程桌面访问
若要使用远程桌面，你需要使用用户名、密码和证书更新 Azure 服务定义和配置。 

在包含应用源文件的计算机上执行以下步骤。

1. 以管理员身份运行 **Windows PowerShell**。 （在“开始”菜单或“开始”屏幕中，搜索 **Windows PowerShell**。）
2. 导航到包含服务定义 (.csdef) 文件和服务配置 (.cscfg) 文件的目录。
3. 输入以下 Azure Powershell cmdlet：
   
        Enable-AzureServiceProjectRemoteDesktop
4. 在提示符下，输入用户名和密码。
   
    ![enable-azureserviceprojectremotedesktop][enable-rdp]
5. 输入以下 PowerShell cmdlet 以发布更改：
   
       Publish-AzureServiceProject
   
   ![publish-azureserviceproject][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>步骤 2：连接到角色实例
发布更新服务定义后，可以连接到角色实例。

1. 在 [Azure 经典门户]中选择“云服务”，然后选择你的服务。
   
   ![Azure 经典门户][cloud-services]
2. 单击“实例”，然后单击“生产”或“过渡”查看你的服务实例。 选择一个实例，然后单击页面底部的“连接”。
   
   ![实例页][3]
3. 单击“连接”后，Web 浏览器会提示你保存.rdp 文件。 打开此文件。 （例如，如果你用的是 Internet Explorer，请单击“打开”。）
   
   ![提示打开或保存 .rdp 文件][4]
4. 打开该文件时，会显示以下安全提示：
   
   ![Windows 安全性提示][5]
5. 单击“连接”，将显示一个安全提示，要求输入访问该实例的凭据。 输入在 [步骤 1][步骤 1：使用 Azure PowerShell 配置服务以进行远程桌面访问] 中创建的密码，然后单击“确定”。
   
   ![用户名/密码提示][6]

连接完成后，远程桌面连接将在 Azure 中显示该实例的桌面。 

![远程桌面会话][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>步骤 3：配置服务以禁用远程桌面访问
当不再需要与云中角色实例的远程桌面连接时，可使用 [Azure PowerShell] 禁用远程桌面访问。

1. 输入以下 Azure Powershell cmdlet：
   
       Disable-AzureServiceProjectRemoteDesktop
2. 输入以下 PowerShell cmdlet 以发布更改：
   
       Publish-AzureServiceProject

## <a name="additional-resources"></a>其他资源
* [远程访问 Azure 中的角色实例] 
* [将远程桌面与 Azure 角色一起使用]
* [Node.js 开发人员中心](/develop/nodejs/)

[Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure 经典门户]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png

[远程访问 Azure 中的角色实例]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[将远程桌面与 Azure 角色一起使用]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx



<!--HONumber=Dec16_HO2-->


