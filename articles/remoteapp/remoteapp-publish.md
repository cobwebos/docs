---
title: "在 Azure RemoteApp 中发布应用 | Microsoft Docs"
description: "了解如何在 Azure RemoteApp 中发布应用程序和资源。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: c7e1a2cd-8e1f-4a33-bd43-8032ec9ac952
ms.service: remoteapp
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 4cd4d35f44320ac57f015b5444985e8b4976ccf0
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-publish-an-app-in-remoteapp"></a>如何在 RemoteApp 中发布应用
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

创建 RemoteApp 集合之后，你需要发布你希望提供给用户的应用或资源。 随订阅提供的模板映像默认情况下仅会发布几个应用；要共享其他应用，你需要发布它们。

> [!NOTE]
> 你是否需要更新某个应用？ 首先，你将需要[更新映像](remoteapp-update.md)。
> 
> 

在门户中的“**发布**”选项卡上，单击“**发布**”。 你可以从模板映像的“**开始**”菜单添加应用，或者可以提供在模板映像上应用所安装到的路径。 如果你选择从“**开始**”菜单添加，请选择要从列表发布的应用。 如果你选择提供到应用的路径，请为输入应用的名称，并输入应用的路径。 在路径中使用变量 - 例如，使用 "%systemdrive%" 而不是 "c:\"。

> [!NOTE]
> 如果想要从“开始”菜单添加应用，则需要*将应用添加到模板映像上的“开始”菜单。* 否则，RemoteApp 将仅会看到“**开始**”菜单中的*内容*，你会感到困惑。 
> 
> 若要确保你的应用位于“**开始**”菜单中，请将快捷方式文件 **.lnk** -放置到 %systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs 文件夹内。
> 
> 如果你在创建模板时忘记将应用添加到“**开始**”菜单中，请选择将该路径添加到应用中。 （或者重新创建模板映像，但这会需要多做一些工作。）
> 
> 


