---
title: "Azure RemoteApp 授权 | Microsoft Docs"
description: "了解如何在 Azure RemoteApp 中进行授权。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: ff8ebd20-61a1-4f10-87a6-234a170534c9
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 43d0dbb905b2f2b9d98fb3bf8c073ba1c4b6f4c4
ms.lasthandoff: 03/31/2017


---
# <a name="how-does-licensing-work-in-azure-remoteapp"></a>如何在 Azure RemoteApp 中进行授权？
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

至此，你已经设置了 Azure RemoteApp 服务，创建了模板并准备好将应用发布给你的用户。 但还有最后一件事情要解决，那就是授权。 如何在 RemoteApp 和你通过 RemoteApp 共享的应用中进行授权？

RemoteApp 不需要任何 Windows 许可证或远程桌面 CAL。 你的订阅负责自身的 RemoteApp 端。 （查看[定价计划](https://azure.microsoft.com/pricing/details/remoteapp)的详细信息。）

如果你使用你订阅中包含的一个映像，则可共享安装在此映像上的任何应用，无需单独的许可证。 例如，如果你使用 Windows Server 2012 R2 模板映像生成集合，则可与你的用户共享 System Center Endpoint Protection。 此规则的唯一例外是 Office 365 ProPlus，它需要单独订阅，还有 Office 2013，它无法在生产集合中共享。

若要使用附带 RemoteApp 的 Office 365 模板映像，你必须拥有 *现有* 的 Office 365 ProPlus 计划。 这同样适用于你使用自定义模板发布的任何 Office 365 应用。 你需要通过自己订阅来激活应用。 这同样适用于试用版和付费订阅。 如果你要在试用期间使用 Office 365 模板映像， *但你还没有订阅*，请转到 Office 365 页面 [注册](https://go.microsoft.com/fwlink/p/?LinkID=403802) 试用版订阅。 有关详细信息，请参阅 [RemoteApp 和 Office 如何协作？](remoteapp-o365.md) 。

如果你在试用期间不想获取 Office 365 试用版订阅，请使用附带 RemoteApp 的 Office 2013 Professional Plus 模板映像。 此模板映像仅可使用 30 天，并且无法转换为付费集合。

对于其他应用，你需要确保拥有可共享应用的许可证。

这种说法很合理，对吗？ 你可以发布你有合法权利共享的任何应用。 并且你需要确保自己确实有权共享自己的程序。

请注意，你不能在云集合中使用 CAL 或批量许可协议。 你 *可以* 使用批量许可协议来激活你混合集合中的应用程序（Office 除外）。 只需将其从批量许可介质安装到你的模板映像上。 按照应用程序供应商提供的信息在远程桌面环境中安装许可证。


