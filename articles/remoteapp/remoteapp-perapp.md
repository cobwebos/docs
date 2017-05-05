---
title: "向 Azure RemoteApp 集合中的单个用户发布应用程序（预览）| Microsoft Docs"
description: "了解如何在 Azure RemoteApp 中，不根据组，而向单个用户发布应用。"
services: remoteapp-preview
documentationcenter: 
author: piotrci
manager: mbaldwin
editor: 
ms.assetid: 1fd0539d-fa65-4ea5-a98e-0be0cf580690
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: piotrci
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7970fb624fcf76d9cba6e60c9766e39b63f175ba
ms.lasthandoff: 04/27/2017


---
# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>向 Azure RemoteApp 集合中的单个用户发布应用程序（预览）
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

本文介绍了如何向 Azure RemoteApp 集合中的单个用户发布应用程序。 这是 Azure RemoteApp 中的新功能，当前处于“特邀预览”阶段。出于评估目的，仅对特定的初期用户可用。

Azure RemoteApp 最初只启用了一种“发布”应用程序的方式：管理员可以从映像发布应用并且这些应用对集合中的所有用户可见。

一种常见方案是将多个应用程序包括在单个映像中并部署一个集合，以便降低管理成本。 通常不是所有应用程序都与所有用户相关 – 管理员更希望将应用发布到单个用户，这样用户就不会在他们的应用程序源中看到不必要的应用程序。

现在，这一想法可在 Azure RemoteApp 中实现 – 目前仅作为受限的预览功能。 以下是新功能的简短摘要：

1. 集合可以设置为以下两种模式之一：
   
   * 原始的“集合模式”，在这种模式下所有用户都可以都看到所有已发布的应用程序。 这是默认模式。
   * 新的“应用程序模式”，在这种模式下用户只能看到显式分配给他们的应用程序
2. 目前，仅可使用 Azure RemoteApp PowerShell cmdlet 启用应用程序模式。
   
   * 设置为应用程序模式时，不能通过 Azure 门户管理集合中的用户分配。 必须通过 PowerShell cmdlets 管理用户分配。
3. 用户只能看到直接发布给他们的应用程序。 但是，用户仍然可以通过在操作系统中直接访问其他可用的应用程序，在映像上启动它们。
   
   * 此功能不提供应用程序的安全锁定；它只是限制应用程序在应用程序源中的可见性。
   * 如果需要将用户从应用程序中隔离，需为此使用单独的集合。

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>如何获取 Azure RemoteApp PowerShell cmdlet
若要试用新的预览版功能，需要使用 Azure PowerShell cmdlet。 目前不能使用 Azure 管理门户启用新的应用程序发布模式。

首先，请确保已安装 [Azure PowerShell 模块](/powershell/azure/overview) 。

然后在管理员模式下启动 PowerShell 控制台并运行以下 cmdlet：

        Add-AzureAccount

系统将提示输入 Azure 用户名和密码。 登录后，便能对 Azure 订阅运行 Azure RemoteApp cmdlet。

## <a name="how-to-check-which-mode-a-collection-is-in"></a>如何检查集合所处模式
运行以下 cmdlet：

        Get-AzureRemoteAppCollection <collectionName>

![检查集合模式](./media/remoteapp-perapp/araacllelvel.png)

AclLevel 属性具有以下值：

* 集合：原始发布模式。 所有用户可看到所有已发布应用。
* 应用程序：新的发布模式。 用户只看到直接发布给他们的应用程序。

## <a name="how-to-switch-to-application-publishing-mode"></a>如何切换到应用程序发布模式
运行以下 cmdlet：

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

将保留应用程序发布状态：最初，所有用户都会看到原始发布的所有应用。

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>如何列出能够看到特定应用程序的用户
运行以下 cmdlet：

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

这将列出可以看到该应用程序的所有用户。

注意：可以通过运行 Get AzureRemoteAppProgram CollectionName <collectionName> 查看应用程序别名（在上面的语法中称为“app alias”）。

## <a name="how-to-assign-an-application-to-a-user"></a>如何将应用程序分配给用户
运行以下 cmdlet：

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

用户现在可在 Azure RemoteApp 客户端中查看应用程序，并进行连接。

## <a name="how-to-remove-an-application-from-a-user"></a>如何删除用户的应用程序
运行以下 cmdlet：

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>提供反馈
非常感谢你对此预览功能提出的反馈和建议。 请填写 [调查](http://www.instant.ly/s/FDdrb) ，让我们了解你的想法。

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>尚未有机会试用预览功能？
如果尚未加入预览试用，请使用此 [调查](http://www.instant.ly/s/AY83p) 请求访问权限。


