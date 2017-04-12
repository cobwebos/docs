---
title: "将 PowerShell cmdlet 与 Azure RemoteApp 配合使用 | Microsoft Docs"
description: "了解如何在 Azure RemoteApp 中使用 Windows PowerShell cmdlet。"
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 7d3d5ded-6f73-4de6-a8ac-c1d622e842a2
ms.service: remoteapp
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9134b5893413abbc49e2332651fb4a8b549ce559
ms.lasthandoff: 03/31/2017


---
# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>将 Windows PowerShell cmdlet 与 Azure RemoteApp 一起使用
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

 你可以使用 Azure RemoteApp PowerShell cmdlet 管理和维护你的集合。 若要开始，请使用以下信息。

## <a name="get-the-cmdlets"></a>获取 cmdlet
- - -
首次下载 Azure PowerShell cmdlet 请单击[此处](http://go.microsoft.com/?linkid=9811175)，RemoteApp cmdlet 会包括在其中。 

查看 [Azure RemoteApp cmdlet 帮助信息](https://msdn.microsoft.com/library/mt428031.aspx)。

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>配置 Azure cmdlet 以使用你的订阅
- - -
请遵循[本指南](/powershell/azureps-cmdlets-docs)中的操作，以便可以针对你的 Azure 订阅使用这些 cmdlet。

可以使用以下步骤来快速开始︰

1. 下载并安装 [Azure PowerShell cmdlet](http://go.microsoft.com/?linkid=9811175)。
2. 启动 Microsoft Azure PowerShell。
3. 运行 **Add-AzureAccount** 向 Azure 订阅进行身份验证。 出现提示时，输入用于登录 Azure 门户的用户名和密码。  
4. 运行 **Get-azuresubscription** 以列出与你的用户帐户相关联的订阅。 
5. 运行 **Select-AzureSubscription** 并指定要在 PowerShell 控制台中使用的订阅名称或 ID。

祝贺你，你的 Azure PowerShell 控制台已配置好并可以使用了。 请注意，每次启动 Azure PowerShell 控制台时都需要重复步骤 2 至步骤 5。  

## <a name="create-a-cloud-collection"></a>创建云集合
- - -
只需运行以下命令：

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

上面的命令会自动发布 Microsoft Office 365 应用程序（Excel、OneNote、Outlook、PowerPoint、Visio 和 Word）。

集合创建可能需要 30 分钟或更长时间才能完成。 因此，此命令会返回你可以使用的跟踪 ID，如下所示︰

    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

集合完成之后，可以使用以下命令将用户添加到集合中︰

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

你成功了！ 用户应该可以使用[此处](https://www.remoteapp.windowsazure.com/)，找到的 Azure RemoteApp 客户端连接到应用程序。

## <a name="available-cmdlets"></a>可用的 cmdlet
下面是可用的许多其他命令，我们很快就会编写出针对这些命令的文档︰

基本 RemoteApp 集合 cmdlet： 

* New-AzureRemoteAppCollection
* Get-AzureRemoteAppCollection
* Set-AzureRemoteAppCollection
* Update-AzureRemoteAppCollection
* Remove-AzureRemoteAppCollection
* Add-AzureRemoteAppUser
* Get-AzureRemoteAppUser
* Remove-AzureRemoteAppUser
* Get-AzureRemoteAppSession
* Disconnect-AzureRemoteAppSession
* Invoke-AzureRemoteAppSessionLogoff
* Send-AzureRemoteAppSessionMessage
* Get-AzureRemoteAppProgram
* Get-AzureRemoteAppStartMenuProgram
* Publish-AzureRemoteAppProgram
* Unpublish-AzureRemoteAppProgram
* Get-AzureRemoteAppCollectionUsageDetails
* Get-AzureRemoteAppCollectionUsageSummary
* Get-AzureRemoteAppPlan

RemoteApp 虚拟网络 cmdlet：

* New-AzureRemoteAppVNet
* Get-AzureRemoteAppVNet
* Set-AzureRemoteAppVNet
* Remove-AzureRemoteAppVNet
* Get-AzureRemoteAppVpnDevice
* Get-- AzureRemoteAppVpnDeviceConfigScript
* Reset-AzureRemoteAppVpnSharedKey

RemoteApp 模板映像 cmdlet：

* New-AzureRemoteAppTemplateImage
* Get-AzureRemoteAppTemplateImage
* Rename-AzureRemoteAppTemplateImage
* Remove-AzureRemoteAppTemplateImage

其他 RemoteApp cmdlet：

* Get-AzureRemoteAppLocation
* Get-AzureRemoteAppWorkspace
* Set-AzureRemoteAppWorkspace
* Get-AzureRemoteAppOperationResult


